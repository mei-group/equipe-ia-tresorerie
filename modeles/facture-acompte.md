STATUT : À VALIDER
Agent : {{agent}}
Données utilisées : {{donnees_utilisees}}
Points à vérifier : {{points_a_verifier}}

---

# FACTURE D'ACOMPTE N° {{numero_facture}}

**Date d'émission :** {{date_emission}}
**Date d'échéance :** {{date_echeance}}
**Devis accepté :** n° {{numero_devis}} du {{date_devis}}, accepté le {{date_acceptation}}
**Catégorie de l'opération :** {{categorie_operation}}

## Vendeur

{{raison_sociale}}, {{forme_juridique}} au capital de {{capital}}
{{adresse}}, {{code_postal}} {{ville}}
SIREN : {{siren}}, RCS {{rcs_ville}}
N° de TVA intracommunautaire : {{tva_intracom}}

## Client

{{client_nom}}
{{client_adresse}}, {{client_code_postal}} {{client_ville}}
SIREN : {{client_siren}}
N° de TVA intracommunautaire : {{client_tva_intracom}}

## Détail

| Désignation | Quantité | Prix unitaire HT | Taux TVA | Total HT |
| --- | --- | --- | --- | --- |
| Acompte de {{acompte_pourcentage}} sur le devis n° {{numero_devis}} : {{objet_devis}} | 1 | {{montant_ht}} | {{taux_tva}} | {{montant_ht}} |

| | Montant |
| --- | --- |
| Montant total du devis HT | {{total_devis_ht}} |
| **Acompte HT** | **{{montant_ht}}** |
| TVA à {{taux_tva}} | {{montant_tva}} |
| **Acompte TTC** | **{{montant_ttc}}** |

Cet acompte sera déduit de la facture de solde.

{{mention_tva_particuliere}}

## Paiement

- À régler au plus tard le **{{date_echeance}}**.
- IBAN : {{iban}}, BIC : {{bic}}. Référence : {{numero_facture}}.

## Conditions

- Escompte pour paiement anticipé : {{conditions_escompte}}.
- Pénalités de retard : {{taux_penalites_retard}}.
- Client professionnel : indemnité forfaitaire pour frais de recouvrement de {{indemnite_forfaitaire_recouvrement}}.
