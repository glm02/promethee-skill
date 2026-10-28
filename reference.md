# Référence — outils du connecteur Promethee

Relevé le 28/10/2026 sur `https://mcp.promethee.io/mcp`. Les noms
d'outils sont ceux du serveur ; l'agent les voit préfixés
(`mcp__promethee__me`, etc.).

## Conventions

- Dates : `YYYY-MM-DD`, dans le fuseau de l'utilisateur (donné par `me`).
- Heures : millisecondes depuis 1970, champs terminés par `_ms`.
- Listes paginées : passer `next_cursor` en `cursor` pour la page suivante.
- `locked_before_window: true` : une partie de l'historique n'est pas
  accessible avec ce compte.
- Créations idempotentes : le même `client_request_id` renvoie l'objet déjà
  créé au lieu d'un doublon ; le réutiliser avec un autre contenu est
  refusé.

## Lecture

| Outil | Rôle | Paramètres utiles |
|---|---|---|
| `me` | Profil, niveau, XP, série, fuseau, session en cours, bilan du jour | — |
| `list_projects` | Projets avec le nombre de tâches ouvertes | — |
| `list_tasks` | Tâches, plus récentes d'abord | `status` (`open`, `completed`, `all`), `project_id`, `scheduled_date`, `limit` ≤ 100, `cursor` |
| `get_task` | Une tâche | `task_id` |
| `list_sessions` | Sessions terminées (7 derniers jours par défaut, 92 max) | `from_ms`, `to_ms`, `limit`, `cursor` |
| `get_session` | Une session | `session_id` |
| `list_notes` | Notes du jour et notes de session | `from_ms`, `to_ms`, `limit` |
| `get_report` | Rapport de concentration | voir le schéma de l'outil |

## Écriture utilisée par le skill

| Outil | Rôle | Paramètres |
|---|---|---|
| `create_project` | Créer un projet (1 à 80 caractères) | `name`, `client_request_id` |
| `create_task` | Créer une tâche (1 à 500 caractères) | `text`, `project_id`, `scheduled_date`, `client_request_id` |
| `update_task` | Changer texte, projet ou date | `task_id`, `text`, `project_id`, `scheduled_date`, `clear_project`, `clear_scheduled_date` |
| `complete_task` | Cocher, ou rouvrir avec `completed: false` ; renvoie l'XP gagnée | `task_id`, `completed` |
| `add_daily_note` | Ajouter à la note du jour (4000 caractères) | `text`, `date`, `mode` (`append` par défaut, `replace` écrase la journée) |
| `add_session_note` | Note attachée à une session, celle en cours comprise | `session_id`, `text` |

## Outils que le skill n'utilise pas

`leaderboard`, `guild_report`, `guild_standings`, `search_guilds`
(lecture, sans risque) ; `send_chat_message`, `invite_to_guild` (visibles
par d'autres personnes : jamais sans accord explicite).

## Exemple de tâche renvoyée

```json
{
  "id": "8ee29e9c-…",
  "text": "[important · Claude] Tester /api/agent en production — fait : commit 4bdcabe",
  "completed": true,
  "completed_at_ms": 1793208782946,
  "project_id": "dbf0bf28-…",
  "project_name": "MonProjet",
  "scheduled_date": null,
  "recurrence": null,
  "current_streak": 0,
  "source": "agent",
  "session_id": null,
  "created_at_ms": 1793208778284,
  "updated_at_ms": 1793208782946
}
```

`complete_task` renvoie en plus `xp_awarded` (0 une fois le plafond
quotidien atteint).
