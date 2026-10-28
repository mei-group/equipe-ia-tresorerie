# Les conventions de l'équipe

Tous les agents, skills, workflows et modèles suivent ce document. Il est la source unique des formats.

## Les règles qui ne se discutent pas

- **Rien ne part sans validation humaine.** Ni facture, ni relance, ni courriel, ni paiement, ni écriture dans un outil connecté. Un agent produit un brouillon dans `sorties/brouillons/`, marqué `STATUT : À VALIDER`, et s'arrête.
- **Mode fichiers par défaut.** L'équipe lit les fichiers de `donnees/`. Une connexion (Qonto, Stripe, Gmail, Drive, Microsoft 365) n'est utilisée que si elle est configurée, et en lecture seule.
- **L'expert-comptable décide.** Tout livrable fiscal, comptable ou juridique porte la mention `À VALIDER PAR L'EXPERT-COMPTABLE`. Une mise en demeure porte `À FAIRE RELIRE PAR UN PROFESSIONNEL DU DROIT AVANT ENVOI`.
- **Aucune clé dans un fichier.** Les accès passent par `.env`, jamais commité.
- **Aucun chiffre inventé.** Un agent qui manque d'une donnée la demande, ou l'écrit comme hypothèse.
- **Les montants** : en euros, deux décimales, point décimal dans les fichiers, `1 234,56 €` dans les textes destinés à un humain.
- **Les dates** : `AAAA-MM-JJ` dans les fichiers, `12 novembre 2026` dans les textes.

## Les dossiers

```
donnees/        les fichiers d'entrée, fournis par l'utilisateur
sorties/
  brouillons/   tout ce qui attend une validation
  rapports/     tableaux de bord, prévisionnels, rapports mensuels
  comptable/    le dossier mensuel de l'expert-comptable
  journal.md    chaque action d'un agent, datée, une ligne
modeles/        les modèles de documents
```

## Les fichiers d'entrée, dans `donnees/`

Séparateur `;`, encodage UTF-8, première ligne = en-têtes.

**`entreprise.csv`** : une ligne. `raison_sociale;forme;capital;siren;rcs_ville;adresse;code_postal;ville;tva_intracom;regime_tva;email;telephone;iban;delai_paiement_defaut_jours;seuil_alerte_tresorerie`

**`clients.csv`** : `id_client;nom;type;siren;tva_intracom;adresse;code_postal;ville;email;delai_paiement_jours;mode_paiement`
- `type` : `professionnel` ou `particulier`.
- `tva_intracom` : le numéro de TVA intracommunautaire d'un client professionnel, vide pour un particulier.

**`devis.csv`** : `numero;date;id_client;objet;montant_ht;taux_tva;montant_tva;montant_ttc;statut;date_validite;date_reponse`
- `statut` : `brouillon`, `envoye`, `accepte`, `refuse`, `expire`.

**`factures.csv`** : `numero;type;date_emission;date_echeance;id_client;devis_lie;montant_ht;taux_tva;montant_tva;montant_ttc;montant_paye;statut;date_dernier_paiement;niveau_relance`
- `type` : `facture`, `acompte`, `situation`, `avoir`.
- `statut` : `brouillon`, `emise`, `partielle`, `payee`, `en_retard`, `litige`, `annulee`.
- `niveau_relance` : `0` à `3`.
- `taux_tva` est un pourcentage : `20.00`, `10.00`, `5.50`, `0.00`. Même règle dans `devis.csv`.
- Un avoir porte des montants négatifs : `montant_ht`, `montant_tva` et `montant_ttc` sont inférieurs à zéro.

**`banque.csv`** : `date;libelle;montant;reference;categorie`
- `montant` positif pour un encaissement, négatif pour une dépense.
- La première ligne porte la catégorie `solde_initial` : son montant est le solde du compte à cette date. Le solde à une date donnée est ce montant plus toutes les lignes suivantes.

**`depenses.csv`** : `date;fournisseur;numero_facture;montant_ht;montant_tva;montant_ttc;categorie;date_echeance;statut;justificatif`
- `statut` : `a_payer`, `payee`, `sans_justificatif`.
- `justificatif` : chemin du fichier, ou vide.

**`abonnements.csv`** : `fournisseur;libelle;montant_ttc;periodicite;date_renouvellement;utilise`
- `periodicite` : `mensuelle`, `annuelle`. `utilise` : `oui`, `non`, `inconnu`.

## Les sorties

- **Brouillon** : un fichier `.md` par document, nommé `AAAA-MM-JJ-type-reference.md`, qui commence par :

```
STATUT : À VALIDER
Agent : nom-de-l-agent
Données utilisées : liste des fichiers ou connexions
Points à vérifier : liste, ou "aucun"
```

- **Jamais d'écrasement.** Si un fichier du même nom existe déjà, l'agent ajoute `-2`, `-3`, etc. avant l'extension. Un rapport d'un autre workflow ne disparaît jamais.
- **Journal** : chaque agent ajoute une ligne à `sorties/journal.md` : `AAAA-MM-JJ HH:MM | agent | action | fichier produit`.

## Les agents : le format

Chaque agent est un fichier `.claude/agents/<nom>.md`, au format des subagents de Claude Code : un en-tête YAML (`name`, `description`, `tools`), puis ses instructions en français, dans cet ordre :

1. **Rôle** : une phrase.
2. **Quand l'appeler** : les demandes qui le déclenchent.
3. **Entrées** : les fichiers et connexions qu'il lit.
4. **Ce qu'il fait** : les étapes.
5. **Sorties** : ce qu'il produit, et où.
6. **Limites** : ce qu'il refuse, et à qui il passe la main.
7. **Skills utilisés**.

`tools` : le minimum. Lecture et écriture de fichiers pour tous, aucun outil d'envoi.

## Les crédits

Une partie du contenu est adaptée de :
- **qonto/skills**, licence MIT : https://github.com/qonto/skills
- **Le plugin small-business d'Anthropic**, licence Apache-2.0.

Tout fichier adapté le dit dans une ligne `Adapté de : ...` en fin de fichier. Les textes de licence sont dans `LICENCES/`.
