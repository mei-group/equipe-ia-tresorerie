---
name: prevision-13-semaines
description: Méthode du prévisionnel de trésorerie à 13 semaines pour une TPE ou PME, construit client par client depuis les fichiers de donnees/ (banque, factures, devis, dépenses, abonnements). Trois scénarios prudent, central et optimiste, semaine la plus basse, alerte sous le seuil de entreprise.csv, actions chiffrées. À charger pour "prévois ma trésorerie", "vais-je manquer de cash", "trou de trésorerie", "point du lundi", ou toute question sur le solde des prochaines semaines.
---

# Prévisionnel de trésorerie à 13 semaines

Objectif : voir 3 mois devant, semaine par semaine, et agir avant que le compte passe sous le seuil.
Lecture seule. Le prévisionnel est une estimation, jamais une certitude. Pas un conseil financier.
Livrable : `sorties/rapports/AAAA-MM-JJ-previsionnel-13-semaines.md`, plus le même tableau en `.csv` si le dirigeant le demande.

---

## 1. Les entrées

Formats exacts : skill `format-tableaux`. Vérifier les fichiers avant de calculer.

| Fichier | Ce qu'on en tire |
| --- | --- |
| `entreprise.csv` | `seuil_alerte_tresorerie`, `delai_paiement_defaut_jours` |
| `banque.csv` | l'historique des flux : charges récurrentes, habitudes de paiement |
| `factures.csv` | les encaissements attendus : factures `emise`, `partielle`, `en_retard` |
| `clients.csv` | `delai_paiement_jours` de chaque client |
| `devis.csv` | les devis `accepte` pas encore facturés, et les devis `envoye` |
| `depenses.csv` | les factures fournisseurs `a_payer` |
| `abonnements.csv` | les prélèvements à venir |

**Le solde de départ.** Il se calcule depuis `banque.csv` : la ligne de catégorie `solde_initial`, plus toutes les lignes suivantes jusqu'à la date du jour. Si cette ligne manque, l'agent demande au dirigeant le solde réel du compte à une date précise. Il ne le suppose jamais nul.
Comptes d'épargne ou de réserve : exclus, sauf si le dirigeant les inclut.

## 2. La grille

