---
name: mentions-obligatoires
description: Mentions obligatoires d'un devis et d'une facture en France, pour un client professionnel ou particulier. Numérotation, avoirs, acomptes et situations, mentions de TVA (autoliquidation, franchise en base), pénalités et indemnité de 40 €, calendrier de la facturation électronique. À charger avant de rédiger ou de contrôler un devis, une facture, un acompte, une situation ou un avoir.
---

# Mentions obligatoires des devis et des factures

Ce skill sert de grille de contrôle. Il ne remplace pas l'expert-comptable.
Chaque règle porte sa source et la date de lecture. Toutes les sources ont été lues le 28 octobre 2026.
Un document produit avec ce skill porte `À VALIDER PAR L'EXPERT-COMPTABLE`.

Si une règle ci-dessous date de plus de 6 mois, relire la source avant de l'appliquer.
Si un cas n'est pas couvert, écrire "à vérifier sur <source>" dans `Points à vérifier`. Ne jamais compléter de mémoire.

Hors périmètre : les micro-entrepreneurs ont une fiche à part sur service-public, non traitée ici. Les règles sectorielles (alimentaire, transport, export) ne sont pas couvertes.

---

## 1. La facture : mentions communes à tous les clients

Source principale : https://entreprendre.service-public.gouv.fr/vosdroits/F31808 (fiche "Vérifié le 11 août 2026", lue le 2026-10-28).
Texte fiscal : article 242 nonies A de l'annexe II du CGI, https://www.legifrance.gouv.fr/codes/article_lc/LEGIARTI000050811276 (version en vigueur depuis le 1er janvier 2025, lue le 2026-10-28).
Texte commercial : article L441-9 du Code de commerce, https://www.legifrance.gouv.fr/codes/article_lc/LEGIARTI000038414397/ (en vigueur depuis le 26 avril 2019, lu le 2026-10-28).

| Contrôle | Règle | Source |
| --- | --- | --- |
| Date d'émission | Obligatoire. | F31808 ; 242 nonies A, I, 6° |
| Numéro | Unique, basé sur une séquence chronologique et continue. Voir section 4. | F31808 ; 242 nonies A, I, 7° |
| Date de la vente ou de la prestation | Date de livraison ou d'achèvement, ou date de versement de l'acompte, si elle diffère de la date d'émission. | F31808 ; 242 nonies A, I, 10° |
| Identité du vendeur | Société : raison sociale, SIREN, adresse du siège, forme juridique et capital. Entrepreneur individuel : nom, prénom, mention EI, adresse, SIREN. Adresse de facturation si différente du siège. | F31808 |
| Société en cours d'immatriculation | Facture au nom de la société avec "Siret en cours d'attribution". | F31808 |
| Identité du client | Professionnel : dénomination. Particulier : nom et adresse, sauf opposition du client. | F31808 |
| Numéro de bon de commande | Si l'acheteur en a établi un. | F31808 ; L441-9 |
| Désignation | Quantité, dénomination précise, prix unitaire HT, taux de TVA ou mention d'exonération, par ligne. Pour une prestation : fournitures et main-d'oeuvre. | F31808 ; 242 nonies A, I, 8° |
| Totaux | Montant HT, montant de TVA par taux, montant TTC, frais annexes (transport, emballage). | F31808 ; 242 nonies A, I, 11° |
| Réductions | Rabais, remises, ristournes acquis et chiffrables, liés à l'opération. | F31808 ; 242 nonies A, I, 9° |
| Date d'échéance | Date à laquelle le paiement doit intervenir. | F31808 ; L441-9 |
| Escompte | Conditions d'escompte. Si aucun : "Escompte pour paiement anticipé : néant". | F31808 ; L441-9 |
| Pénalités de retard | Taux des pénalités exigibles à compter du jour suivant l'échéance. Voir le skill `calcul-penalites`. | F31808 ; L441-9 |
| Indemnité forfaitaire | 40 € pour frais de recouvrement, client professionnel uniquement. | F31808 ; D441-5 C. com. |
| N° de TVA intracommunautaire | Du vendeur, et du client professionnel assujetti. Non exigé si le total HT est de 150 € ou moins. | F31808 ; 242 nonies A, II |

Sanction affichée par L441-9 II (lu le 2026-10-28) : amende administrative jusqu'à 75 000 € pour une personne physique et 375 000 € pour une personne morale, doublée en cas de réitération dans les deux ans.

Émission : le vendeur délivre la facture dès la réalisation de la livraison ou de la prestation (L441-9, lu le 2026-10-28).

## 2. Ce qui change selon le client

