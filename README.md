# fo-copilot — Copilot Plugin Marketplace for D365 F&O

Private repository that functions as a **marketplace for plugins and skills** for GitHub Copilot in our organization. This is where custom skills, agents, and hooks live for Microsoft Dynamics 365 Finance & Operations (X++) projects.

---

## Repository Structure

```
fo-copilot/
├── .github/
│   └── plugin/
│       └── marketplace.json        ← marketplace registry (required)
├── plugins/
│   └── fo-xpp/                     ← one plugin per domain
│       ├── plugin.json             ← plugin manifest
│       └── skills/
│           ├── x-plus-plus/
│           │   └── SKILL.md        ← X++ patterns and D365 F&O
│           └── azure-devops/
│               └── SKILL.md        ← Azure DevOps integration
└── README.md
```

---

## Main Files

### `.github/plugin/marketplace.json`

The only required file for the repo to be recognized as a marketplace by Copilot. Registers all available plugins and their metadata.

### `plugins/<name>/plugin.json`

Manifest for each plugin. Defines the name, version, author, keywords, and where the skills are located (and optionally agents, hooks, and mcpServers).

### `plugins/<name>/skills/<skill>/SKILL.md`

Instructions and context that Copilot uses when the skill is active. The YAML frontmatter (between `---`) defines the skill name and description.

---

## How to Add a New Skill

1. Create a folder in `plugins/<plugin>/skills/<new-skill>/`
2. Add the `SKILL.md` file with YAML frontmatter (`name`, `description`) and instructions for Copilot
3. Bump the version (see section below)
4. Commit and push to `main`

### Example Minimal SKILL.md

```markdown
---
name: my-skill
description: >
  Short description. Use when developer asks X or Y.
---

## Instructions for Copilot

- Rule 1
- Rule 2
```

---

## How to Add a New Plugin

1. Create the folder `plugins/<new-plugin>/`
2. Add `plugin.json` with the plugin metadata
3. Create skills inside `plugins/<new-plugin>/skills/`
4. Register the new plugin in `.github/plugin/marketplace.json` (array `plugins`)
5. Bump the version in both files
6. Commit and push to `main`

---

## Version Bumping — Required for Auto-Update

Whenever you add or modify a skill, **update the version in two places in the same commit**. Without bumping, developers won't receive the update automatically.

| File | Field to Update |
|---|---|
| `plugins/fo-xpp/plugin.json` | `"version"` |
| `.github/plugin/marketplace.json` | `"version"` in the plugin entry |

> ⚠️ If you only update one of them, auto-update may not work. Always update both simultaneously.

Example:
```
"version": "1.0.0"  →  "version": "1.1.0"
```

---

## How Developers Install / Update

### VS Code
Copilot detects updates in the background automatically when the version is bumped.

### CLI
```bash
# Install the marketplace (first time)
copilot plugin install fo-copilot

# Update a plugin manually
copilot plugin update fo-xpp
```

> The repo can be private — Copilot CLI and VS Code support private repos as long as the developer has access to the repository.

---

## Available Plugins

| Plugin | Description | Included Skills |
|---|---|---|
| `fo-xpp` | X++ skills and D365 F&O patterns | `x-plus-plus`, `azure-devops` |

---

## Contribution

1. Create a branch `feature/<description>`
2. Make changes (new skill, instruction update, etc.)
3. Bump the version in the two required files
4. Open a Pull Request to `main`
5. After merge, developers receive the update automatically (VS Code) or via `copilot plugin update` (CLI)