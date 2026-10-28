# Workflow : le devis est accepté

Du devis signé à la facture prête, puis au prévisionnel à jour.

## Phrases qui le déclenchent

- "Le devis est accepté"
- "Le devis D2026-020 est signé"
- "Il a dit oui pour le devis"
- Commande : `/devis-accepte D2026-020`

## Entrées

- `donnees/devis.csv` : le devis accepté, par son numéro.
- `donnees/clients.csv` : le client du devis.
- `donnees/entreprise.csv` : mentions du vendeur, délai de paiement par défaut.
- `donnees/factures.csv` : dernier numéro de facture, acomptes déjà émis.
- `modeles/` : le modèle de facture.
- Option : Qonto ou Stripe en lecture, pour vérifier un acompte déjà reçu.

Numéro absent ou ambigu : demande le numéro du devis. Ne devine pas.

## Étapes

1. **Le chef** vérifie le devis : il existe, son statut est `envoye` ou `accepte`, sa date de validité.
   Devis expiré : signale-le et demande si le client a bien accepté.
2. **`createur-facture`** prépare la facture depuis le devis.
   Facture d'acompte ou de situation demandée : passe la main à `acomptes-situations`.
3. **`controle-mentions`** relit la facture : mentions obligatoires, TVA, numérotation, échéance, pénalités et indemnité de 40 € pour un client professionnel.
   Écart trouvé : retour à l'étape 2, une seule fois. Écart persistant : noté dans `Points à vérifier`.
4. **Point de validation 1.** Le chef présente la facture et le contrôle. Il attend "je valide".
5. Après validation seulement : le chef propose la mise à jour de `donnees/devis.csv` en `accepte` et l'ajout de la facture dans `donnees/factures.csv`, statut `emise`. Il écrit ces lignes sur accord explicite.
6. **`previsionnel-13-semaines`** ajoute l'encaissement attendu à la date d'échéance, selon le délai réel de ce client s'il est connu.
7. Le chef résume : montant TTC, échéance, effet sur la trésorerie.

## Sorties

- `sorties/brouillons/AAAA-MM-JJ-facture-<numero>.md`
- `sorties/brouillons/AAAA-MM-JJ-controle-mentions-<numero>.md`
- `sorties/rapports/AAAA-MM-JJ-previsionnel-13-semaines.md`
- Lignes dans `sorties/journal.md`

## Points de validation

- La facture n'est jamais envoyée par l'équipe. Le dirigeant l'envoie.
- Les fichiers de `donnees/` changent seulement après "je valide".
- Régime de TVA particulier, autoliquidation, client public ou étranger : mention `À VALIDER PAR L'EXPERT-COMPTABLE`.
