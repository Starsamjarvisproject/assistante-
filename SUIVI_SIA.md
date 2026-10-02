# Historique SIA — extrait de SUIVI_FIXES.md (prospect-source)

Ce fichier est une **extraction** (pas une réécriture) de tout ce qui concerne SIA/l'assistant vocal
dans l'historique complet du projet, pour pouvoir reprendre le travail sur SIA sans recharger tout
`SUIVI_FIXES.md` de Prospect. En cas de doute sur un détail, l'original fait foi.

Extrait fait le 02/10/2026 par Claude Code, à la demande de Sami, dans le cadre de la séparation des
"cerveaux" Prospect / SIA en environnements cloud distincts.

---

## Lignée avant SIA — "Assistant vocal" (ElevenLabs)

### FIX 43 — Vraie intégration ElevenLabs pour l'Assistant vocal (20/09)
Poussé et déployé (`@131`). Fonction `genererAudioElevenLabs(texte, voiceId, nomConnecte)` — vrai
endpoint Text-to-Speech ElevenLabs (`eleven_multilingual_v2`). Sélecteur de voix sur l'Accueil : voix
choisies par Sami — **Stephyra** (1er choix), **Victoria** (2e), **Lucie** (3e), + "Voix gratuite"
(repli `speechSynthesis` navigateur). Un seul appel API pour tout le texte d'un coup (économie de
crédits). Bouton "Arrêter" coupe lecture ElevenLabs ou navigateur.

### FIX 55 — Voix déplacée en Paramètres, stockée par compte (21/09)
Poussé (`@142`). Avant : la voix choisie était stockée par **appareil** (localStorage), pas par
compte — Sami sélectionnait Bella sur PC (Admin) mais retrouvait une voix différente sur téléphone
(compte Sami). Corrigé : stockage par compte, "le gros sélecteur complet reste réservé à Admin, mais
chaque vendeur voit les 4-5 voix déjà pré-choisies."

### FIX 56 — Lexique de raccourcis pour l'Assistant vocal (21/09)
Poussé (`@143`). Sami utilise des raccourcis dans ses notes ("Rapp" = rappeler, "NrP" = ne répond pas,
"RDV" = rendez-vous, + cas contextuels "Mme Dir" → directrice, "Resp Info" → informatique) que
l'Assistant vocal lisait tels quels. Ajout de `LEXIQUE_VOIX` (index.html, près de
`lireBriefingVoix()`) : remplacement mot entier, insensible à la casse, ne touche jamais les mots qui
commencent pareil ("Rapp" ne touche pas "Rappel"). Copie lisible dans
`serveur/voix/LEXIQUE_ONOMATOPEES.md` (contenu copié ci-dessous).

**Contenu de `LEXIQUE_ONOMATOPEES.md` au 02/10** :
| Raccourci tapé | Dit à voix haute |
|---|---|
| Rapp | rappeler |
| NrP | ne répond pas |
| RDV | rendez-vous |

---

## Recherche architecture voix pas chère (21/09) — `serveur/voix/NOTES_VOIX.md`

Décision prise avec Sami le 21/09, pour le futur "projet suprême" (assistante autonome distincte de
Prospect) :
- **Pour SIA/Prospect aujourd'hui : aucun changement**, on reste sur les voix ElevenLabs/Gemini
  actuelles. Un briefing/dialogue vocal normal reste un usage trop léger pour justifier un hébergement
  externe dédié.
- **Pour le futur "projet suprême"** : architecture candidate retenue = **F5-TTS** (modèle open source
  le plus moderne, excellent en français) plutôt que XTTS v2, hébergé via une **API cloud payante à la
  demande** (Replicate/Fal.ai/RunPod serverless, facturé à la seconde de calcul GPU, ~0,10€/mois estimé
  pour un usage de quelques minutes/jour) — candidat retenu car zéro maintenance, pas de dépendance au
  PC de Sami allumé. Les deux autres options (Hugging Face Space gratuit, serveur local + Ngrok) ont
  été écartées (peu fiable / PC doit rester allumé).
- Apps Script ne peut pas faire tourner ces modèles lui-même (pas de Python/PyTorch/GPU, exécution max
  6 min) — il faudrait dans tous les cas un vrai serveur externe exposant une URL appelée via
  `UrlFetchApp.fetch()`.
- Point clarifié avec Sami : jamais question de cloner la voix réelle d'une personne identifiable sans
  son accord — écarté explicitement.
