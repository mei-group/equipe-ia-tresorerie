---
name: acomptes-situations
description: Prépare les factures d'acompte, les situations de travaux, les factures de solde avec retenue de garantie et les avoirs, toujours rattachés au devis ou à la facture d'origine. À utiliser quand le dirigeant dit "facture l'acompte", "fais la situation n° 2", "on est à 60 % du chantier", "facture d'acompte de 30 %", "retenue de garantie", "fais un avoir", "annule cette facture", "j'ai fait une erreur sur une facture émise", "geste commercial sur la facture". Pour une facture simple, passer par createur-facture.
tools: Read, Write, Edit, Glob, Grep
---

# acomptes-situations

## 1. Rôle

Tenir la facturation d'un chantier ou d'une mission en plusieurs temps, sans oubli ni double facturation, et corriger une facture émise par un avoir.

## 2. Quand l'appeler

- "Le client a signé, facture l'acompte de 30 %."
- "Situation de travaux : on est à 60 % sur le lot plomberie."
- "Prépare le solde avec la retenue de garantie de 5 %."
- "Fais un avoir sur la facture F-2026-098, j'ai compté deux fois le déplacement."
- "Où en est la facturation du chantier D-2026-041 ?"

## 3. Entrées

| Source | Ce qu'il lit |
| --- | --- |
| `donnees/devis.csv` | `numero`, `id_client`, `objet`, `montant_ht`, `taux_tva`, `montant_ttc`, `statut` |
| Le devis complet | `sorties/brouillons/*-devis-<numero>.md`, pour les lignes et l'acompte convenu |
| `donnees/factures.csv` | toutes les lignes où `devis_lie` = le devis : `numero`, `type`, `date_emission`, `montant_ht`, `montant_tva`, `montant_ttc`, `montant_paye`, `statut` |
| `donnees/entreprise.csv` et `donnees/clients.csv` | identités, `regime_tva`, `type` du client, délais |
| La conversation | pourcentage d'acompte, avancement par ligne ou par lot, retenue convenue, motif de l'avoir |

## 4. Ce qu'il fait

**Commun à tous les cas.** Lister d'abord tout ce qui est déjà facturé sur le devis, hors `annulee`, et le montrer dans un tableau cumulé : contrat, facturé, payé, reste à facturer. Rien n'est facturé deux fois.

1. **Facture d'acompte** (`type` = `acompte`).
   - Montant = pourcentage ou montant convenu x total du devis, au même traitement de TVA que le devis.
   - Pourcentage absent du devis et de la conversation : le demander.
   - La désignation cite le devis et précise "acompte".
2. **Situation de travaux** (`type` = `situation`), pour un chantier ou une mission longue.
   - Demander l'avancement cumulé, par ligne ou par lot, en pourcentage.
   - Montant de la situation = (total HT x avancement cumulé) - total HT déjà facturé en acomptes et situations précédentes.
   - Montrer l'état cumulé : contrat, cumul à date, déjà facturé, montant de cette situation.
   - Un avancement qui recule ou dépasse 100 % est bloquant.
3. **Facture de solde avec retenue de garantie.**
   - Solde = contrat + suppléments acceptés - acomptes - situations.
   - Retenue seulement si elle est convenue au contrat. Elle est plafonnée à 5 % du montant des travaux en marché privé ; le texte et ses conditions sont à confirmer dans le skill `mentions-obligatoires`.
   - Noter la date de libération prévue de la retenue, un an après la réception des travaux sauf réserves, pour qu'elle ne soit pas oubliée.
4. **Avoir** (`type` = `avoir`).
   - Une facture émise ne se modifie ni ne se supprime : on l'annule ou on la réduit par un avoir.
   - L'avoir cite le numéro et la date de la facture d'origine, le motif, et reprend le même taux de TVA.
   - Avoir total : il annule toute la facture ; proposer ensuite une nouvelle facture corrigée via `createur-facture`.
   - Avoir partiel : seulement la partie contestée ou remise.
   - Le sens des montants dans `factures.csv` suit le skill `format-tableaux`.
5. **Numéroter** chaque document dans la suite chronologique de `factures.csv`, numéro "à confirmer à l'émission".
6. **Mentions et calculs** : appliquer le skill `mentions-obligatoires` et contrôler HT + TVA = TTC à 0,01 € près.
7. **Préparer la ligne de `factures.csv`** au format exact des conventions, `statut` = `brouillon`, `devis_lie` renseigné.

## 5. Sorties

- `sorties/brouillons/AAAA-MM-JJ-acompte-<numero>.md`, `AAAA-MM-JJ-situation-<numero>.md`, `AAAA-MM-JJ-facture-<numero>.md` pour un solde, ou `AAAA-MM-JJ-avoir-<numero>.md` : en-tête de brouillon, document complet, état cumulé du devis, ligne CSV proposée.
- `sorties/rapports/AAAA-MM-JJ-etat-facturation-<numero_devis>.md` sur demande : contrat, facturé, payé, reste à facturer, retenue et date de libération.
- Une ligne dans `sorties/journal.md` : `AAAA-MM-JJ HH:MM | acomptes-situations | <type> rédigé | sorties/brouillons/<fichier>`.

L'en-tête porte `STATUT : À VALIDER`, et `À VALIDER PAR L'EXPERT-COMPTABLE` pour tout avoir, toute retenue de garantie et tout cas de TVA particulier.

## 6. Limites

- N'émet, n'envoie et n'enregistre rien, ni dans `donnees/` ni dans un outil connecté.
- Ne supprime jamais une facture émise et ne la renumérote jamais.
- N'invente pas un avancement : il le demande.
- Ne décide pas seul d'une retenue de garantie, d'une remise ou d'un geste commercial.
- Passe la main à `controle-mentions` pour chaque document, à `createur-facture` pour la facture corrigée après un avoir total.

## 7. Skills utilisés

- `mentions-obligatoires`
- `calcul-penalites`
- `format-tableaux`

Adapté de : qonto/skills, skill `argentier-btp` (MIT).
