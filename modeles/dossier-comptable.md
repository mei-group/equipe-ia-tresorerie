STATUT : À VALIDER
Agent : dossier-mensuel
Données utilisées : {{donnees_utilisees}}
Points à vérifier : {{points_a_verifier}}

**À VALIDER PAR L'EXPERT-COMPTABLE**

---

# Dossier comptable : {{mois}} {{annee}}

**Pour :** {{expert_comptable_nom}}, {{expert_comptable_cabinet}}
**De :** {{raison_sociale}}, SIREN {{siren}}
**Préparé le :** {{date_dossier}}
**Période :** du {{date_debut}} au {{date_fin}}

## 1. Contenu du dossier

| Élément | Fichier | Nombre de lignes |
| --- | --- | --- |
| Journal des ventes | {{fichier_ventes}} | {{nb_ventes}} |
| Journal des achats | {{fichier_achats}} | {{nb_achats}} |
| Relevé bancaire | {{fichier_banque}} | {{nb_lignes_banque}} |
| Justificatifs | {{dossier_justificatifs}} | {{nb_justificatifs}} |

## 2. Ventes

| | HT | TVA | TTC |
| --- | --- | --- | --- |
| Factures | {{ventes_ht}} | {{ventes_tva}} | {{ventes_ttc}} |
| Factures d'acompte | {{acomptes_ht}} | {{acomptes_tva}} | {{acomptes_ttc}} |
| Avoirs | {{avoirs_ht}} | {{avoirs_tva}} | {{avoirs_ttc}} |
| **Total** | **{{total_ventes_ht}}** | **{{total_ventes_tva}}** | **{{total_ventes_ttc}}** |

TVA collectée par taux :

| Taux | Base HT | TVA |
| --- | --- | --- |
| {{taux_tva_1}} | {{base_ht_taux_1}} | {{tva_taux_1}} |
| {{taux_tva_2}} | {{base_ht_taux_2}} | {{tva_taux_2}} |

Numérotation des factures : de {{premier_numero}} à {{dernier_numero}}. Continuité : {{continuite_numerotation}}.

## 3. Achats et dépenses

| Catégorie | HT | TVA | TTC |
| --- | --- | --- | --- |
| {{categorie_achat_1}} | {{achat_ht_1}} | {{achat_tva_1}} | {{achat_ttc_1}} |
| {{categorie_achat_2}} | {{achat_ht_2}} | {{achat_tva_2}} | {{achat_ttc_2}} |
| **Total** | **{{total_achats_ht}}** | **{{total_achats_tva}}** | **{{total_achats_ttc}}** |

## 4. Banque

| | Montant |
| --- | --- |
| Solde au {{date_debut}} | {{solde_debut}} |
| Total des encaissements | {{total_encaissements}} |
| Total des décaissements | {{total_decaissements}} |
| Solde au {{date_fin}} | {{solde_fin}} |
| Lignes non rapprochées | {{nb_lignes_non_rapprochees}} |

## 5. Ce qui manque

**Justificatifs manquants**

| Date | Fournisseur | Montant TTC | Ligne bancaire |
| --- | --- | --- | --- |
| {{date_manquant_1}} | {{fournisseur_manquant_1}} | {{montant_manquant_1}} | {{reference_banque_1}} |

**Lignes bancaires sans pièce**

| Date | Libellé | Montant |
| --- | --- | --- |
| {{date_ligne_1}} | {{libelle_ligne_1}} | {{montant_ligne_1}} |

## 6. Questions pour l'expert-comptable

1. {{question_1}}
2. {{question_2}}
3. {{question_3}}

## 7. Contrôles faits avant envoi

- [ ] Totaux HT + TVA = TTC sur chaque facture : {{controle_totaux}}
- [ ] Paiements reçus rapprochés des factures : {{controle_rapprochement}}
- [ ] Numérotation continue : {{controle_numerotation}}
- [ ] Solde bancaire égal au relevé : {{controle_solde}}
