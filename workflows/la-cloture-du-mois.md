# Workflow : la clôture du mois

Un mois propre, un rapport lisible, un dossier prêt pour l'expert-comptable.

## Phrases qui le déclenchent

- "La clôture du mois"
- "On clôture octobre"
- "Prépare le mois pour le comptable"
- Commande : `/cloture-du-mois 2026-10`

Mois non précisé : le mois civil précédent. Le chef l'annonce avant de lancer.

## Entrées

- Tous les fichiers de `donnees/` pour le mois visé.
- Les justificatifs cités dans `donnees/depenses.csv`, colonne `justificatif`.
- `sorties/journal.md` : les validations du mois.
- `modeles/` : rapport mensuel et dossier de l'expert-comptable.
- Option : Qonto ou Stripe en lecture, Google Drive ou OneDrive pour les justificatifs.

## Étapes

1. **`rapprochement`** vérifie que chaque mouvement bancaire du mois est rattaché à une facture ou une dépense.
2. **`notes-de-frais`** liste les dépenses sans justificatif.
3. **`controle-coherence`** cherche les doublons, les trous de numérotation, les écarts entre factures, banque et dépenses.
4. **Point de validation 1.** Le chef présente les anomalies. Le dirigeant corrige ou explique. Une anomalie non résolue reste dans le dossier, signalée.
5. **`rapport-mensuel`** écrit le rapport : chiffre d'affaires facturé et encaissé, encours, délai moyen de paiement, dépenses, solde, faits marquants.
6. **`dossier-mensuel`** assemble le dossier de l'expert-comptable : listes des ventes, achats, encaissements, justificatifs manquants, anomalies.
7. **`questions-comptable`** liste les questions à poser, une par point douteux.
8. **Point de validation 2.** Le chef présente rapport, dossier et questions. Le dirigeant valide chacun.

## Sorties

- `sorties/rapports/AAAA-MM-rapport-mensuel.md`
- `sorties/comptable/AAAA-MM-dossier-mensuel.md` et ses tableaux
- `sorties/comptable/AAAA-MM-questions-comptable.md`
- `sorties/brouillons/AAAA-MM-JJ-courriel-comptable.md` : le message d'accompagnement
- Lignes dans `sorties/journal.md`

## Points de validation

- Le dossier et les questions portent `À VALIDER PAR L'EXPERT-COMPTABLE`.
- L'équipe ne passe aucune écriture comptable et ne calcule aucune déclaration de TVA.
- Le dirigeant envoie lui-même le dossier à son expert-comptable.
