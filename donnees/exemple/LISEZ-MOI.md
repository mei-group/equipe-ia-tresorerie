# Le jeu de données d'exemple

**Toutes ces données sont fictives.** L'entreprise, les clients, les fournisseurs, les numéros SIREN et de TVA, l'IBAN, les adresses et les courriels sont inventés. Les SIREN commencent par `000000`, l'IBAN est `FR00 0000...`, les courriels finissent en `example.com`. Aucun ne correspond à une personne ou une entreprise réelle.

L'entreprise : **Atelier Fictif SAS**, un atelier d'agencement bois de 4 salariés. Date de référence du jeu : **28 octobre 2026**. Factures émises d'août à octobre 2026, relevé bancaire du 1er août au 28 octobre 2026.

## Les fichiers

| Fichier | Lignes | Contenu |
| --- | --- | --- |
| `entreprise.csv` | 1 | Identité, IBAN, délai par défaut de 30 jours, seuil d'alerte à 15 000 € |
| `clients.csv` | 20 | 12 professionnels, 8 particuliers |
| `devis.csv` | 35 | Tous les statuts : brouillon, envoyé, accepté, refusé, expiré |
| `factures.csv` | 60 | 50 factures, 9 acomptes, 1 avoir, de F2026-0101 à F2026-0159 |
| `banque.csv` | 111 | Solde d'ouverture, encaissements, fournisseurs, salaires, cotisations, TVA |
| `depenses.csv` | 41 | Factures fournisseurs payées, à payer, sans justificatif |
| `abonnements.csv` | 12 | Mensuels et annuels, utilisés ou non |

Format : celui de `CONVENTIONS.md`. Deux précisions :

- `taux_tva` est un pourcentage : `20.00` veut dire 20 %.
- L'avoir a des montants négatifs. Il porte sur la facture F2026-0124 et a été remboursé par virement.

## Les histoires à trouver

Ce que l'équipe doit découvrir seule pendant la démo.

1. **Un client paie toujours en retard.** Bâtiments Imaginaires SARL, C003, règle 40 à 55 jours après l'échéance. Il doit environ 27 350 € TTC sur 7 factures : relance de niveau 3 sur F2026-0105, paiement partiel sur F2026-0117, grosse facture F2026-0130 échue. Workflow "Un client ne paie pas".
2. **Un creux de trésorerie mi-novembre.** Solde au 28 octobre : 23 708,81 €. Arrivent ensuite les salaires du 30 octobre, une sous-traitance de 8 640 € le 13 novembre, les panneaux le 14, les cotisations et la licence CAO annuelle le 15, la TVA vers le 21. Si C003 ne paie pas, le solde passe sous le seuil de 15 000 € vers le 14 novembre et frôle zéro fin novembre. Workflow "J'ai un trou de trésorerie".
3. **Un signal déjà passé.** Début septembre, le solde est tombé à 6 697,87 €, sous le seuil, avant les encaissements d'août.
4. **Deux abonnements inutilisés.** Planning Chantier Fictif et Banque d'Images Démo, marqués `non`. Deux autres sont `inconnu` et méritent une question.
5. **Des justificatifs manquants.** 5 dépenses `sans_justificatif`, payées en banque : carburant en août et en octobre, fournitures, un repas, de l'outillage pour 1 032 €.
6. **Des devis sans réponse.** D2026-020, D2026-021, D2026-024 et D2026-026, envoyés entre le 15 septembre et le 2 octobre, toujours sans réponse. D2026-024 pèse 13 200 € HT.
7. **Des devis acceptés pas encore facturés.** D2026-030 et D2026-032 n'ont aucune facture. Quatre autres n'ont que leur acompte.
8. **Un litige.** Garage Fictif Auto, C008, conteste la finition : F2026-0147, 5 580 € TTC, rien de payé.
9. **Des paiements partiels.** F2026-0125, F2026-0136, F2026-0139 et F2026-0117 sont payées à moitié ou moins.
10. **Une facture en brouillon.** F2026-0159, datée du 28 octobre, attend la validation du dirigeant.

## La vérification

La cohérence a été contrôlée par un script le 28 octobre 2026 :

- HT + TVA = TTC sur chaque devis, facture et dépense.
- Chaque `montant_paye` égale la somme des lignes bancaires portant le numéro de la facture.
- `date_dernier_paiement` correspond à la dernière de ces lignes.
- Statuts cohérents avec l'échéance au 28 octobre et le montant payé.
- `niveau_relance` supérieur à 0 seulement sur une facture en retard ou en litige.
- Factures liées à un devis accepté, sans dépasser son montant.
- Numérotation des factures continue.
- Chaque dépense payée a sa ligne bancaire, aucune dépense à payer n'en a.