- Statut au 21/09 : en pause, Sami teste F5-TTS de son côté, rien à coder tant que le projet suprême
  n'a pas officiellement démarré.

---

## Naissance de SIA (Gemini Live)

### FIX 91 — Prototype assistante Gemini (24/09)
Codé en local, pas encore poussé à ce stade. Premiers tests d'intégration Gemini Live.

### FIX 91→98 — Résultats des tests (24/09)
Le micro est bloqué dans l'iframe Apps Script (confirmé par test direct + relayé par Gemini) — voix
fluide uniquement hors iframe. **Décision** : page vocale externe hébergée sur **GitHub Pages** (dépôt
public, aucun secret dedans), qui appelle Apps Script en HTTP. `doPost` (Code.gs) gère les actions
login/modeles/jeton/log (login limité à 5 échecs/15 min). Ton de l'assistante (consigne de Sami) :
chaleureuse, charmeuse, sensuelle mais strictement professionnelle, sans jamais dépasser la limite
fixée par Sami, tutoiement.
**✅ Validé en réel (24/09, 15:54)** sur `https://starsamjarvisproject.github.io/assistante-/` (dépôt
`Starsamjarvisproject/assistante-`, nom de fichier en minuscules — sensible à la casse sur Pages).
Micro OK, conversation fluide, interruption OK, transcription à l'écran OK, voix **Sulafat** choisie
par Sami. Seul défaut à l'époque : quelques secondes de préparation au démarrage.

### FIX 99–100 — Assistante branchée dans Jarvis (24/09)
Jeton Gemini 30 min, session 10 min. Démarrage accéléré (modèle mémorisé + jeton préparé en arrière-
plan). Page mise à jour via `gh` (portable, connecté au compte Starsamjarvisproject par Sami). Accueil :
nouvel encart "Assistante" (bouton Parler), retrait de l'ancien "Assistant vocal" ElevenLabs et du bloc
"Demander à Jarvis". Le bouton ouvre la page externe avec la session transmise (`#t=`, pas de 2e
connexion). Admin uniquement à ce stade.

### FIX 101–105 — Outils, mémoire, renommage, rechargement (24/09)
- Outils serveur : `chercher_fiche`, `lire_fiche`, `agenda`, `preparer_rdv` → `confirmer_action`/
  `annuler_action`. Page GitHub branchée (cartes fiche/agenda/confirmation), lien « Ouvrir dans
  Prospect » (`?fiche=ID`). Ouverte à tous les comptes (chacun ses fiches, Admin tout).
- **Mémoire en fichiers Drive, dossier "SIA"** (spec de Sami) : `SIA/BASE.md` (commun) + par compte
  `PROFIL.md` (ton/comportement/infos perso), `MEMOIRE_LONG_TERME.md` (résumés des 30 dernières
  sessions max), `HISTORIQUE.md` (1 ligne/session). Créés à la 1ère utilisation, lus au début de
  chaque conversation. Fin de session : transcription résumée par Gemini texte → alimente
  HISTORIQUE + MEMOIRE_LONG_TERME. Outils `retenir`/`oublier`, garde-fous (pas de mot de passe/donnée
  bancaire, 150 éléments max/fichier).
- Rechargement auto : vérification de version toutes les 20s + à chaque retour sur l'onglet (jamais
  pendant une conversation).
- Nom "SIA" / "Prospect" appliqué partout dans les libellés.

### FIX 108 — Skills de SIA (24/09, idée de Sami)
Dossier `SIA/skills/` : un `.md` par compétence + `INDEX.md` (seul chargé au démarrage, détail via
`lire_skill`). Skills communs à tous les comptes. Graines créées à la 1ère utilisation : creation_rdv,
creation_fiche_prospect, suppression, boutons_cliquables, calculs_rdv_pourcentages, recherche_leads,
agenda, memoire — avec statut réel (disponible/pas encore/interdit). SIA peut proposer un skill
(`proposer_skill`), écrit seulement après le "oui" de l'utilisateur, marqué "à valider par Sami",
journalisé. **Règle de sécurité : un skill est une note de procédure, il ne donne AUCUN pouvoir** — les
actions réelles restent les outils serveur (écriture = confirmation, aucune suppression directe).

