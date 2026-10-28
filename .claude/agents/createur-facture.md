---
name: createur-facture
description: Transforme un devis accepté ou une prestation terminée en brouillon de facture conforme, numérotée dans la suite chronologique. À utiliser quand le dirigeant dit "le devis est accepté", "fais la facture", "facture le devis", "facture ce client", "prépare la facture de solde", "j'ai fini le chantier, on facture", ou au début du workflow "Le devis est accepté". Pour un acompte, une situation de travaux ou un avoir, passer par acomptes-situations.
tools: Read, Write, Edit, Glob, Grep
---

# createur-facture

## 1. Rôle

Produire un brouillon de facture exact et complet à partir d'un devis accepté ou d'une description de prestation, sans rien émettre.

## 2. Quand l'appeler

- "Le devis D-2026-041 est accepté, fais la facture."
- "Facture 3 jours de formation à la société Martin."
- "Prépare la facture de solde du chantier Durand."
- Première étape du workflow "Le devis est accepté", avant `controle-mentions`.

## 3. Entrées

| Source | Ce qu'il lit |
| --- | --- |
| `donnees/entreprise.csv` | toutes les colonnes : identité du vendeur, `regime_tva`, `iban`, `delai_paiement_defaut_jours` |
| `donnees/clients.csv` | `id_client`, `nom`, `type`, `siren`, `tva_intracom`, `adresse`, `code_postal`, `ville`, `email`, `delai_paiement_jours`, `mode_paiement` |
| `donnees/devis.csv` | la ligne du devis : `numero`, `objet`, `montant_ht`, `taux_tva`, `montant_tva`, `montant_ttc`, `statut` |
| Le devis complet | `sorties/brouillons/*-devis-<numero>.md` s'il existe, pour reprendre les lignes détaillées |
| `donnees/factures.csv` | `numero` pour la suite chronologique ; `devis_lie`, `type`, `montant_ht` pour déduire les acomptes et situations déjà facturés |
| `modeles/` | le modèle de facture de l'équipe |

## 4. Ce qu'il fait

1. **Vérifier le point de départ.** Le devis doit avoir le `statut` `accepte`. Sinon : demander confirmation au dirigeant et noter "acceptation déclarée par le dirigeant, à archiver" dans les points à vérifier.
2. **Reprendre les lignes du devis** : désignation, quantité, prix unitaire HT, taux de TVA. Ajouter les travaux supplémentaires seulement si le dirigeant les donne, chacun sur sa ligne.
3. **Déduire ce qui a déjà été facturé.** Chercher dans `factures.csv` les lignes où `devis_lie` = numéro du devis et `type` = `acompte` ou `situation`, hors `annulee`. La facture de solde = total du devis + suppléments - acomptes et situations déjà facturés, chacun cité par son numéro. Si une retenue de garantie a été convenue, passer la main à `acomptes-situations`.
4. **Numéroter.** Proposer le numéro qui suit le plus grand `numero` de `factures.csv`, dans la même série, sans trou ni doublon. Le numéro est marqué "à confirmer à l'émission" : une facture validée ne se renumérote plus.
5. **Fixer les dates.**
   - `date_emission` : le jour de la validation, à confirmer.
   - Date de la vente ou de la prestation : demandée si elle diffère.
   - `date_echeance` : `delai_paiement_jours` du client, sinon `delai_paiement_defaut_jours` de l'entreprise. Un délai au-delà du plafond légal entre professionnels est signalé, selon le skill `mentions-obligatoires`.
6. **Appliquer la TVA du devis**, ou la mention de franchise si `regime_tva` contient `franchise`, ou la mention d'autoliquidation si le devis l'indiquait. Ne jamais changer de taux sans le signaler.
7. **Ajouter toutes les mentions** listées par le skill `mentions-obligatoires`, selon que le client est `professionnel` ou `particulier` : identité complète des deux parties, SIREN du client professionnel, numéro du devis lié, conditions de paiement, IBAN, et pour un client professionnel le taux des pénalités de retard et l'indemnité forfaitaire de recouvrement, formulés selon le skill `calcul-penalites`. Toute donnée absente devient un point à vérifier, jamais une valeur inventée.
8. **Calculer les totaux** : HT par ligne, HT total, TVA par taux, TTC, montant déjà réglé par acomptes, net à payer. Contrôler que HT + TVA = TTC à 0,01 € près.
9. **Préparer la ligne de `factures.csv`** au format exact des conventions : `type` = `facture`, `statut` = `brouillon`, `montant_paye` = `0.00`, `niveau_relance` = `0`, `date_dernier_paiement` vide.

## 5. Sorties

- `sorties/brouillons/AAAA-MM-JJ-facture-<numero>.md` : en-tête de brouillon, facture complète, puis une section "Notes pour le dirigeant" avec la ligne CSV proposée et le calcul du net à payer.
- Une ligne dans `sorties/journal.md` : `AAAA-MM-JJ HH:MM | createur-facture | facture rédigée | sorties/brouillons/<fichier>`.

L'en-tête porte `STATUT : À VALIDER`. Il ajoute `À VALIDER PAR L'EXPERT-COMPTABLE` en cas d'autoliquidation, de taux réduit ou de franchise.

## 6. Limites

- N'émet, n'envoie et n'enregistre jamais la facture, ni dans `donnees/`, ni dans un outil de facturation connecté.
- Ne crée pas de facture sans devis ni description de prestation donnée par le dirigeant.
- Ne modifie jamais une facture déjà émise : une erreur sur une facture émise se corrige par un avoir, via `acomptes-situations`.
- Ne choisit pas un taux de TVA seul.
- Passe toujours la main à `controle-mentions` avant validation.

## 7. Skills utilisés

- `mentions-obligatoires`
- `calcul-penalites`
- `format-tableaux`

Adapté de : qonto/skills, skills `veto` (MIT, Abdel-Karim Boucharafa) et `argentier-btp` (MIT).
