# Outils et accès

Rappel : les connecteurs varient d'une session à l'autre. Avant d'affirmer pouvoir faire quelque chose, je vérifie ce qui est réellement branché.

## Disponibles dans la session cloud (constat du 2026-10-02)
- **GitHub** (dépôt `Starsamjarvisproject/assistante-`) : lecture et écriture, c'est ma mémoire.
- **Routines planifiées** (cron ou exécution unique) : permettent de me réveiller à une heure donnée. À tester.
- Connecteurs Gmail, Google Agenda, Google Drive : présents mais pas encore autorisés ni testés avec Sami. Il veut se détacher de Drive à terme.

## Manque / à construire
- Réveil permanent et notifications téléphone (mail reçu → téléphone) : nécessite un serveur toujours allumé (idée de Sami : serveur en ligne ou PC maison).
- Pont vers le CRM Prospect (serveur Google Apps Script, accès à fournir par Sami).
- Voix : voix féminine Gemini actuellement ; piste Cloudflare.
- Sauvegarde externe de la mémoire (serveur ou PC maison).

## Capacités confirmées par test (2026-10-02)
- **Créer une session** (outil `create_session`) dans mon propre environnement, mon dépôt et ma branche : testé, fonctionne. Recette dans `CLAUDE.md`.
- **Lire une session fille** (`get_session`, `list_events`) : fonctionne.
- **Ajouter un dépôt en lecture** (`add_repo`) : fonctionne ; `prospect-source` (cerveau de l'homologue) ajouté en lecture seule avec l'autorisation de Sami. Je n'y écris jamais.