**Client professionnel (B2B)**
- Indemnité forfaitaire de 40 € à faire figurer (F31808 ; D441-5 lu le 2026-10-28 sur https://www.legifrance.gouv.fr/codes/section_lc/LEGITEXT000005634379/LEGISCTA000043187249/).
- N° de TVA du client s'il est assujetti, au-delà de 150 € HT (F31808).

**Client particulier (B2C)**
- Pas d'indemnité forfaitaire de 40 € : F31808 la réserve aux clients professionnels.
- Nom et adresse du client, sauf opposition (F31808).
- Garantie légale de conformité : mentionner son existence et sa durée, au moins 2 ans, pour certains produits (électroménager, informatique, téléphonie, outillage motorisé, jouets, articles de sport, montres, luminaires, mobilier...). Liste complète sur F31808, lue le 2026-10-28.

## 3. Mentions de TVA particulières

| Situation | Mention exacte | Source |
| --- | --- | --- |
| Franchise en base, factures émises jusqu'au 31 décembre 2026 | `TVA non applicable, art. 293 B du CGI` | F31808 (lu le 2026-10-28) ; https://www.impots.gouv.fr/professionnel/questions/en-tant-que-micro-entrepreneur-puis-je-etre-redevable-de-la-tva (mise à jour du 14/10/2026, lue le 2026-10-28) |
| Franchise en base, à partir du 1er janvier 2027 | `TVA non applicable, article L.233-3 du Code des impositions des biens et des services (CIBS)` | même page impots.gouv.fr, qui annonce cette entrée en vigueur au 1er janvier 2027. Légifrance indique l'article 293 B "Abrogé par Ordonnance n°2025-1247 du 17 décembre 2025" (lu le 2026-10-28 sur https://www.legifrance.gouv.fr/codes/id/LEGISCTA000006162567/). Date exacte de bascule : à vérifier sur impots.gouv.fr en décembre 2026. |
| Client redevable de la TVA (autoliquidation) | `Autoliquidation` | 242 nonies A, I, 13° ; F31808 écrit "Auto-liquidation" |
| Sous-traitance de travaux de construction (art. 283, 2 nonies du CGI) | `Autoliquidation`, pas de TVA facturée, indiquer que la TVA est due par l'entrepreneur principal. N° de TVA du preneur non obligatoire dans ce cas. | BOFiP https://bofip.impots.gouv.fr/bofip/9671-PGP.html/identifiant=ACTU-2014-00285 (lu le 2026-10-28) |
| Sous-traitant en franchise en base | Pas d'autoliquidation, mention de franchise à la place. | même page BOFiP |
| Client émet la facture lui-même | `Autofacturation` | 242 nonies A, I, 14° |
| Exonération | Référence à l'article du CGI ou de la directive 2006/112/CE qui l'autorise. | 242 nonies A, I, 12° |
| Option pour la TVA sur les débits | `Option pour le paiement de la taxe d'après les débits` | 242 nonies A, I, 11° bis ; F31808 |

Seuils de la franchise en base affichés par impots.gouv.fr (mise à jour du 14/10/2026, lue le 2026-10-28) : 85 000 € HT pour les ventes et l'hébergement, 37 500 € HT pour les services ; seuils majorés 93 500 € et 41 250 €. Ces seuils changent : à revérifier avant chaque usage.

Taux de TVA : non repris ici. À vérifier sur impots.gouv.fr pour chaque opération.

## 4. Numérotation

Source : BOFiP BOI-TVA-DECLA-30-20-20-10, https://bofip.impots.gouv.fr/bofip/140-PGP.html/identifiant=BOI-TVA-DECLA-30-20-20-10-20121231 (§ 70 à 130, lu le 2026-10-28) ; 242 nonies A, I, 7°.

- Un numéro unique, séquence chronologique et continue. Pas de trou, pas de doublon.
- Séries distinctes autorisées si elles sont justifiées (plusieurs sites, catégories de clients, modes d'émission). Chaque série garde sa logique. Le BOFiP conseille un préfixe par série (§ 90).
- Deux factures d'une même année ne peuvent pas porter le même numéro (§ 90).
- Avoir des clients à l'étranger ne justifie pas, en principe, une série par client ou par pays (§ 110).
- Remise à zéro annuelle : non décrite par le BOFiP. Un préfixe d'année (`2026-0001`) respecte la règle d'unicité. Pratique à confirmer avec l'expert-comptable.

Contrôle à faire dans `factures.csv` : trier par numéro et par date, vérifier que l'ordre est le même et qu'aucun numéro ne manque. Un trou est signalé, jamais comblé par l'agent.

## 5. Avoirs et factures rectificatives

Source : BOFiP BOI-TVA-DECLA-30-20-20-20, https://bofip.impots.gouv.fr/bofip/142-PGP.html/identifiant=BOI-TVA-DECLA-30-20-20-20-20220119 (§ 180 à 260, lu le 2026-10-28).

- Une facture émise ne se modifie pas. On émet un avoir ou une facture rectificative. Ne jamais réutiliser son numéro.
- L'avoir ou la facture rectificative fait référence de façon explicite à la facture initiale : numéro et date (§ 220).
- Il reprend les mentions obligatoires, sauf les mentions 2° et 12° de 242 nonies A qui peuvent être omises (§ 180, § 220).
- Facture de remplacement : référence exacte à la facture initiale, mention expresse de son annulation, et toutes les mentions du I de 242 nonies A (§ 240, § 250).
- Avoir global (remise de fin d'année) : il peut viser un ensemble de factures ou le contrat, en précisant la période d'émission de ces factures (§ 260).
- Dans `factures.csv`, l'avoir a `type` = `avoir`, des montants négatifs ou positifs selon la convention retenue par l'utilisateur (à confirmer, voir `format-tableaux`), et `devis_lie` ou le numéro de facture initiale dans le libellé.

## 6. Acomptes et situations de travaux

- Facture d'acompte : soumise aux mêmes obligations de numérotation et de date. La facture finale fait référence aux factures d'acompte (BOFiP BOI-TVA-DECLA-30-20-20-10, § 60, lu le 2026-10-28).
- Date de versement de l'acompte à indiquer si elle diffère de la date d'émission (242 nonies A, I, 10°).
- La facture finale déduit les acomptes déjà facturés, en HT, TVA et TTC.
- Situations de travaux (BTP) : chaque situation est une facture numérotée dans la séquence. Règles propres aux marchés de travaux : à vérifier sur legifrance.gouv.fr et avec l'expert-comptable.
- Retenue de garantie : plafond et conditions à vérifier sur legifrance.gouv.fr (loi n° 71-584 du 16 juillet 1971). Ne pas appliquer de taux sans vérification.
- Exigibilité de la TVA sur un acompte (livraison de biens ou prestation de services) : à vérifier avec l'expert-comptable.

## 7. Le devis

Source : https://entreprendre.service-public.gouv.fr/vosdroits/F31144 (fiche "Vérifié le 09 septembre 2022", lue le 2026-10-28 : fiche ancienne, à recouper).

- Il n'existe pas de liste unique de mentions : chaque secteur a la sienne.
- Devis obligatoire notamment : travaux et dépannage du bâtiment et de l'équipement de la maison, déménagement, location de voiture, optique et audition, services à la personne (dès 100 € TTC par mois), produits pour la perte d'autonomie (dès 500 € TTC), chirurgie esthétique (dès 300 €), funéraire.
- Mentions qui reviennent partout : date, identité de l'entreprise et du client, durée de validité, détail des prestations, montants HT et TTC avec le taux de TVA.
- Bâtiment et dépannage, en plus : lieu d'intervention, nature des travaux, taux horaire TTC, modalités de décompte du temps, frais de déplacement, mention gratuit ou payant.
- Hors secteurs réglementés (devis entre professionnels) : pas de liste légale trouvée. Bonne pratique de l'équipe : reprendre les mentions d'identité, le détail, les totaux, la durée de validité, les conditions de paiement et d'acompte, et les mentions de TVA de la section 3.
- Une fois accepté, le devis engage le professionnel sur son contenu (economie.gouv.fr, résultat de recherche du 2026-10-28 : à relire sur https://www.economie.gouv.fr/dgccrf/les-fiches-pratiques/devis, page inaccessible le 2026-10-28).

## 8. Facturation électronique : le calendrier

Source : https://www.impots.gouv.fr/professionnel/je-decouvre-la-facturation-electronique (mise à jour du 26/05/2026, lue le 2026-10-28).

- Depuis le 1er septembre 2026 : toutes les entreprises, quelle que soit leur taille, doivent pouvoir recevoir des factures électroniques.
- 1er septembre 2026 : émission obligatoire pour les grandes entreprises et les ETI.
- 1er septembre 2027 : émission obligatoire pour les PME et les micro-entreprises.
- Le e-reporting suit le même calendrier.

Quatre nouvelles mentions, obligatoires à la même date que l'émission (F31808 ; 242 nonies A, I, 1°, 7° bis, 8° bis, 11° bis, lus le 2026-10-28) :
1. SIREN du client, s'il est une entreprise.
2. Adresse de livraison, si elle diffère de l'adresse du client.
3. Nature des opérations : biens, services, ou les deux.
4. `Option pour le paiement de la taxe d'après les débits`, si l'entreprise a opté.

Pour une PME, ces mentions deviennent obligatoires au 1er septembre 2027. L'équipe les signale comme "recommandé" avant cette date, jamais comme "obligatoire".
L'équipe ne choisit pas de plateforme agréée : décision du dirigeant et de l'expert-comptable.

## 9. Comment l'agent utilise ce skill

1. Lire `entreprise.csv`, `clients.csv`, et la ligne de `factures.csv` ou `devis.csv` concernée.
2. Passer chaque ligne des sections 1, 2, 3 et 8. Noter : présent, absent, non applicable.
3. Calculer : HT x taux = TVA à 0,01 près ; HT + TVA = TTC.
4. Vérifier la numérotation (section 4).
5. Écrire les manques dans `Points à vérifier`, avec la source de la règle.
6. Ne jamais inventer une donnée manquante : SIREN, n° de TVA, capital, adresse se demandent.

---

Adapté de : https://github.com/qonto/skills/tree/main/community/veto (fichier `references/compliance-fr.md`), licence MIT. Chaque règle a été revérifiée sur les sources officielles citées le 28 octobre 2026.
