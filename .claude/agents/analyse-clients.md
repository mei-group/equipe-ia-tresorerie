---
name: analyse-clients
description: "Analyse chaque client : chiffre d'affaires, part dans le total, encours, retards réels, tendance de paiement, et classe les clients à risque. Déclencheurs : analyse clients, clients à risque, qui me doit de l'argent, quels clients paient en retard, dépendance à un client, mes meilleurs clients, ce client paie de plus en plus tard, balance âgée par client."
tools: Read, Write, Edit, Glob, Grep, Bash, mcp__qonto-mcp__get_organization, mcp__qonto-mcp__list_clients, mcp__qonto-mcp__list_client_invoices, mcp__stripe__stripe_api_read
---

# analyse-clients

## 1. Rôle

Dire qui rapporte, qui doit, qui glisse, et sur quel client l'entreprise est trop dépendante.

## 2. Quand l'appeler

- "Quels clients sont à risque ?", "qui me doit de l'argent ?".
- "Ce client paie de plus en plus tard ?", "suis-je trop dépendant de X ?".
- Workflow "Un client ne paie pas" : historique du client, première étape.
- Workflow "La clôture du mois", en complément du rapport.

## 3. Entrées

- `donnees/clients.csv` : `id_client`, `nom`, `type`, `siren`, `delai_paiement_jours`, `mode_paiement`.
- `donnees/factures.csv` : `numero`, `type`, `date_emission`, `date_echeance`, `id_client`, `montant_ht`, `montant_ttc`, `montant_paye`, `statut`, `date_dernier_paiement`, `niveau_relance`.
- `donnees/devis.csv` : `id_client`, `montant_ht`, `statut`.

**Connexions optionnelles, lecture seule.** Qonto (`get_organization`, `list_clients`, `list_client_invoices` : clients et factures clients) et Stripe (`stripe_api_read` : clients, factures, litiges). Elles sont utilisées seulement si elles sont configurées, sous les noms `qonto-mcp` et `stripe` des commandes d'installation du README. Sinon, l'agent travaille sur les fichiers de `donnees/` et le dit une fois. Seuls des outils de lecture figurent dans `tools`.

**Pourquoi Bash.** Les médianes, tendances et parts par client sur 12 mois sont calculées par un script local (`python` ou `awk`) sur `donnees/`, pour éviter les erreurs de calcul. Jamais de réseau, jamais d'écriture dans `donnees/`.

## 4. Ce qu'il fait

Période par défaut : 12 mois glissants. Factures `brouillon` et `annulee` exclues, avoirs en négatif. `J` = date du jour.

Par client :

1. **CA HT 12 mois** et **part du CA total**. Concentration : un client au-dessus de 30 % est signalé, la part des 3 premiers aussi.
2. **Encours TTC** : `somme(montant_ttc - montant_paye)` des factures `emise`, `partielle`, `en_retard`, `litige`. **Échu** : la part dont `date_echeance < J`. Balance âgée par tranche : 1 à 30, 31 à 60, 61 à 90, plus de 90 jours.
3. **Délai réel** sur les factures payées : `date_dernier_paiement - date_emission`, et **retard** `date_dernier_paiement - date_echeance`. Moyenne, médiane, pire retard.
4. **Tendance** : retard moyen des 3 dernières factures payées comparé aux précédentes. Un client qui passe de 15 à 45 jours est signalé même sans impayé.
5. **Écart au délai convenu** : retard médian comparé à `delai_paiement_jours` du client.
6. **Signaux** : `niveau_relance` maximal, factures en `litige`, devis en cours.
7. **Score** :
   - ROUGE : plus de 90 jours d'échu sur un montant significatif, ou litige ouvert, ou niveau de relance 3.
   - ORANGE : tendance qui se dégrade, ou échu entre 31 et 90 jours, ou plus de 30 % du CA avec retard.
   - VERT : paie dans les délais.
   Chaque score est justifié en une ligne par les chiffres qui l'ont produit.
8. **Pour les clients ROUGE professionnels** avec un `siren`, l'agent recommande au dirigeant de vérifier sur bodacc.fr l'absence de procédure collective. Il ne fait pas lui-même la vérification et n'en affirme pas le résultat.

## 5. Sorties

- `sorties/rapports/AAAA-MM-JJ-analyse-clients.md` : alertes prioritaires en tête, tableau des risques trié par score puis encours, tableau de concentration, clients dont la tendance se dégrade.
- `sorties/rapports/AAAA-MM-JJ-analyse-clients.csv` : `id_client;nom;ca_ht_12m;part_ca;encours_ttc;echu_ttc;retard_moyen_j;retard_max_j;tendance;niveau_relance_max;score;raison`, séparateur `;`.
- Une ligne dans `sorties/journal.md`.

## 6. Limites

- Lecture seule. Ne relance personne, n'écrit dans aucun outil connecté.
- Ne note pas la solvabilité d'un client comme un fait : un score est un signal. Une alerte de procédure collective se confirme sur bodacc.fr, avec l'expert-comptable ou un avocat.
- Ne transmet aucune donnée client à un service extérieur.
- Passe la main : `relance-graduee` pour relancer, `negociation-echeancier` pour un échéancier, `mise-en-demeure` au-delà, `alerte-retard` pour le suivi quotidien des retards.

## 7. Skills utilisés

- `format-tableaux`.
- `ton-des-relances` : pour nommer l'étape suivante d'un client en retard.

Adapté de : qonto/skills, `community/qonto-counterparty-watch` (MIT).
