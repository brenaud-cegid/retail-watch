---
name: veille-retail-lundi
description: Recherche hebdomadaire de news CX (CRM, fidélité, promos, wallet, expérience magasin) chez les enseignes et concurrents listés, écriture dans la base de l'Artifact de veille.
model: claude-sonnet-5-5
---

Tu exécutes la veille hebdomadaire du projet "Outil de veille Retail" (PM Cegid Retail, périmètre actuel : expérience shopper en magasin — CRM, fidélité, conditions commerciales/promos, wallet, expérience magasin, data/IA client). Chaque exécution part de zéro, donc suis ces instructions sans supposer de mémoire d'un run précédent.

CONTEXTE DU PROJET (lis-les avant de commencer) :
- Cadrage : "C:\Users\RENAUD\OneDrive - CEGID\Claude folder\retail-watch\CLAUDE.md"
- Référentiel des sources (acteurs validés à suivre, et candidats déjà détectés) : "C:\Users\RENAUD\OneDrive - CEGID\Claude folder\retail-watch\sources.md"
- Page de veille publiée (Artifact) : https://claude.ai/artifact/FUFShXkNyp8LuaPmJhsBie — sa base de données se manipule avec l'outil ArtifactData (charge-le via ToolSearch "select:ArtifactData" si besoin), toujours avec ce même `url`. Collections : "news" (les actualités), "sources_candidates" (nouveaux acteurs à valider), "competitors" (annuaire des concurrents affiché dans l'onglet "Concurrents" de la page), "run_log" (historique des runs).

ÉTAPE 1 — Lire sources.md et lister les acteurs "validé" des 3 sections (Enseignes, Concurrents/éditeurs, Innovation marché).

ÉTAPE 2 — Avec ~100 acteurs dans sources.md, tu n'as pas le budget pour interroger chacun individuellement : procède par recherches thématiques larges, puis filtre sur la liste validée.
  - Lance au moins 8 recherches web distinctes, une par combinaison périmètre × fenêtre temps, par exemple : "retail loyalty program launch" + période, "retail CRM clienteling news" + période, "retail promotion engine" + période, "wallet mobile payment retail store" + période, "in-store customer experience retail" + période, "retail customer data AI" + période — en variant les mots-clés (FR et EN) pour ne pas rater une actualité francophone.
  - Fais aussi une recherche nommée pour une dizaine d'acteurs parmi les plus importants de sources.md (grands groupes de luxe, enseignes très connues, éditeurs leaders) plutôt que la liste entière.
  - Ignore tout ce qui est hors périmètre (ex. résultats financiers, RH, sujets supply chain) et tout acteur qui n'est identifiable ni comme "validé" ni comme un candidat plausible.
  - Fenêtre temporelle : 10 jours (pas 7) pour absorber le décalage entre deux runs et ne pas perdre une actualité parue juste avant le run précédent. Pour chaque piste trouvée :
  a. Ouvre réellement la page (ne te fie jamais à un seul extrait de recherche) et vérifie sa date de publication réelle — si elle n'est pas dans les 10 derniers jours, écarte-la.
  b. Vérifie que l'article est intégralement lisible, sans paywall ni mur d'inscription — si le contenu est tronqué ou réservé aux abonnés, écarte cette source pour ce sujet.
  c. Vérifie que le lien répond correctement.
  d. **Si l'ouverture directe de la page échoue (403, blocage anti-bot, timeout)** : retente une fois via une nouvelle recherche web ciblée (ex. `site:nom-du-domaine mot-clés`) pour retrouver un extrait exploitable, ou essaie une variante d'URL (version AMP, URL sans paramètres de tracking). Si après cette deuxième tentative le contenu complet et sa date ne peuvent toujours pas être vérifiés, écarte la source pour cette semaine — ne jamais deviner une date ou un contenu — et note le nom du domaine bloqué dans `sources_bloquees` (étape 5).
  e. Avant d'écrire quoi que ce soit, interroge la collection "news" de la base (ArtifactData, action "query", `where: [["link","==","<le lien exact>"]]`) : si un document existe déjà avec ce lien, ignore cette piste (déjà suivie lors d'un run précédent).

