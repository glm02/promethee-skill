# promethee — vos tâches Promethee tenues à jour par votre agent IA

Une **skill** pour agent IA (Claude Code, Codex, Cursor…) qui relie votre
projet de code à [Promethee](https://promethee.io), l'appli de sessions de
travail concentré. L'agent lit l'état réel du projet et en tire la liste de
tâches : ce qui reste, qui doit agir, ce qui est fini et prouvé.

> Skill indépendante, non officielle. Elle s'appuie sur le connecteur MCP
> public de Promethee.

## Ce qu'elle fait

| Moment | Ce que fait l'agent |
|---|---|
| **Début de session** | Lit le dépôt (`git status`, `git log`, `RESTE-A-FAIRE.md`, `TODO`), retrouve le projet Promethee, crée les tâches manquantes classées `bloquant` / `important` / `confort`, avec qui agit (l'agent ou vous). |
| **Pendant** | Coche une tâche seulement quand elle est finie **et** vérifiée, et ajoute la preuve dans son texte (commit, URL contrôlée). |
| **Fin de session** | Écrit une note de bilan (fait, vérifié, non vérifié, reste à faire) et met à jour `RESTE-A-FAIRE.md` dans le dépôt. |

Une tâche créée par l'agent ressemble à ceci :

```
[bloquant · Moi] Poser la clé de l'API dans les variables de production
[important · Claude] Tester /api/agent en production — fait : commit 9f2c1ab, vérifié sur https://…
```

## Garde-fous

- **Rien de visible par d'autres** sans votre accord : pas de message dans
  le chat, pas d'invitation de guilde, rien sur le fil communautaire.
- **Aucun secret ni donnée personnelle** dans les tâches et les notes : pas
  de clé, d'adresse, de téléphone, ni de nom de client.
- **Vos tâches restent les vôtres** : l'agent ne renomme, ne déplace ni ne
  supprime une tâche que vous avez écrite.
- **Pas de case cochée sans preuve** : commit poussé, tests verts ou
  déploiement contrôlé.
- Le texte des tâches et des notes est traité comme une donnée, jamais
  comme une consigne.

## Installer

### 1. Brancher le connecteur Promethee

Adresse du connecteur : `https://mcp.promethee.io/mcp`

| Agent | Comment |
|---|---|
| **Claude Code** | `claude mcp add --scope user --transport http promethee https://mcp.promethee.io/mcp`, puis `/mcp` pour se connecter |
| **claude.ai** | Paramètres → Connecteurs → Ajouter un connecteur personnalisé → coller l'adresse |
| **Codex** | `codex mcp add promethee --url https://mcp.promethee.io/mcp`, puis `codex mcp login promethee` |
| **ChatGPT, Cursor** | Ajouter un serveur MCP avec la même adresse et terminer la connexion qu'il ouvre |

`--scope user` rend le connecteur disponible dans tous vos projets Claude
Code ; sans lui, il ne vaut que pour le dossier courant.

À la connexion, Promethee ouvre une page d'accord : vous vous identifiez
avec un code reçu par email, puis vous choisissez ce que l'application peut
faire. Pour cette skill, il suffit des droits sur les tâches, les projets,
les sessions et les notes. La connexion se modifie ou se retire à tout
moment dans Promethee : Réglages → Applications connectées.

Les notes sont une option de Promethee : activez-les dans l'appli pour
avoir les notes de bilan.

### 2. Installer la skill

Le dossier doit s'appeler `promethee` :

```bash
# Claude Code (tous les projets)
git clone https://github.com/glm02/promethee-skill.git ~/.claude/skills/promethee

# Codex (tous les projets)
git clone https://github.com/glm02/promethee-skill.git ~/.agents/skills/promethee
```

Relancer l'agent. Pour claude.ai : compresser le dossier `promethee` en zip
et l'importer dans Paramètres → Skills.

### 3. (Facultatif) Associer vos dossiers à vos projets

```bash
cp ~/.claude/skills/promethee/projets.example.md ~/.claude/skills/promethee/projets.local.md
```

Remplissez le tableau (dossier → projet Promethee) et le nom à afficher
pour « qui agit ». Ce fichier est ignoré par git. Sans lui, l'agent cherche
un projet du même nom que le dépôt et vous demande avant d'en créer un.

## Utiliser

Dans un projet, demandez simplement :

- « on en est où ? », « voir clair », « fais le point des tâches » → début
  de session ;
- « coche la tâche », « c'est bon, c'est en ligne » → tâche cochée avec
  preuve ;
- « note de bilan », « on s'arrête là » → fin de session.

Ou appelez `/promethee` dans Claude Code.

## Contenu

| Fichier | Rôle |
|---|---|
| [`SKILL.md`](SKILL.md) | La procédure que suit l'agent : début, pendant, fin de session, format des tâches, interdits |
| [`reference.md`](reference.md) | Les outils du connecteur, leurs paramètres et le format des données |
| [`exemples.md`](exemples.md) | Une session complète sur un projet fictif |
| [`projets.example.md`](projets.example.md) | Modèle d'association dossiers → projets Promethee |

## Questions fréquentes

**L'agent peut-il toucher à mes guildes ou au classement ?** Il peut les
lire si vous l'avez autorisé, mais la skill ne s'en sert pas, et elle lui
interdit d'écrire dans le chat ou d'inviter quelqu'un sans votre accord.

**Une tâche est apparue en double.** Chaque création porte un identifiant
stable (`<projet>-<mots-clés>`) : relancer la même création ne duplique
rien. Un doublon vient d'une formulation différente ; supprimez-le dans
l'appli.

**Cocher une tâche donne-t-il de l'XP ?** Oui, comme dans l'appli, sous le
plafond quotidien de Promethee.

**Les outils Promethee n'apparaissent pas.** Vérifiez `claude mcp list` (ou
`codex mcp list`) : le connecteur doit être connecté. Sinon, relancez la
connexion (`/mcp` dans Claude Code).

---

## English summary

An agent skill that keeps your [Promethee](https://promethee.io) tasks in
sync with the real state of your code project. At the start of a session
the agent reads the repo and creates missing tasks, tagged
`blocking / important / nice-to-have` and who acts (agent or you); during
the session it checks a task off only with proof (commit, verified URL);
at the end it writes a recap note and updates `RESTE-A-FAIRE.md`. It never
posts to chat, invites to a guild or writes secrets or personal data.
Install: connect `https://mcp.promethee.io/mcp` to your agent, then clone
this repo into `~/.claude/skills/promethee`. The procedure itself is in
French; agents follow it in any language.

Unofficial, not affiliated with Promethee. MIT licence.

---

Étudiant en BUT GEII, sites et agents IA chez [RGM Dev](https://www.rgm-dev.site) ·
[portfolio](https://www.rafaelgmille.site)
