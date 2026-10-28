# Workflow : un client ne paie pas

Comprendre le retard, relancer au bon ton, puis proposer un échéancier ou préparer une mise en demeure.

## Phrases qui le déclenchent

- "Un client ne paie pas"
- "Bâtiments Imaginaires SARL ne m'a toujours pas payé"
- "La facture F2026-0105 est impayée"
- Commande : `/client-ne-paie-pas Bâtiments Imaginaires SARL`

Client ou facture non précisé : le chef demande lequel. Il ne choisit pas seul.

## Entrées

- `donnees/factures.csv` : les factures du client, dont `niveau_relance`.
- `donnees/clients.csv` : type professionnel ou particulier, délai, contact.
- `donnees/banque.csv` : pour vérifier qu'il n'a pas déjà payé.
- `sorties/brouillons/` et `sorties/journal.md` : les relances déjà faites.
- Option : Qonto ou Stripe en lecture, Gmail ou Microsoft 365 en lecture pour l'historique des échanges.

## Étapes

1. **`rapprochement`** vérifie dans la banque que la facture n'est pas déjà payée, même partiellement.
   Payée : le workflow s'arrête et passe à `lettrage-paiements`.
2. **`analyse-clients`** dresse l'historique : retards passés, montant dû, ancienneté, dernière relance, litige éventuel.
3. **`relance-graduee`** prépare la relance du niveau suivant, avec `ton-des-relances` et `calcul-penalites`.
4. **Point de validation 1.** Le chef présente l'historique et la relance. Le dirigeant choisit la suite :
   - **relancer** : il valide la relance et l'envoie lui-même ;
   - **le client propose de payer en plusieurs fois** : étape 5 ;
   - **les relances n'ont rien donné** : étape 6.
5. **`negociation-echeancier`** prépare une proposition d'échéancier : montants, dates, conséquence d'un défaut. Il met à jour le prévisionnel en hypothèse.
6. **`mise-en-demeure`** prépare le brouillon, avec pénalités et indemnité calculées par `calcul-penalites`.
7. **Point de validation 2.** Le chef présente le document. Il rappelle les mentions obligatoires de relecture.

## Sorties

- `sorties/rapports/AAAA-MM-JJ-historique-<id_client>.md`
- `sorties/brouillons/AAAA-MM-JJ-relance-<numero>.md`
- `sorties/brouillons/AAAA-MM-JJ-echeancier-<numero>.md`, si choisi
- `sorties/brouillons/AAAA-MM-JJ-mise-en-demeure-<numero>.md`, si choisi
- Lignes dans `sorties/journal.md`

## Points de validation

- La mise en demeure porte `À FAIRE RELIRE PAR UN PROFESSIONNEL DU DROIT AVANT ENVOI`.
- Les pénalités et l'indemnité de 40 € portent `À VALIDER PAR L'EXPERT-COMPTABLE`. L'indemnité ne vise que les clients professionnels.
- Facture en `litige` : pas de relance automatique. Le chef demande d'abord au dirigeant.
- L'équipe ne saisit aucun tribunal et ne contacte aucun recouvreur.