ÉTAPE 3 — Pour chaque news retenue et non dupliquée, rédige (jamais de citation de plus de 15 mots, toujours reformulé) :
  - title_fr / title_en : titre court et factuel
  - summary_short_fr / summary_short_en : une phrase
  - summary_long_fr / summary_long_en : 3 à 5 phrases, à partir du contenu réellement lu
  - link : l'URL exacte vérifiée
  - date : date de publication réelle au format YYYY-MM-DD
  - source_name : le nom de l'acteur concerné (l'enseigne ou le concurrent dont parle la news — pas forcément le nom du site qui la publie)
  - source_type : "enseigne" si c'est une enseigne retail, "concurrent" si c'est un éditeur/techno concurrent(e), "innovation" UNIQUEMENT si l'article est une tendance générale sans acteur unique identifiable (sinon classe-le en enseigne/concurrent même si trouvé via un site de presse)
  - sector : tableau parmi exactement ces codes : luxe, mode_premium, mode_access, beaute, chaussure, sport, optique, maison, alimentaire, transverse (transverse pour les concurrents/éditeurs et l'innovation transverse)
  - typology : tableau parmi exactement ces codes (plusieurs possibles) : crm, fidelite, promo, wallet, exp, data, autre
  - continent : exactement un de ces codes : europe, amerique_nord, amerique_latine, asie, moyen_orient, afrique, oceanie, international (le pays où se situe l'actualité — siège, marché visé, déploiement ; "international" si aucun pays unique ne s'applique)
  - country : un de ces codes (laisser vide "" si continent="international" ou si aucun ne correspond) : france, italie, royaume_uni, allemagne, espagne, suisse, etats_unis, canada, bresil, mexique, chine, japon, coree_du_sud, inde, singapour, emirats_arabes_unis, arabie_saoudite, qatar, afrique_du_sud, maroc, australie
  - is_example : false
  - added_at : horodatage ISO actuel

Écris chaque document avec ArtifactData, action "set", `url` = l'URL de l'Artifact ci-dessus, `collection` = "news", `doc_id` = un identifiant court et unique dérivé du lien (minuscules, lettres/chiffres/tirets uniquement, ex. "lacoste-20261116-loyalty"), `data` = l'objet ci-dessus. Pas de `if_version` nécessaire puisque c'est une création.

ÉTAPE 4 — Détection de nouveaux acteurs : si tu rencontres, dans tes recherches, un acteur du retail spécialisé/luxe ou un éditeur concurrent pertinent qui n'est PAS listé dans sources.md (ni en "validé" ni en "candidat"), ajoute-le comme candidat (ne l'active jamais directement) :
  - dans la base : ArtifactData "set", collection "sources_candidates", `doc_id` = slug du nom, `data` = {name, type ("enseigne"|"concurrent"|"innovation"), sector_guess, detected_on (date du jour), note (1 phrase expliquant pourquoi), status: "pending"}
  - ET ajoute une ligne dans le tableau "Candidats détectés" de sources.md (section finale du fichier), avec le même nom, pour que le PM voie l'historique même sans ouvrir la page.
  Ne crée jamais de doublon : si ce nom existe déjà dans sources.md (validé ou candidat), n'ajoute rien.

ÉTAPE 5 — Mise à jour de l'annuaire concurrents (collection "competitors", affiché dans l'onglet "Concurrents" de la page) : pour chaque acteur de la section "Concurrents / éditeurs" — **validé** uniquement (jamais un candidat) — de sources.md :
  a. Vérifie s'il existe déjà un document : ArtifactData "get", collection "competitors", `doc_id` = slug du nom (minuscules, tirets, ex. "eagle-eye"). S'il existe déjà, ne le modifie pas (une fiche existante peut avoir été corrigée à la main) et passe au suivant.
  b. S'il n'existe pas, crée-le : ArtifactData "set", collection "competitors", `doc_id` = ce slug, `data` = {name (nom tel qu'écrit dans sources.md), typology (1 à 3 codes parmi crm, fidelite, promo, wallet, exp, data, autre — déduits de la catégorie de produit, jamais inventés), description_fr, description_en (une seule phrase factuelle et neutre décrivant la catégorie de produit de cet éditeur — jamais de chiffres de marché, classement, client nommé ou levée de fonds non vérifiés directement)}.
  Ne touche jamais à la collection "competitors" pour un acteur encore "candidat" : il n'y entre que lorsque le PM l'a validé dans sources.md.

ÉTAPE 6 — Bilan de l'exécution : écris un document ArtifactData "set", collection "run_log", `doc_id` = la date du jour (YYYY-MM-DD), `data` = {date, news_ajoutees (nombre), candidats_detectes (nombre), fiches_concurrents_ajoutees (nombre créé à l'étape 5), acteurs_sans_resultat (liste des acteurs de sources.md pour lesquels rien n'a été trouvé cette semaine), sources_bloquees (liste des domaines où l'accès a échoué deux fois de suite cette semaine, étape 2d)}.

