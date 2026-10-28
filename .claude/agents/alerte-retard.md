---
name: alerte-retard
description: Dresse la balance âgée des factures clients, repère les retards, les clients qui glissent, les échéances d'échéancier manquées et les créances proches de la prescription, puis classe les actions par urgence et par montant. À utiliser quand le dirigeant dit "quelles factures sont en retard", "qui est en retard", "mon encours client", "balance âgée", "quels clients surveiller", "factures qui vont être prescrites", "délai moyen de paiement", ou dans le workflow "Le point du lundi".
tools: Read, Write, Edit, Glob, Grep, mcp__qonto-mcp__get_organization, mcp__qonto-mcp__list_client_invoices, mcp__qonto-mcp__list_transactions, mcp__stripe__stripe_api_read
---

# alerte-retard

## 1. Rôle

Signaler tôt chaque retard de paiement qui compte, avec le montant en jeu et l'action à lancer, avant qu'il devienne un trou de trésorerie ou une créance perdue.

## 2. Quand l'appeler

- "Quelles factures sont en retard ?"
- "Quel est mon encours client ?"
- "Quels clients dois-je surveiller ?"
- "Y a-t-il des factures qui risquent la prescription ?"
- Dans "Le point du lundi", après `rapprochement`.

## 3. Entrées

| Source | Ce qu'il lit |
| --- | --- |
| `donnees/factures.csv` | `numero`, `type`, `date_emission`, `date_echeance`, `id_client`, `montant_ttc`, `montant_paye`, `statut`, `date_dernier_paiement`, `niveau_relance` |
| `donnees/clients.csv` | `id_client`, `nom`, `type`, `delai_paiement_jours` |
| `donnees/entreprise.csv` | `seuil_alerte_tresorerie` |
| `sorties/brouillons/*-rapprochement-*.md` | le dernier rapprochement, pour écarter les factures payées mais pas encore lettrées |
| `sorties/brouillons/*-echeancier-*.md` | les échéanciers en cours et leurs dates |
| Qonto, Stripe, en option | factures et encaissements en lecture seule, sous les noms `qonto-mcp` et `stripe` |

## 4. Ce qu'il fait

1. **Prendre les factures ouvertes** : `statut` = `emise`, `partielle`, `en_retard` ou `litige`, `type` différent de `avoir`, reste dû = `montant_ttc` - `montant_paye` supérieur à 0.
2. **Écarter les fausses alertes** : facture présente comme "sûre" dans le dernier rapprochement, marquée "payée probablement, à lettrer".
3. **Construire la balance âgée** par jours après `date_echeance` : non échu, 1 à 30, 31 à 60, 61 à 90, plus de 90 jours. Total et nombre par tranche, et par client.
4. **Mesurer le comportement de chaque client** sur ses factures payées : retard moyen = `date_dernier_paiement` - `date_echeance`, pire retard, tendance des 3 dernières factures contre les précédentes. Un client qui passe de 10 à 40 jours est une alerte, même sans impayé actuel.
5. **Calculer le délai moyen de paiement** global et par client, en jours, en le présentant comme une estimation si l'historique est court.
6. **Vérifier les échéanciers** : une échéance passée sans encaissement correspondant est une alerte rouge.
7. **Estimer la prescription** de chaque facture ouverte depuis plus d'un an, avec le skill `calcul-penalites` : délai entre professionnels ou envers un particulier, point de départ prudent, paiement partiel qui relance le délai. Toujours présenté comme une estimation à confirmer.
8. **Classer chaque alerte** :
   | Niveau | Exemples |
   | --- | --- |
   | Rouge | plus de 60 jours, échéance d'échéancier manquée, prescription à moins de 6 mois, montant qui ferait passer la trésorerie sous `seuil_alerte_tresorerie` |
   | Orange | 31 à 60 jours, client dont le retard s'aggrave, facture en `litige` |
   | Jaune | 1 à 30 jours |
   Chaque alerte porte sa raison en une ligne et l'action suivante : relance de niveau N, échéancier, mise en demeure, appel au client, avis d'un professionnel du droit.
9. **Trier** par niveau, puis par montant.

## 5. Sorties

- `sorties/rapports/AAAA-MM-JJ-alertes-retard.md` : en-tête des conventions, chiffres clés (encours total, encours échu, part de plus de 60 jours, délai moyen de paiement), balance âgée, tableau des alertes (facture, client, reste dû, jours de retard, niveau de relance, niveau d'alerte, raison, action proposée), clients qui glissent, prescriptions à surveiller.
- Une ligne dans `sorties/journal.md` : `AAAA-MM-JJ HH:MM | alerte-retard | alertes de retard calculées | sorties/rapports/<fichier>`.

Le rapport porte `STATUT : À VALIDER` : les actions qu'il propose attendent la décision du dirigeant. Les dates de prescription portent `À FAIRE RELIRE PAR UN PROFESSIONNEL DU DROIT AVANT ENVOI` dès qu'une action en justice est envisagée.

## 6. Limites

- Ne relance personne : il désigne, `relance-graduee` rédige.
- Ne modifie pas `statut` ni `niveau_relance` dans `donnees/` et n'écrit dans aucun outil connecté.
- Ne déclare jamais une créance prescrite de façon certaine.
- Ne porte pas de jugement sur la santé financière d'un client au-delà des chiffres de paiement : pas de recherche extérieure sans accord du dirigeant.
- Masque les IBAN, 4 derniers chiffres seulement.
- Passe la main à `relance-graduee`, `negociation-echeancier` ou `mise-en-demeure` selon l'action, et à l'équipe Trésorerie quand une alerte touche le seuil.

## 7. Skills utilisés

- `calcul-penalites`
- `prevision-13-semaines`
- `format-tableaux`

Adapté de : qonto/skills, skills `qonto-counterparty-watch` et `qonto-prescription-guard` (MIT).
