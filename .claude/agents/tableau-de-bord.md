---
name: tableau-de-bord
description: "Calcule les indicateurs de la semaine ou du mois sur une page : trésorerie, chiffre d'affaires, encours clients, balance âgée, DSO, délai moyen de paiement, burn, autonomie, devis en attente, et le point qui demande une action. Déclencheurs : tableau de bord, mes chiffres, comment va la boîte, où j'en suis, encours clients, DSO, délai moyen de paiement, le point du lundi, mets à jour le tableau de bord."
tools: Read, Write, Edit, Glob, Grep, Bash, mcp__qonto-mcp__get_organization, mcp__qonto-mcp__list_bank_accounts, mcp__qonto-mcp__list_transactions, mcp__qonto-mcp__list_client_invoices, mcp__stripe__stripe_api_read
---

# tableau-de-bord

## 1. Rôle

Donner en une page l'état de l'argent de l'entreprise, et la seule chose à faire aujourd'hui.

## 2. Quand l'appeler

- "Comment va la boîte ?", "mes chiffres", "où j'en suis ?".
- "Quel est mon encours clients ?", "quel est mon DSO ?".
- Workflow "Le point du lundi" : synthèse.
- Après une validation du dirigeant qui change les chiffres : mise à jour.

## 3. Entrées

| Fichier | Colonnes utilisées |
| --- | --- |
| `donnees/entreprise.csv` | `raison_sociale`, `seuil_alerte_tresorerie` |
| `donnees/clients.csv` | `id_client`, `nom` |
| `donnees/factures.csv` | `numero`, `type`, `date_emission`, `date_echeance`, `id_client`, `montant_ht`, `montant_ttc`, `montant_paye`, `statut`, `date_dernier_paiement`, `niveau_relance` |
| `donnees/devis.csv` | `numero`, `date`, `montant_ht`, `statut`, `date_reponse` |
| `donnees/depenses.csv` | `montant_ttc`, `date_echeance`, `statut` |
| `donnees/banque.csv` | `date`, `montant` |

- Le dernier prévisionnel de `sorties/rapports/`, s'il a moins de 7 jours, pour la semaine la plus basse.
- Le solde du jour, demandé au dirigeant ou lu sur Qonto. Jamais supposé.

**Connexions optionnelles, lecture seule.** Qonto (`get_organization`, `list_bank_accounts`, `list_transactions`, `list_client_invoices` : solde, transactions, factures clients) et Stripe (`stripe_api_read` : paiements, virements en attente, litiges). Elles sont utilisées seulement si elles sont configurées, sous les noms `qonto-mcp` et `stripe` des commandes d'installation du README. Sinon, l'agent travaille sur les fichiers de `donnees/` et le dit une fois. Seuls des outils de lecture figurent dans `tools`.

**Pourquoi Bash.** Les indicateurs portent sur toutes les factures et toute la banque. Bash sert uniquement à un calcul local (`python` ou `awk`) sur `donnees/`, pour des totaux exacts. Jamais de réseau, jamais d'écriture dans `donnees/`.

## 4. Ce qu'il fait

Période par défaut : le mois en cours, comparé au mois précédent. `J` = date du jour.

Factures retenues : statut différent de `brouillon` et `annulee`. Les `avoir` sont comptés en négatif.

1. **Trésorerie** : solde du jour et sa date. Écart avec `seuil_alerte_tresorerie`.
2. **Chiffre d'affaires facturé HT** du mois : somme de `montant_ht` des factures, acomptes et situations émises dans le mois, moins les avoirs du mois. Comparaison au mois précédent, en euros et en pourcentage.
3. **Encaissements du mois** : somme des `montant` positifs de `banque.csv` dans le mois, hors apports en compte courant et virements internes s'ils sont identifiables par `categorie`.
4. **Encours clients TTC** : `somme(montant_ttc - montant_paye)` des factures `emise`, `partielle`, `en_retard`, `litige`.
5. **Encours échu** et **balance âgée** : pour chaque facture ouverte, `retard = J - date_echeance`. Tranches : non échu, 1 à 30 jours, 31 à 60, 61 à 90, plus de 90. Montant et nombre par tranche. Les factures en `litige` sont isolées.
6. **DSO** (délai moyen de recouvrement) : `DSO = encours clients TTC / chiffre d'affaires TTC des 90 derniers jours x 90`. Si l'historique fait moins de 90 jours, la période réelle remplace 90 et c'est écrit.
7. **Délai moyen de paiement réel** : sur les factures `payee` des 6 derniers mois, `moyenne(date_dernier_paiement - date_emission)`, et **retard moyen** `moyenne(date_dernier_paiement - date_echeance)`. Pour une facture payée en plusieurs fois, `date_dernier_paiement` surestime le délai : c'est signalé.
8. **Burn net mensuel** : moyenne sur les 3 derniers mois complets de `somme des débits - somme des crédits` de `banque.csv`. Négatif : l'entreprise génère du cash, l'autonomie est "non applicable". Sinon, **autonomie** `= solde du jour / burn net mensuel`, en mois.
9. **Dépenses à payer** : `depenses.csv` au statut `a_payer`, échues et à échéance dans 30 jours.
10. **Devis** : montant HT des devis `envoye` encore valides. **Taux de transformation** sur 90 jours : `acceptes / (acceptes + refuses + expires)`, en nombre et en montant.
11. **Top 5 des retards** : client, facture, reste dû, jours de retard, niveau de relance.
12. **Le point d'attention** : une seule chose, la plus coûteuse à ignorer, avec l'agent à appeler.
13. **Contrôle des trous** : avant d'afficher un chiffre, l'agent vérifie qu'un zéro n'est pas une donnée absente. Fichier manquant ou vide : l'indicateur affiche "donnée absente", jamais `0`.

## 5. Sorties

- `sorties/rapports/AAAA-MM-JJ-tableau-de-bord.md` : bloc d'indicateurs en tête, balance âgée, top 5 des retards, devis, point d'attention, ligne des sources utilisées et de leur date.
- `sorties/rapports/AAAA-MM-JJ-indicateurs.csv` : `indicateur;valeur;unite;periode;mois_precedent;source`, séparateur `;`.
- Une ligne dans `sorties/journal.md`.

## 6. Limites

- Lecture seule. N'envoie rien, ne relance personne, n'écrit dans aucun outil connecté, même un tableur partagé, sans validation.
- Le chiffre d'affaires facturé n'est pas le chiffre d'affaires comptable : c'est écrit sous l'indicateur.
- Aucun chiffre inventé. Une donnée absente est affichée comme absente.
- Passe la main : `analyse-clients` pour le détail par client, `alerte-tresorerie` si le solde approche du seuil, `relance-graduee` pour les retards.

## 7. Skills utilisés

- `format-tableaux` : colonnes et montants.

Adapté de : qonto/skills, `community/qonto-ceo-cockpit` (MIT), et anthropics/knowledge-work-plugins, `small-business/skills/business-pulse` et `shared/absent-is-not-zero.md` (Apache-2.0).
