# Shared Obsidian Subvault for TFM Prior project

## Installation setup

1. Clone this repository into `<main vault root>/Shared/tfm_prior_subvault`, i.e. it should live inside a `Shared/` folder within your main Obsidian vault.
2. Open your main vault in Obsidian, or reload it if it was already open, so Obsidian picks up the new folder.
3. Install and configure the community plugins listed in [Plugin settings](#plugin-settings).

## Subvault structure
This Obsidian subvault should live in a 'main vault' such that all your customizations (such as themes and plugin settings) are not part of the subvault. In order for this work, this subvault `tfm_prior_subvault` should live as `<main vault root>/Shared/tfm_prior_subvault`, and some plugin in the main vault should be set (discussed below). Please note that:
- The term'subvault' is not a widely-used term within the Obsidian community.
- Some internal Obsidian note links should be the full path instead of just the note name, because there might also be a note with the same name in the vault (outside of the subvault.)

Within the subvault, we have the following structure:

| Folders              | Explanation                                                                                 |
| -------------------- | ------------------------------------------------------------------------------------------- |
| `Calender/Meetings/` | Meeting notes. Should always start with American date (such as '26-07-21 - TFM prior team') |
| `Cards/`             | Knowledge notes                                                                             |
| `Extras/Bases/`      | Obsidian Bases that provide database-like views over the vault                              |
| `Extras/Files/`      | Attachments (such as figures)                                                               |
| `Sources/`           | References to papers. Use the Zotero citation key as the note title.                        |
| `Tasks/`             | Task notes (one task per note).                                                             |

There are nested folders by design, as organically some more folders will emerge. The above system is loosely based on Zettelkasten.

### Folder details
Some more details on the above folders are discussed below:


#### Tasks

Each task lives as its own note in `Tasks/`. A task note uses the following frontmatter:

```yaml
---
status:
creation date: 26-07-21
---
```

- `status` — the current state of the task (leave empty when not yet started).
- `creation date` — the American date the task was created (such as '26-07-21').

The note body describes the task itself.


#### Meetings

Meeting notes live in `Calender/Meetings/`. Each note title should always start with the American date, followed by a short description of the meeting, for example:

```
26-07-21 - TFM prior team
```

Starting every title with the date (in `YY-MM-DD` order) keeps meetings sorted chronologically in the file list.



## Plugin settings

For the above system to work, one should set the following plugin as follows.

#### Attachment management
[URL](obsidian://show-plugin?id=attachment-management)

In its settings:
- Date format: `YYYYMMDDHHmmss`
- "Extension" →
	- Root path to save attachment: `In the folder specified below`
	- Root folder: `Shared/tfm_prior_subvault`
	- Attachment path: `Extras/Files`
	- Attachment format: `Pasted image ${date}`



#### Citations Extended
[URL](obsidian://show-plugin?id=citation-extended)

In its settings:
- Citation databases →
	- Database 1: `TFM Pior`
	- Database source: `Zotero (local API)`
	- Group library ID: `6602565`
- Literature notes →
	- Literature notes folder: `Shared/tfm_prior_subvault/Sources`
- Literature note templates
	- Literature note content template file: `Shared/tfm_prior_subvault/Extras/Templates/citation-content-template.md`


#### Bases
[Bases](https://help.obsidian.md/bases) are Obsidian's native way to render notes as a database-like table. The `.base` files live in `Extras/Bases/`.