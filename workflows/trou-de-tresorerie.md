# Workflow : j'ai un trou de trésorerie

Voir quand l'argent manque, de combien, et quoi faire en premier.

## Phrases qui le déclenchent

- "J'ai un trou de trésorerie"
- "Je vais manquer d'argent"
- "Est-ce que je peux payer les salaires ?"
- Commande : `/trou-de-tresorerie`

## Entrées

- `donnees/banque.csv` : solde de départ et mouvements récents.
- `donnees/factures.csv` : encaissements attendus.
- `donnees/devis.csv` : devis acceptés pas encore facturés.
- `donnees/depenses.csv` et `donnees/abonnements.csv` : sorties prévues.
- `donnees/entreprise.csv` : `seuil_alerte_tresorerie`.
- Option : Qonto ou Stripe en lecture pour le solde du jour.

Solde de départ inconnu : le chef le demande. Il ne le suppose jamais.
Charges fixes absentes, salaires, loyer, échéances d'emprunt : il les demande, ou les écrit comme hypothèses.

## Étapes

1. **`previsionnel-13-semaines`** construit le prévisionnel semaine par semaine, avec le skill `prevision-13-semaines`.
2. **`scenarios`** calcule un scénario prudent et un optimiste. Il nomme les hypothèses qui changent.
3. **`alerte-tresorerie`** donne la première semaine sous le seuil et le montant manquant, pour chaque scénario.
4. **`alerte-retard`** classe les factures échues ou proches par effet sur le trou : montant, probabilité, délai.
5. **`relance-graduee`** prépare les relances des factures en tête de liste.
6. **`factures-fournisseurs`** et **`abonnements`** listent ce qui peut être décalé ou arrêté, sans rien payer ni résilier.
7. **Point de validation.** Le chef présente une page :
   - la semaine critique et le montant ;
   - les 3 actions qui rapportent le plus vite ;
   - les relances prêtes ;
   - les dépenses qu'on peut décaler, à négocier par le dirigeant.

## Sorties

- `sorties/rapports/AAAA-MM-JJ-previsionnel-13-semaines.md`
- `sorties/rapports/AAAA-MM-JJ-scenarios.md`
- `sorties/rapports/AAAA-MM-JJ-plan-trou-de-tresorerie.md`
- `sorties/brouillons/AAAA-MM-JJ-relance-<numero>.md`
- Lignes dans `sorties/journal.md`

## Points de validation

- Aucun paiement, virement ni résiliation par l'équipe.
- Aucun conseil de financement engageant. Prêt, affacturage, report URSSAF ou fiscal : l'équipe cite la piste, le dirigeant voit avec son expert-comptable ou sa banque.
- Les hypothèses sont écrites en clair dans chaque rapport.
