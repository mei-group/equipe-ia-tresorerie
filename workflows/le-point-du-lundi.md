# Workflow : le point du lundi

En 10 minutes : ce qui est rentré, qui relancer, et si la trésorerie tient.

## Phrases qui le déclenchent

- "Le point du lundi"
- "On fait le point"
- "Où on en est cette semaine ?"
- Commande : `/point-du-lundi`

## Entrées

- `donnees/banque.csv` : les mouvements des 7 derniers jours au moins.
- `donnees/factures.csv` : factures `emise`, `partielle`, `en_retard`.
- `donnees/clients.csv` : délais et contacts.
- `donnees/entreprise.csv` : `seuil_alerte_tresorerie`.
- `donnees/depenses.csv` : sorties prévues.
- `donnees/devis.csv` : devis envoyés sans réponse, devis acceptés sans facture.
- Option : Qonto ou Stripe en lecture pour les encaissements récents.

`banque.csv` pas à jour : dis la date du dernier mouvement. Demande un relevé plus récent ou continue en le signalant.

## Étapes

1. **`rapprochement`** associe les encaissements de la semaine aux factures. Il liste les paiements sans facture et les écarts de montant.
2. **`lettrage-paiements`** propose les factures à marquer `payee` ou `partielle`.
3. **`alerte-retard`** liste les factures échues non payées, triées par montant et par ancienneté.
4. **`relance-graduee`** prépare les relances du bon niveau, une par facture, avec le skill `ton-des-relances`.
5. **`previsionnel-13-semaines`** calcule le solde projeté, puis **`alerte-tresorerie`** le compare au seuil sur les 4 prochaines semaines. Le point du lundi ne refait jamais son propre calcul : il reprend ce prévisionnel, pour que tous les workflows donnent la même semaine critique.
6. **`suivi-devis`** liste les devis envoyés sans réponse depuis plus de 15 jours, et les devis acceptés sans facture.
7. Le chef liste les factures au statut `brouillon` qui attendent une validation.
8. **Point de validation.** Le chef présente une page :
   - encaissé cette semaine ;
   - factures à marquer payées ;
   - relances prêtes, avec leur niveau ;
   - alerte trésorerie, oui ou non, et la semaine concernée ;
   - devis à relancer, devis acceptés à facturer ;
   - factures en brouillon qui attendent sa validation.
9. Le dirigeant valide document par document. Le chef met à jour `donnees/factures.csv` seulement pour le lettrage validé.

## Sorties

- `sorties/rapports/AAAA-MM-JJ-point-du-lundi.md` : la synthèse d'une page.
- `sorties/rapports/AAAA-MM-JJ-rapprochement.md`
- `sorties/brouillons/AAAA-MM-JJ-relance-<numero>.md` : une par facture.
- Lignes dans `sorties/journal.md`

## Points de validation

- Aucune relance n'est envoyée par l'équipe.
- Une relance de niveau 3 n'est jamais préparée ici. Elle passe par le workflow "Un client ne paie pas".
- Un paiement sans facture n'est jamais affecté au hasard. Il est signalé.
