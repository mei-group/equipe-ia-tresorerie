---
name: ton-des-relances
description: Le ton et les formulations des relances de factures impayées, en 3 niveaux (rappel aimable vers J+7, relance ferme vers J+21, dernière relance avant mise en demeure vers J+45), pour un client professionnel ou un particulier. Modèles de textes, adaptation au profil du payeur, et ce qu'il ne faut jamais écrire. À charger avant de rédiger une relance, un courriel de suivi d'impayé ou un échéancier.
---

# Le ton des relances

But : être payé et garder le client. Une relance trop dure perd le client. Une relance trop molle perd l'argent.
Chaque relance est un brouillon dans `sorties/brouillons/`, marqué `STATUT : À VALIDER`. Rien ne part sans le dirigeant.

---

## 1. Avant d'écrire une ligne

1. **La facture est-elle déjà payée ?** Chercher dans `banque.csv` un encaissement du même montant, au centime près, avec le nom du client ou le numéro de facture dans le libellé. Si oui : proposer de la marquer payée, ne pas relancer. La relance la plus coûteuse est celle envoyée à un client qui a payé.
2. **Paiement ambigu** (montant proche, virement groupé, paiement partiel) : montrer les candidats au dirigeant, ne pas relancer.
3. **Statut `litige`** : pas de relance. On passe la main au dirigeant.
4. **Plusieurs factures en retard pour un même client** : une seule relance qui les liste toutes, avec le total.
5. **Adresse courriel absente** dans `clients.csv` : la demander. Ne jamais la deviner.

## 2. Le profil du payeur

Calculé sur les factures payées des 12 derniers mois du client dans `factures.csv` (date_dernier_paiement comparée à date_echeance). Moins de 3 factures : profil "occasionnel".

| Profil | Critère | Effet sur le ton |
| --- | --- | --- |
| Bon payeur | payé à l'heure dans au moins 75 % des cas | ton le plus chaleureux, on suppose un oubli |
| Occasionnel | en retard dans 25 à 50 % des cas, ou moins de 3 factures | ton standard, factuel |
| Retardataire | en retard dans plus de 50 % des cas | on saute le niveau le plus chaleureux, ton correct et daté |

Le profil ajuste le ton. Il ne change pas les faits ni les dates.
Le profil reste interne : il n'apparaît jamais dans le texte envoyé au client.

## 3. Les 3 niveaux

Le niveau est noté dans `factures.csv`, colonne `niveau_relance` (0 = aucune relance).

| Niveau | Quand | Registre | Contient obligatoirement |
| --- | --- | --- | --- |
| 1. Rappel aimable | vers J+7 après l'échéance | chaleureux, suppose un oubli | n° de facture, montant TTC, date d'échéance, moyen de paiement prévu sur la facture |
| 2. Relance ferme | vers J+21 | ferme, factuel | la même chose + nombre de jours de retard + date du premier rappel + prochaine étape annoncée |
| 3. Dernière relance avant mise en demeure | vers J+45 | formel | la même chose + pénalités calculées (B2B) ou rappel des intérêts possibles (B2C) + date limite de paiement + annonce de la mise en demeure |

