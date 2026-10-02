# Compétence : agenda et rendez-vous (dormante)

- Niveau opérationnel : 0 % (pas de pont avec le CRM)

## Outils de l'ancien système
- `agenda(jours)` : liste les rendez-vous et événements à venir (RDV des fiches et Google Agenda), 14 jours par défaut.
- `preparer_rdv(id_fiche, date, heure, type, note)` : prépare un rendez-vous sur une fiche (type : démo visio, rendez-vous physique, appel…).
- `preparer_modification_rdv(id_fiche, date, heure, note)` : modifie un rendez-vous existant.
- `preparer_suppression_rdv(id_fiche)` : supprime le rendez-vous d'une fiche.

Les actions étaient autrefois « préparées puis confirmées » (système de confirmation de Prospect, supprimé depuis).

## Procédure
Trouver la fiche, vérifier la date et l'heure, résumer en une phrase, puis exécuter. En cas de doute sur la date ou l'heure (dictée), demander.
Remarque : Google Agenda est un connecteur distinct, utilisable séparément si branché (voir `outils/`).
