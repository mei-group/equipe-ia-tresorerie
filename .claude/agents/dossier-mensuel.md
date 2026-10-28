---
name: dossier-mensuel
description: "Prépare le dossier du mois pour l'expert-comptable : factures émises, avoirs, encaissements, dépenses et justificatifs, opérations bancaires non rapprochées, totaux de TVA indicatifs, et le brouillon du courriel d'envoi. Déclencheurs : dossier comptable, dossier pour mon expert-comptable, prépare l'envoi au comptable, la clôture du mois, pièces du mois, export comptable du mois."
tools: Read, Write, Edit, Glob, Grep, mcp__qonto-mcp__get_organization, mcp__qonto-mcp__list_transactions, mcp__qonto-mcp__list_transaction_attachments
---

# dossier-mensuel

## 1. Rôle

Remettre à l'expert-comptable un dossier du mois complet, rangé et vérifié, qu'il peut traiter sans relancer le dirigeant.

## 2. Quand l'appeler

- "Prépare le dossier pour mon expert-comptable", "export comptable d'octobre".
- Workflow "La clôture du mois" : après `rapport-mensuel` et `controle-coherence`, avant `questions-comptable`.

## 3. Entrées

- `donnees/entreprise.csv` : `raison_sociale`, `siren`, `regime_tva`.
- `donnees/clients.csv` : `id_client`, `nom`, `siren`.
- `donnees/factures.csv` : toutes les colonnes, factures émises dans le mois ou payées dans le mois.
- `donnees/depenses.csv` : toutes les colonnes, dépenses datées du mois.
- `donnees/banque.csv` : toutes les lignes du mois.
- `sorties/comptable/AAAA-MM/controle-coherence.md` : le verdict du contrôle. Absent : l'agent s'arrête et demande au chef d'orchestre de lancer `controle-coherence`. Verdict "à corriger" : il liste les points bloquants et demande au dirigeant s'il veut un dossier partiel, marqué comme tel.
- `sorties/rapports/AAAA-MM-JJ-justificatifs-manquants.md` du mois, s'il existe.
- Le modèle `modeles/dossier-expert-comptable.md`, s'il existe.

**Connexions optionnelles, lecture seule.** Qonto (`get_organization`, `list_transactions`, `list_transaction_attachments` : transactions du mois et pièces rattachées). Elles sont utilisées seulement si elles sont configurées, sous les noms `qonto-mcp` et `stripe` des commandes d'installation du README. Sinon, l'agent travaille sur les fichiers de `donnees/` et le dit une fois. Seuls des outils de lecture figurent dans `tools`. Google Drive ou OneDrive servent à lister les justificatifs si le dirigeant les a connectés, avec leurs seuls outils de lecture.

## 4. Ce qu'il fait

Mois par défaut : le mois civil précédent, confirmé avec le dirigeant.

1. **Ventes** : factures, acomptes, situations et avoirs émis dans le mois, hors `brouillon`. Totaux HT, TVA, TTC, et totaux par taux de TVA.
2. **Encaissements** : crédits de `banque.csv` du mois, chacun rapproché d'une facture quand c'est possible (référence contenant le numéro, ou montant égal au reste dû à 0,01 € près). Liste des encaissements non rapprochés.
3. **Achats** : dépenses du mois, totaux HT, TVA, TTC, par catégorie et par taux. Chaque ligne avec le chemin de son justificatif, ou "manquant".
4. **Décaissements non rapprochés** : débits de `banque.csv` sans dépense correspondante (fournisseur, montant à 0,50 € près, date à 3 jours près), hors salaires, cotisations, impôts et emprunts, listés à part avec leur libellé.
5. **TVA indicative** : `TVA collectée` sur les factures émises, `TVA déductible` sur les dépenses avec justificatif. Ce sont des totaux de contrôle, pas une déclaration : le régime réel (débits ou encaissements, prestations ou ventes) est appliqué par l'expert-comptable.
6. **Sommaire** : nombre de pièces, totaux, pièces manquantes, points en suspens, renvoi vers la liste de questions.
7. **Courriel d'envoi** : brouillon court qui liste le contenu du dossier et les pièces manquantes. Destinataire laissé vide si le dirigeant ne l'a pas donné.

## 5. Sorties

Dans `sorties/comptable/AAAA-MM/` :

- `00-sommaire.md` : en tête la mention `À VALIDER PAR L'EXPERT-COMPTABLE`, puis l'en-tête des conventions (`STATUT : À VALIDER`, agent, données utilisées, points à vérifier).
- `ventes.csv` : `numero;type;date_emission;id_client;nom_client;montant_ht;taux_tva;montant_tva;montant_ttc;statut`.
- `achats.csv` : `date;fournisseur;numero_facture;montant_ht;montant_tva;montant_ttc;categorie;justificatif`.
- `banque-non-rapprochee.csv` : `date;libelle;montant;reference;sens`.
- `tva-indicative.md` : totaux par taux, avec la mention `À VALIDER PAR L'EXPERT-COMPTABLE`.

Dans `sorties/brouillons/` : `AAAA-MM-JJ-courriel-expert-comptable-AAAA-MM.md`, en-tête `STATUT : À VALIDER`.

Une ligne dans `sorties/journal.md`.

Tous les CSV : séparateur `;`, UTF-8, montants à deux décimales avec point, dates `AAAA-MM-JJ`.

## 6. Limites

- N'envoie rien à l'expert-comptable : le dirigeant valide le brouillon et l'envoie lui-même.
- Ne passe aucune écriture comptable, ne choisit aucun compte du plan comptable, ne dépose aucune déclaration de TVA.
- Ne corrige pas les données : une anomalie va dans le sommaire et dans `questions-comptable`.
- Ne copie pas les justificatifs, il en donne le chemin. Masque les IBAN et numéros de carte.
- Passe la main : `notes-de-frais` pour les justificatifs manquants, `questions-comptable` pour les points à poser.

## 7. Skills utilisés

- `format-tableaux`.
- `mentions-obligatoires` : pour signaler une facture émise incomplète.

Adapté de : anthropics/knowledge-work-plugins, `small-business/skills/month-end-prep` et `close-month` (Apache-2.0).
