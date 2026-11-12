# Outil de veille — Cegid Retail

> Nom volontairement générique : l'outil est conçu pour couvrir, à terme, tout le périmètre Cegid Retail. Pour l'instant, le périmètre actif reste celui ci-dessous (expérience shopper en magasin), porté par ce PM.

## Périmètre actuel
Expérience shopper **en magasin**, sur les briques :
- **CRM** (clienteling, profil client, data client, vendeur connecté)
- **Fidélité** (programmes, points, niveaux, avantages)
- **Conditions commerciales** (promotions, remises, moteurs de promo, politique de prix)
- **Wallet & paiement mobile** (cartes/coupons dans wallet)
- **Expérience magasin** (parcours in-store, omnicanal magasin, click & collect, retours)
- **Data & IA appliquées au client** (recommandations, analytics client, IA conversationnelle en magasin)

Cible : **retail spécialisé et luxe** (pas le mass market). Extension prévue, dans un second temps, à tout le périmètre Cegid Retail (au-delà de la seule expérience client) : le nom, la structure de données et l'architecture de la page sont déjà pensés pour accueillir d'autres périmètres (ex. supply chain, finance magasin) sans refonte.

## Provenance (donnée, pas un filtre)
- **Enseignes** : tout acteur retail (pas seulement les clients Cegid)
- **Concurrents** : éditeurs et technologies concurrentes
- **Innovation marché** : tendances / cas d'usage sans acteur précis
- Conservée sur chaque news (badge), mais retirée des filtres de l'onglet Veille (jugée peu utile en pratique).

## Onglet "Concurrents"
Annuaire des éditeurs/concurrents (collection `competitors`) : nom + description courte + typologie(s), filtrable par typologie (même taxonomie que la veille). Maintenu par la tâche hebdomadaire : à chaque run, tout acteur "validé" de la section Concurrents/éditeurs de `sources.md` sans fiche existante se voit créer une fiche (jamais pour un simple candidat). Une fiche existante n'est jamais réécrite automatiquement (pour préserver une correction manuelle).

## Nomenclature sectorielle (filtre 2)
- Luxe (maroquinerie, joaillerie, horlogerie)
- Mode & prêt-à-porter premium
- Mode & accessoires (non luxe)
- Beauté & parfumerie sélective
- Chaussure & maroquinerie courante
- Sport & loisirs
- Optique & santé visuelle
- Maison & arts de la table
- Alimentaire premium
- Éditeurs & transverse (concurrents, innovation)

## Géographie (filtre, continent puis pays)
- Continents : Europe, Amérique du Nord, Amérique latine, Asie, Moyen-Orient, Afrique, Océanie, International (annonce globale sans pays précis)
- Pays (liste restreinte aux marchés retail/luxe pertinents, enrichie si besoin) : France, Italie, Royaume-Uni, Allemagne, Espagne, Suisse (Europe) · États-Unis, Canada (Amérique du Nord) · Brésil, Mexique (Amérique latine) · Chine, Japon, Corée du Sud, Inde, Singapour (Asie) · Émirats arabes unis, Arabie saoudite, Qatar (Moyen-Orient) · Afrique du Sud, Maroc (Afrique) · Australie (Océanie)
- Une news porte le pays où se situe l'actualité (siège, marché visé, déploiement) ; "International" si aucun pays unique ne s'applique.

## Typologie (filtre 3, multi-valeurs)
- CRM
- Fidélité
- Promo & conditions commerciales
- Wallet & paiement mobile
- Expérience magasin
- Data & IA client
- Autre

## Règles de collecte
- Une news par acteur/sujet, dédupliquée si reprise par plusieurs sites.
- **Jamais d'article payant/paywall** : contenu non lisible en entier = exclu.
- Date de publication vérifiée en ouvrant la page (jamais déduite).
- Lien vérifié valide.
- Résumé court (1 phrase) + résumé long (3-5 phrases) rédigés à partir de la page, jamais reproduits mot pour mot (pas de citation > 15 mots).
- Un nouvel acteur détecté est ajouté en "candidat" (à valider), jamais directement en source active. Liste de référence : `sources.md`.

## Stockage & diffusion
- Base de données de l'Artifact (`db` capability) : lecture pour toute l'organisation, écriture réservée aux éditeurs (PM).
- Lazy loading : fenêtre de résultats élargie par pas de 20 (pas de chargement complet).
- FR/EN selon la langue du navigateur, thème clair/sombre selon la préférence système.
- Charte graphique : Cegid Design System (Material) — voir design system partagé dans l'organisation.

## Tâche planifiée
Chaque lundi matin : parcourir `sources.md`, chercher les news de la semaine écoulée par acteur, vérifier dates/liens, écrire dans `news`, signaler les nouveaux acteurs candidats dans `sources_candidates`.
