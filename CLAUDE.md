# SIA — Instructions Claude Code

Ce dépôt (`assistante-`) est le "cerveau" dédié au projet **SIA**, séparé du projet **Prospect**
(dépôt `prospect-source`). Sami veut à terme en faire une assistante IA autonome, conversationnelle,
multi-outils — distincte du CRM Prospect même si elle y est aujourd'hui intégrée comme fonctionnalité.

**Mémoire globale (niveau compte, pas ce fichier) : qui est Sami, comment il travaille, ses règles de
méthode (dictée vocale, précision avant d'agir, perfectionnisme).** Ce `CLAUDE.md` ne contient QUE ce
qui est spécifique à SIA — pas de doublon des règles générales.

## ⚠️ Important : le code de SIA n'est PAS isolé de Prospect aujourd'hui

Le backend de SIA (`doPost`, `outilAssistante`, outils `preparer_*`/`confirmer_action`, etc.) vit
**dans le même `Code.gs` que Prospect**, hébergé dans `prospect-source`. Ce dépôt (`assistante-`) ne
contient que la page vocale autonome (`index.html`, client léger qui appelle ce même backend).
**Toute modification du comportement réel de SIA nécessite donc d'avoir aussi accès à `prospect-source`.**
Séparer complètement le code de SIA dans son propre projet Apps Script serait un chantier à part,
jamais démarré — ne pas le supposer fait.

## Historique condensé (détail complet dans `SUIVI_SIA.md` de ce dépôt)

- Nom : **SIA** (Supreme Intelligence Assistant). Jamais "Paula"/"Pola"/"Jarvis". Prénom définitif pas
  encore choisi par Sami.
- Ancêtre : un "Assistant vocal" plus simple existait avant SIA (lecture du briefing via ElevenLabs,
  voix Bella/Stephyra/Victoria/Lucie, lexique de raccourcis) — remplacé par SIA (Gemini Live) mais le
  code ElevenLabs existe peut-être encore quelque part, à vérifier/archiver si pas déjà fait.
- SIA actuelle : Gemini Live (modèle `gemini-3.8-live`, voix Sulafat), page externe GitHub Pages
  (le micro est bloqué dans l'iframe Apps Script), + widget flottant intégré directement dans Prospect
  (`sia-widget.js`, chargé uniquement en version hébergée hors Apps Script).
- Mémoire de SIA : fichiers markdown sur Google Drive, dossier `SIA/` — `BASE.md` (commun à tous les
  comptes) + un sous-dossier par compte (`PROFIL.md`, `MEMOIRE_LONG_TERME.md`, `HISTORIQUE.md`).
  **Ceci est la mémoire DE SIA (l'assistante vocale dans l'app), différente de la mémoire de Claude
  Code (moi) sur ce dépôt — ne pas confondre les deux.**
- Outils de SIA : lecture libre + écritures soumises à confirmation (`preparer_*` → `confirmer_action`).
  Suppression de fiche entière : uniquement sur ordre explicite de Sami.
- Skills de SIA : `SIA/skills/` sur Drive, proposés par SIA puis écrits après validation explicite de
  l'utilisateur — jamais écrits directement par SIA de son propre chef.

## Vision long terme ("projet suprême", pas encore cadré ni démarré)

Objectif final de Sami (ses mots, 02/10) : une assistante autonome multi-IA, capable de :
- s'auto-comprendre et apprendre de son propre chef (vidéos, photos, infos cherchées seule) ;
- orchestrer plusieurs IA en "arène" sur une même question/problème (chaque IA traite de son côté,
  une IA principale arbitre ou présente les options à Sami) ;
- être accessible depuis téléphone/ordinateur (app mobile dédiée), trier les mails, chercher des
  informations, créer des pages web, etc. — une vraie assistante personnelle.

**Ce n'est PAS la suite naturelle de ce qui existe aujourd'hui** (SIA = Gemini Live intégrée à
Prospect) — c'est un chantier séparé, à cadrer en plusieurs phases avant d'écrire la moindre ligne de
code dessus. Pistes déjà notées côté TTS (voix open-source auto-hébergée, F5-TTS/XTTS) dans
`serveur/voix/NOTES_VOIX.md` de `prospect-source` — pas encore copiées ici, à rapatrier si ce chantier
démarre réellement.

## Règles d'or (héritées de Prospect, s'appliquent ici aussi)

1. Jamais de push/déploiement sans accord explicite de Sami, à chaque fois.
2. Claude n'entre jamais de mot de passe dans un champ de connexion.
3. Vérifier avec de vraies données avant d'affirmer que quelque chose fonctionne.
4. Toute règle de comportement/sécurité de SIA (ce qu'elle peut faire, écrire sans validation) se
   décide AVEC Sami avant de coder — jamais posée par défaut.
