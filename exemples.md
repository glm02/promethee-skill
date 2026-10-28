# Exemple d'une session

Projet fictif : une application web `mon-saas`, projet Promethee
« MON SAAS ».

## Début : « voir clair »

L'utilisateur ouvre la session et écrit « on en est où ? ».

1. `me` → fuseau `Europe/Paris`, une session Promethee tourne.
2. Dépôt : rien en cours dans `git status`, dernier commit `61f1429`.
   `RESTE-A-FAIRE.md` liste : base de données à créer, clé d'API à poser,
   relier la visite au devis, activer les statistiques.
3. `list_projects` → « MON SAAS » existe ; `list_tasks` → deux tâches
   écrites à la main : « connecter les env », « passer en prod ».
4. Rapprochement :
   - « connecter les env » couvre la clé d'API → pas de doublon ;
   - les autres points manquent → trois `create_task`.

Réponse à l'utilisateur :

```
Bloquant
- Moi : créer la base de données (région Europe)
- Moi : poser la clé d'API en production (déjà dans ta tâche « connecter les env »)
Important
- Claude : relier la visite au devis ← je commence par ça
- Moi : activer les statistiques du site
Ta tâche « passer en prod » est large : je la laisse telle quelle.
```

## Pendant : cocher avec une preuve

Le lien visite → devis est fini, tests verts, déployé et vérifié en ligne.

1. `update_task` → texte :
   `[important · Claude] Relier la visite au devis — fait : commit 9f2c1ab, vérifié sur https://mon-saas.example/demo`
2. `complete_task` → `xp_awarded: 10`.

Au passage, un bouton déborde sur téléphone : nouvelle tâche
`[confort · Claude] Corriger le débordement du bouton « Payer » en 390 px`.

## Fin : la note de bilan

Une session Promethee tourne → `add_session_note` :

```
MON SAAS — bilan
Fait : visite reliée au devis (commit 9f2c1ab)
Vérifié : tests, build, prod https://mon-saas.example/demo
Non vérifié : affichage sur iPhone réel
Reste : [bloquant · Moi] créer la base ; [bloquant · Moi] clé d'API ; [confort · Claude] bouton « Payer » en 390 px
```

Puis `RESTE-A-FAIRE.md` est mis à jour et commité, et chaque ligne « Reste »
a sa tâche ouverte dans Promethee.