La mise en demeure elle-même n'est pas un niveau de relance. Elle est rédigée par l'agent `mise-en-demeure`, avec la mention `À FAIRE RELIRE PAR UN PROFESSIONNEL DU DROIT AVANT ENVOI`.
Attention : une lettre qui interpelle clairement le débiteur peut valoir mise en demeure (article 1344 du Code civil, https://www.legifrance.gouv.fr/codes/article_lc/LEGIARTI000032042162, repéré le 2026-10-28). Le niveau 3 annonce donc la mise en demeure, il ne la remplace pas.

Les jours J+7, J+21, J+45 sont des repères, pas des obligations légales. Le dirigeant peut les décaler.

Chaque chiffre est réel : numéro, montant, jours de retard, pénalités. Pas de champ vide, pas de `{montant}` oublié. Les montants et pénalités viennent du skill `calcul-penalites`.

## 4. Client professionnel ou particulier

| Point | Professionnel | Particulier |
| --- | --- | --- |
| Vouvoiement | oui | oui |
| Pénalités de retard | rappel au niveau 2, montant calculé au niveau 3 | pas de pénalités commerciales ; intérêts au taux légal seulement après mise en demeure |
| Indemnité de 40 € | mentionnée aux niveaux 2 et 3 | jamais |
| Frais de recouvrement | 40 € forfaitaires, ou plus sur justificatif | jamais réclamés en amiable (L111-8 du Code des procédures civiles d'exécution, lu le 2026-10-28) |
| Interlocuteur | comptabilité fournisseurs + contact du projet | la personne elle-même, en toute discrétion |
| Échéancier | proposé si demandé | proposé dès le niveau 2, simplement |

Les règles chiffrées et leurs sources sont dans le skill `calcul-penalites`.

## 5. Les modèles

Les crochets sont remplacés par des données réelles. Un crochet qui reste = brouillon refusé.

### Niveau 1, rappel aimable, professionnel

> Objet : Facture [numéro] du [date d'émission] : petit rappel
>
> Bonjour [prénom nom],
>
> Sauf erreur de notre part, la facture [numéro] de [montant TTC] €, arrivée à échéance le [date d'échéance], n'apparaît pas encore comme réglée.
>
> Vous trouverez nos coordonnées de paiement sur la facture, que je joins de nouveau. Si le règlement est déjà parti, merci de ne pas tenir compte de ce message.
>
> Bonne journée,
> [signature]

### Niveau 1, rappel aimable, particulier

> Objet : Votre facture [numéro]
>
> Bonjour [Madame ou Monsieur nom],
>
> Je me permets de vous rappeler la facture [numéro] de [montant TTC] €, à régler le [date d'échéance]. Elle ne figure pas encore dans nos encaissements.
>
> C'est peut-être un simple oubli. Si vous avez déjà payé, ne tenez pas compte de ce message. Pour toute question, je reste joignable au [téléphone].
>
> Bien cordialement,
> [signature]

### Niveau 2, relance ferme, professionnel

> Objet : Facture [numéro] : [jours] jours de retard
>
> Bonjour [prénom nom],
>
> Malgré notre rappel du [date du rappel], la facture [numéro] de [montant TTC] €, échue le [date d'échéance], reste impayée. Elle compte aujourd'hui [jours] jours de retard.
>
> Nos conditions de paiement prévoient des pénalités de retard au taux de [taux] % et l'indemnité forfaitaire de 40 € pour frais de recouvrement.
>
> Merci de procéder au règlement avant le [date limite, 8 jours]. Si un point bloque le paiement, dites-le-moi : nous le réglerons ensemble.
>
> Cordialement,
> [signature]

### Niveau 2, relance ferme, particulier

> Objet : Facture [numéro] toujours en attente
>
> Bonjour [Madame ou Monsieur nom],
>
> Je reviens vers vous au sujet de la facture [numéro] de [montant TTC] €, à régler depuis le [date d'échéance]. Notre premier rappel du [date du rappel] est resté sans réponse.
>
> Merci de la régler avant le [date limite]. Si le montant en une fois pose problème, je peux vous proposer un paiement en plusieurs fois. Il suffit de me répondre.
>
> Bien cordialement,
> [signature]

### Niveau 3, dernière relance avant mise en demeure, professionnel

> Objet : Facture [numéro] : dernière relance avant mise en demeure
>
> Madame, Monsieur,
>
> Malgré nos relances du [date 1] et du [date 2], la facture [numéro] de [montant TTC] €, échue le [date d'échéance], reste impayée après [jours] jours.
>
> À ce jour, la somme due s'élève à [total] € : [montant TTC] € de principal, [pénalités] € de pénalités de retard au taux de [taux] %, et 40 € d'indemnité forfaitaire pour frais de recouvrement.
>
> Sans règlement au plus tard le [date limite], nous vous adresserons une mise en demeure par lettre recommandée avec accusé de réception.
>
> Je reste disponible pour en parler d'ici là.
>
> [signature]

### Niveau 3, dernière relance avant mise en demeure, particulier

> Objet : Facture [numéro] : dernière relance
>
> Madame, Monsieur,
>
> Malgré nos relances du [date 1] et du [date 2], la facture [numéro] de [montant TTC] €, à régler depuis le [date d'échéance], n'est toujours pas payée.
>
> Je vous remercie de la régler au plus tard le [date limite]. Sans règlement ni nouvelle de votre part à cette date, je vous adresserai une mise en demeure par lettre recommandée. Des intérêts au taux légal courront alors sur la somme due.
>
> Un paiement en plusieurs fois reste possible si vous me contactez avant cette date.
>
> [signature]

## 6. Ce qu'on n'écrit jamais

- Une menace qui ne sera pas suivie d'effet, ou qu'on n'a pas le droit d'exécuter.
- Une procédure présentée comme engagée alors qu'elle ne l'est pas.
- Prison, fichage, saisie immédiate, "huissier demain" : jamais.
- Une somme non due : frais de dossier, frais de relance inventés, 40 € réclamés à un particulier, frais de recouvrement amiable réclamés à un particulier.
- Un taux ou un montant sans calcul vérifié.
- Un reproche personnel, une insulte, de l'ironie, des majuscules ou des points d'exclamation en série.
- "Comme d'habitude", "encore une fois", ou toute allusion au profil du payeur.
- Une information sur un autre client, ou des données bancaires autres que celles de la facture.
- Une copie à un tiers : employeur, famille, autre client, réseau social.
- Un nouveau RIB ou de nouvelles coordonnées de paiement non validées par le dirigeant.
- Une relance à un client qui a déjà payé, ou dont la facture est en litige.
- Deux demandes dans un même courriel : une relance, une action attendue.

## 7. Après la relance

- Proposer au dirigeant de mettre à jour `niveau_relance` dans `factures.csv`, seulement après son envoi.
- Ajouter la ligne au journal : `AAAA-MM-JJ HH:MM | relance-graduee | brouillon relance niveau N | <fichier>`.
- Client qui répond par une contestation : stopper l'escalade, statut `litige` proposé au dirigeant.
- Client qui demande un délai : passer la main à `negociation-echeancier`.

---

Adapté de : https://github.com/qonto/skills/tree/main/community/qonto-invoice-chaser et https://github.com/qonto/skills/blob/main/community/veto/skills/veto/references/reminder-templates.md, licence MIT ; et de https://github.com/anthropics/knowledge-work-plugins/tree/main/small-business/skills/invoice-chase (`reference/tone-matching.md`), licence Apache-2.0.