### FIX 109 (25/09) — Règles de SIA fixées AVEC Sami
Correction d'une erreur de Claude (avait posé "suppression interdite" sans en parler à Sami d'abord) :
1. RDV : créer/modifier/supprimer possibles, toujours résumé + confirmation.
2. Skills : SIA propose, n'écrit qu'après le "oui", marqués "à valider par Sami" (trace).
3. `proposer_idee` → `SIA/PROPOSITIONS.md` (boîte à idées relue par Sami).
4. Suppression d'une fiche entière : UNIQUEMENT sur ordre explicite de Sami, réservé à son compte,
   confirmation obligatoire.
5. Vérité : ne jamais inventer ni mentir.
**Principe retenu : toute règle de comportement de SIA se décide avec Sami AVANT de coder.**

---

## Décisions complémentaires de Sami sur SIA (25/09, à respecter, ne pas re-demander)

1. **Mots de passe** : restent en clair dans `Code.gs` pour l'instant (Sami seul utilisateur, oublie
   parfois son mot de passe admin et Claude le lui redonne) — plus tard : empreintes chiffrées + accès
   séparé (approche déjà validée en principe).
2. **Auto-amélioration** : SIA règle seule son ton/voix/manière de parler (pour le compte connecté).
   Pour ses règles/skills : elle repère ses erreurs, explique ce dont elle a besoin, enregistre après
   "validation express" de Sami. Apprend des consignes répétées (2-3 fois) pour arrêter le récap
   systématique. **Le code reste réservé à Claude.**
3. **SIA = vendeur à part entière** dans Prospect : case vide → elle écrit ; donnée existante →
   confirmation explicite pour modifier/supprimer ; jamais d'invention ; doublon → elle le signale ;
   récap court avant d'agir au début (s'estompe avec l'apprentissage) ; suppression de fiche (même
   créée par erreur par elle) uniquement sur ordre.
4. **Tout dépend du compte connecté** : mémoire, fiches créées, droits suivent la session. Les actions
   de SIA doivent s'afficher à l'écran.
5. **iPhone** : quand SIA fait basculer l'écran vers une fiche, le micro se coupe (onglet en arrière-
   plan, constaté 24/09). Pistes : afficher le détail des fiches dans la page SIA elle-même, à terme
   héberger Prospect à côté de SIA (même page).

## FIX 114 (25/09) — SIA intégrée à Prospect (P2) : codé, non publié à l'époque
`PAGE_VOIX_GITHUB\sia-widget.js` (fichier séparé, chargé par `index.html` uniquement en version
hébergée via le pont HTTP). Bouton rond flottant déplaçable (gris hors ligne / orange connexion / vert
pulsant en ligne, pastille rouge = arrêt immédiat), panneau de conversation (cartes fiche/agenda/
confirmation), session de Prospect réutilisée (aucune 2e connexion), même mémoire/outils/règles/voix
que la page SIA autonome. Nouveaux outils locaux (exécutés dans la page) : `ou_suis_je`,
`ouvrir_fiche`, `aller_onglet`. Après une écriture confirmée, Prospect recharge les fiches. Jamais de
rechargement automatique pendant une conversation. Dette technique notée à l'époque : la page SIA
autonome duplique une partie du moteur (à factoriser plus tard — pas fait à ce jour).

**Depuis** : `sia-widget.js` est servi en production depuis `pf-jzmurkit` puis `prospect-front`
(Cloudflare) — confirmé fonctionnel en test (02/10).

---

## Avatar visage animé (idée de Sami, projet de fond, pas pour le salon)

Pistes issues d'une réponse de Gemini (**NON vérifiées par Claude** — liens/licences/GPU à contrôler
avant tout choix) : SadTalker, MuseTalk, Duix-Avatar, Agnes Video Generator, Open Generative AI ;
installation via Pinokio ou Google Colab. Contrainte connue : la plupart demandent un GPU Nvidia ; pour
un rendu temps réel synchronisé sur la voix Gemini Live il faudrait un serveur dédié (lié à l'option
hébergement externe). Vérifier aussi le droit d'usage du visage (image générée, pas une vraie
personne) et la licence de chaque outil.

---

## Vision "projet suprême" / arène multi-IA (02/10, nouvelle précision de Sami)

Voir `CLAUDE.md` de ce dépôt, section "Vision long terme" — objectif : assistante autonome, multi-IA
en arène (plusieurs IA traitent une question, une IA principale arbitre ou présente les options),
accessible mobile, triage mails, recherche web, création de contenu. Pas cadré, pas démarré, à traiter
dans une session dédiée avant tout code.