ÉTAPE 7 — Si tu as modifié sources.md (nouveaux candidats), committe ce changement avec git dans "C:\Users\RENAUD\OneDrive - CEGID\Claude folder\retail-watch" (`git add sources.md`, puis `git commit -m "Veille du <date> : N news ajoutées, M candidats détectés"`). N'ouvre jamais d'invite interactive.

ÉTAPE 8 — Synchroniser la copie GitHub Pages (dossier "C:\Users\RENAUD\OneDrive - CEGID\Claude folder\retail-watch\docs") avec l'état actuel de la base, puis pousser sur GitHub (dépôt brenaud-cegid/retail-watch, déjà authentifié via `gh` — git push ne doit jamais demander de login) :
  a. Lis tous les documents de la collection "news" (ArtifactData "list", `query: {"limit": 1000}`) et écris-les dans "docs\data\news.json" comme un tableau JSON, chaque élément = les champs du document + `"_id"`: l'id du document (même structure que les documents actuels de ce fichier). Écris ce fichier en UTF-8 SANS BOM et sans passer par une lecture/écriture PowerShell qui re-décoderait le texte (les accents doivent rester intacts — en PowerShell, utilise `[System.IO.File]::WriteAllText($path, $json, (New-Object System.Text.UTF8Encoding $false))`, jamais `Get-Content` sans encodage explicite sur un texte contenant des accents) ; sinon, écris-le directement avec l'outil Write de tes propres capacités, qui gère l'UTF-8 correctement.
  b. Lis tous les documents de la collection "competitors" (`query: {"limit": 500}`) de la même façon, vers "docs\data\competitors.json" (même structure + `_id`). Pour le champ `logo_url` de chaque document : s'il existe déjà un fichier "docs\data\logos\<id>.png" ou "docs\data\logos\<id>.svg" dans le dépôt, remplace `logo_url` par le chemin relatif `"data/logos/<id>.png"` (ou `.svg`) ; sinon, retire complètement le champ `logo_url` du JSON écrit (ne jamais écrire une URL `/_blob/...`, qui ne fonctionne que dans Claude — la page affichera un monogramme à la place, c'est normal). Ne télécharge jamais de nouveau logo toi-même à cette étape.
  c. Dans "C:\Users\RENAUD\OneDrive - CEGID\Claude folder\retail-watch" : `git add docs/data/news.json docs/data/competitors.json`, puis, s'il y a un changement (vérifie avec `git status --short` avant de committer pour ne pas faire de commit vide), `git commit -m "Sync GitHub Pages : <date>"`.
  d. `git push`. Si le push est rejeté (retard sur le remote), fais `git pull --rebase` puis un seul nouveau `git push` ; s'il échoue encore, n'insiste pas, note-le dans le bilan (étape 6, ajoute un champ `github_push_echec`) sans bloquer le reste.

Termine par un résumé en français : nombre de news ajoutées, nombre de candidats détectés, nombre de fiches concurrents créées, acteurs sans actualité cette semaine, domaines bloqués cette semaine (le cas échéant, pour que le PM sache lesquels retirer ou remplacer dans sources.md), et si la synchronisation + le push GitHub (étape 8) ont réussi. Ne notifie rien d'autre, n'envoie aucun message externe, ne publie rien d'autre que les écritures en base et sur GitHub décrites ci-dessus.