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

## Provenance (donnée interne, ni filtre ni badge visible)
- **Enseignes** : tout acteur retail (pas seulement les clients Cegid)
- **Concurrents** : éditeurs et technologies concurrentes
- **Innovation marché** : tendances / cas d'usage sans acteur précis
- Champ `source_type` conservé sur chaque document pour un usage futur, mais plus affiché nulle part dans l'UI (ni filtre, ni badge sur les cartes) — jugé peu lisible en pratique.

## Onglet "Concurrents"
Annuaire des éditeurs/concurrents (collection `competitors`) : nom, logo (favicon officiel du site, ou monogramme si introuvable), lien vers le site, année de création, chiffre d'affaires sourcé (ou "Non communiqué" si privé/non public — jamais un chiffre inventé ou une estimation tierce non vérifiée présentée comme un fait), description courte, typologie(s), filtrable par typologie (même taxonomie que la veille).
- Maintenu par la tâche hebdomadaire pour les champs texte : à chaque run, tout acteur "validé" de la section Concurrents/éditeurs de `sources.md` sans fiche existante se voit créer une fiche minimale (nom, typologie, description). Jamais pour un simple candidat.
- Website/année/CA/logo sont ajoutés manuellement (par recherche ponctuelle), pas automatiquement par la tâche hebdomadaire.
- Une fiche existante n'est jamais réécrite automatiquement (pour préserver une correction manuelle).
- Logos : récupérés via le favicon/apple-touch-icon officiel du site (jamais un service tiers comme Clearbit — bloqué par le CSP de l'Artifact, et de toute façon indisponible). Stockés comme assets de l'Artifact (`/_blob/...`) pour la version Claude, et comme fichiers locaux dans `docs/data/logos/<id>.(png|svg)` pour la version GitHub Pages. Beaucoup de sites renvoient 403/404/429 sur ces tentatives (protection anti-bot) : pas de logo = repli automatique sur un monogramme, c'est un état normal et attendu, pas un bug.

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
- Une news par acteur/sujet, dédupliquée si reprise par plusieurs sites (vérification par `link` avant écriture).
- **Jamais de données d'exemple/fictives dans la base** : uniquement du réel, vérifié. (Les 6 news d'exemple du prototype initial ont été retirées le 12/11/2026.)
- **Jamais d'article payant/paywall** : contenu non lisible en entier = exclu.
- Date de publication vérifiée en ouvrant la page (jamais déduite).
- Lien vérifié valide.
- Résumé court (1 phrase) + résumé long (3-5 phrases) rédigés à partir de la page, jamais reproduits mot pour mot (pas de citation > 15 mots).
- Un nouvel acteur détecté est ajouté en "candidat" (à valider), jamais directement en source active. Liste de référence : `sources.md`.
- Fenêtre temporelle du run hebdomadaire normal : 10 jours (pas 7, pour absorber le décalage entre deux runs). Des runs exceptionnels ponctuels (backfill) peuvent élargir cette fenêtre (ex. 60 jours le 13/11/2026) sans modifier la tâche planifiée elle-même.

## Stockage & diffusion — double hébergement
L'outil tourne sur **deux fronts, un seul fichier HTML** (`site/index.html`, copié vers `docs/index.html`) :
1. **Page Claude (Artifact)** — https://claude.ai/artifact/FUFShXkNyp8LuaPmJhsBie — source de vérité, base de données live (`db` + `assets` capability) : lecture pour toute l'organisation, écriture réservée aux éditeurs (PM). C'est là que la tâche planifiée écrit.
2. **GitHub Pages** — https://brenaud-cegid.github.io/retail-watch/ — copie statique, **publique sur internet** (le dépôt GitHub est public). Au chargement, la page essaie `claude.use("db")` ; si indisponible (hors Claude), elle bascule sur `docs/data/news.json` et `docs/data/competitors.json`. Ces fichiers sont régénérés et poussés par la tâche planifiée (étape 8), pas modifiables manuellement en direct sur GitHub.
- Dépôt : `brenaud-cegid/retail-watch` (compte GitHub personnel du PM, pas une org Cegid). Poussé via **GitHub CLI (`gh`)**, authentifié une fois (`gh auth login`) — le connecteur GitHub intégré à Claude ne fonctionne pas pour l'écriture (bug connu, cf. historique de la session de création).
- Filtrage/tri : tout le filtrage (secteur, typologie, continent, pays, période) se fait **côté client**, sur un tableau chargé une fois (pas de requête réseau par filtre). Le "Charger plus" réaffiche juste une tranche plus grande du même tableau.
- FR/EN selon la langue du navigateur, thème clair/sombre selon la préférence système.
- Filtres repliables : repliés par défaut sous ~860px de large (où ils sont au-dessus de la liste) et au scroll dans ce cas précis uniquement ; toujours visibles en fenêtre large. Bouton flottant "↑ Filtres" pour remonter et les rouvrir.
- Charte graphique : Cegid Design System (Material) — voir design system partagé dans l'organisation.

## Tâche planifiée (`veille-retail-lundi`, SKILL.md, copie dans `tache-planifiee.md`)
Chaque lundi 7h31 (si l'app est ouverte, sinon au prochain démarrage) :
1. Lit `sources.md`, cherche les news des 10 derniers jours par recherches thématiques (pas acteur par acteur, trop nombreux) chez les acteurs validés, vérifie chaque source, écrit dans `news`.
2. Détecte les nouveaux acteurs → `sources_candidates` + `sources.md` (jamais validés automatiquement).
3. Crée une fiche `competitors` minimale pour tout concurrent validé qui n'en a pas (jamais pour un candidat, jamais de réécriture d'une fiche existante).
4. Écrit un bilan dans `run_log`.
5. Commit git local de `sources.md` si modifié.
6. **Régénère et pousse** `docs/data/news.json` + `docs/data/competitors.json` vers GitHub (`git push`, déjà authentifié) pour que la copie GitHub Pages reste synchronisée.
