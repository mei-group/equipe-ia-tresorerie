STATUT : À VALIDER
Agent : {{agent}}
Données utilisées : {{donnees_utilisees}}
Points à vérifier : {{points_a_verifier}}

---

# FACTURE N° {{numero_facture}}

**Date d'émission :** {{date_emission}}
**Date de la vente ou de la prestation :** {{date_prestation}}
**Date d'échéance :** {{date_echeance}}
**Catégorie de l'opération :** {{categorie_operation}}
**Référence devis ou bon de commande :** {{reference_devis}}

## Vendeur

{{raison_sociale}}, {{forme_juridique}} au capital de {{capital}}
{{adresse}}, {{code_postal}} {{ville}}
SIREN : {{siren}}, RCS {{rcs_ville}}
N° de TVA intracommunautaire : {{tva_intracom}}
{{email}}, {{telephone}}

## Client

{{client_nom}}
{{client_adresse}}, {{client_code_postal}} {{client_ville}}
SIREN : {{client_siren}}
N° de TVA intracommunautaire : {{client_tva_intracom}}
Adresse de livraison, si différente : {{adresse_livraison}}

## Détail

| Désignation | Quantité | Unité | Prix unitaire HT | Taux TVA | Total HT |
| --- | --- | --- | --- | --- | --- |
| {{designation_1}} | {{quantite_1}} | {{unite_1}} | {{prix_unitaire_ht_1}} | {{taux_tva_1}} | {{total_ht_1}} |
| {{designation_2}} | {{quantite_2}} | {{unite_2}} | {{prix_unitaire_ht_2}} | {{taux_tva_2}} | {{total_ht_2}} |
| {{designation_3}} | {{quantite_3}} | {{unite_3}} | {{prix_unitaire_ht_3}} | {{taux_tva_3}} | {{total_ht_3}} |

Répéter une ligne par produit ou service.

| | Montant |
| --- | --- |
| Total HT | {{total_ht}} |
| Rabais, remises, ristournes | {{reductions}} |
| TVA à {{taux_tva}} | {{montant_tva}} |
| **Total TTC** | **{{total_ttc}}** |
| Acomptes déjà facturés et réglés | {{acomptes_deduits}} |
| **Net à payer** | **{{net_a_payer}}** |

{{mention_tva_particuliere}}

## Paiement

- À régler au plus tard le **{{date_echeance}}**.
- Moyens de paiement : {{moyens_paiement}}.
- IBAN : {{iban}}, BIC : {{bic}}.
- Référence à indiquer avec le paiement : {{numero_facture}}.

## Conditions

- Escompte pour paiement anticipé : {{conditions_escompte}}.
- Pénalités de retard : {{taux_penalites_retard}}, exigibles dès le lendemain de la date d'échéance.
- Client professionnel : indemnité forfaitaire pour frais de recouvrement de {{indemnite_forfaitaire_recouvrement}}, due de plein droit en cas de retard de paiement.
- {{mention_option_tva_debits}}