- Semaine 1 : du lundi de la semaine en cours au dimanche.
- 13 lignes. Pour chaque semaine : encaissements, décaissements, solde de fin dans chaque scénario.
- Montants TTC : c'est ce qui passe sur le compte.
- Chaque ligne de flux porte une étiquette : `connu` (facture, échéance datée), `appris` (déduit de l'historique), `hypothèse` (donné ou supposé).

## 3. Les encaissements, client par client

Pour chaque facture non soldée de `factures.csv` (statut `emise`, `partielle` ou `en_retard`, type autre que `avoir`) :

1. **Reste dû** = `montant_ttc - montant_paye`.
2. **Retard habituel du client** = médiane de (`date_dernier_paiement - date_echeance`) sur ses factures `payee`. Sans historique : + 15 jours, noté `hypothèse`.
3. **Date attendue** = `date_echeance` + retard habituel. Si cette date est déjà passée : semaine 1 à 2 selon le niveau de relance, noté `hypothèse`.
4. **Probabilité d'encaissement** selon le retard actuel :

| Retard au jour du calcul | Probabilité |
| --- | --- |
| pas encore échue | 0,95 |
| 1 à 30 jours | 0,85 |
| 31 à 60 jours | 0,70 |
| 61 à 90 jours | 0,50 |
| plus de 90 jours | ligne "douteux", hors scénario central |

Ajustements : + 0,05 pour un client qui a toujours payé ; - 0,15 pour un client en retard de plus de 60 jours deux fois. Plafond 1,00.
Facture en `litige` : ligne "douteux", jamais dans le central.

5. **Devis `accepte` non facturés** : demander la date de facturation prévue si elle n'est pas claire, puis appliquer le retard habituel du client.
6. **Devis `envoye`** : scénario optimiste seulement.
7. **Avoirs** : déduits du client concerné.

Ces probabilités sont des repères de méthode, pas des statistiques de l'entreprise. Le dirigeant peut les changer.

## 4. Les décaissements

1. **Factures fournisseurs** `a_payer` de `depenses.csv` : à leur `date_echeance`, ou à l'habitude de paiement lue dans `banque.csv`. Échéance absente : demander.
2. **Abonnements** : `mensuelle` chaque mois à la date anniversaire de `date_renouvellement` ; `annuelle` à `date_renouvellement` si elle tombe dans les 13 semaines.
3. **Charges récurrentes apprises de `banque.csv`** : salaires, cotisations sociales, loyer, emprunts, assurances, TVA, impôts.
   - Une série est récurrente si elle a au moins 3 occurrences mensuelles ou 2 trimestrielles, à intervalles réguliers, avec des montants à moins de 15 % de la médiane.
   - Prochaine date = dernière date + intervalle médian.
   - Une série qui a manqué sa date de plus de la moitié de son intervalle s'est arrêtée : ne pas la projeter, demander.
   - Moins de 3 mois d'historique : le dire, et demander les charges fixes mensuelles.
4. **Dépenses courantes** (carte, petits achats) : moyenne hebdomadaire des 12 dernières semaines, sans les grosses dépenses exceptionnelles.
5. **TVA à reverser** et impôts : montants et dates à demander au dirigeant ou à l'expert-comptable si l'historique ne les montre pas clairement. Noté `hypothèse`.

## 5. Une question au dirigeant

Ce que l'historique ne peut pas savoir : embauche ou départ, gros achat, prêt ou subvention attendu, prime, saison basse.
Une seule question, regroupée. S'il veut aller vite : on saute, et on liste l'hypothèse.

## 6. Les trois scénarios

| Scénario | Encaissements | Décaissements |
| --- | --- | --- |
| **Prudent** | probabilités du tableau ; le plus gros client en montant ouvert paie 30 jours plus tard que d'habitude ; 10 % des encaissements non récurrents sortent de l'horizon ; devis `envoye` exclus | toutes les charges connues et apprises, + 5 % sur les récurrentes |
| **Central** | montant x probabilité, à la date attendue ; devis `envoye` exclus | toutes les charges connues et apprises |
| **Optimiste** | chaque client paie à l'échéance ; devis `envoye` à 30 % | toutes les charges connues et apprises |

Les pourcentages sont des paramètres de méthode, affichés dans le rapport, modifiables.

## 7. Le seuil et les alertes

- Seuil = `seuil_alerte_tresorerie` de `entreprise.csv`. S'il est vide : proposer 4 semaines de charges récurrentes, et le noter comme `hypothèse` à faire valider.
- Pour chaque scénario : la semaine la plus basse et son solde.
- Alerte **orange** : une semaine du scénario prudent passe sous le seuil.
- Alerte **rouge** : une semaine du scénario central passe sous le seuil, ou une semaine du prudent passe sous zéro.
- Chaque alerte vient avec des actions chiffrées, classées par effet sur la semaine la plus basse :
  - les factures à relancer en premier (numéro, client, montant, semaine gagnée) ;
  - un paiement fournisseur à décaler dans ses conditions ;
  - un achat à reporter ;
  - un acompte à demander sur les nouveaux devis ;
  - un abonnement inutile à couper (`utilise` = `non`) ;
  - une demande de facilité à préparer avec la banque, décision du dirigeant.

## 8. Le rapport

En tête, le bloc de `CONVENTIONS.md` (`STATUT : À VALIDER`, agent, données, points à vérifier), puis :

1. **En bref** : solde de départ et sa date, semaine la plus basse en central et en prudent, nombre de semaines sous le seuil.
2. **Tableau des 13 semaines** : début de semaine, encaissements, décaissements, solde prudent, central, optimiste. Arrondi à 100 €.
3. **Les 5 encaissements à risque** : client, montant, date attendue, pourquoi.
4. **Les actions**, classées.
5. **Les hypothèses**, une par ligne, et la part des décaissements `connu`, `appris`, `hypothèse`.

Format des montants : `12 300 €` dans le texte, `12300.00` dans le CSV. Dates : `16 novembre 2026` dans le texte, `2026-11-16` dans le CSV.

## 9. Garde-fous

- Pas de solde de départ : pas de prévisionnel. On le demande.
- Un fichier manquant ou illisible : le dire, marquer le prévisionnel "incomplet", ne jamais affirmer que le compte reste au-dessus du seuil.
- Une donnée vide n'est pas un zéro. Une dépense absente n'est pas une dépense nulle.
- Noms des clients visibles pour le dirigeant. Version à partager : Client A, B, C.
- Aucun IBAN dans le rapport.
- Dans un CSV produit, une cellule de texte qui commence par `=`, `+`, `-` ou `@` est préfixée d'une apostrophe. Les colonnes de montant ne sont pas concernées.
- Le journal reçoit sa ligne : `AAAA-MM-JJ HH:MM | previsionnel-13-semaines | prévisionnel | <fichier>`.

---

Adapté de : https://github.com/qonto/skills/tree/main/community/argentier-cash-forecast, licence MIT ; et de https://github.com/anthropics/knowledge-work-plugins/tree/main/small-business/skills/cash-flow-snapshot (dont `shared/absent-is-not-zero.md`), licence Apache-2.0.
