# Local Claude Code Plugins

A local marketplace for custom Claude Code plugins.

## Available Plugins

| Plugin | Description |
|--------|-------------|
| **ralph-dev** | PRD-driven feature development with fresh context isolation per task |

## Installation

### Step 1: Clone this repository

```bash
git clone <repo-url> c:/path/to/your/plugins
```

### Step 2: Register the marketplace

Add the marketplace entry to your Claude Code configuration.

#### Windows

Edit `C:\Users\<username>\.claude\plugins\known_marketplaces.json` and add:

```json
{
  "local-plugins": {
    "source": {
      "source": "directory",
      "path": "c:/path/to/your/plugins"
    },
    "installLocation": "c:/path/to/your/plugins",
    "lastUpdated": "2026-01-14T00:00:00.000Z",
    "autoUpdate": true
  }
}
```

#### macOS/Linux

Edit `~/.claude/plugins/known_marketplaces.json` and add:

```json
{
  "local-plugins": {
    "source": {
      "source": "directory",
      "path": "/path/to/your/plugins"
    },
    "installLocation": "/path/to/your/plugins",
    "lastUpdated": "2026-01-14T00:00:00.000Z",
    "autoUpdate": true
  }
}
```

Replace `<username>` and `/path/to/your/plugins` with your actual values.

### Step 3: Restart Claude Code

Close and reopen Claude Code for the changes to take effect.

### Step 4: Install plugins

1. Open Claude Code
2. Run `/plugins` or press the plugins shortcut
3. Go to **Marketplaces** tab
4. Select **local-plugins**
5. Browse and install desired plugins

## Plugin Usage

### ralph-dev

PRD-driven feature development with fresh context isolation.

**Commands:**
- `/ralph-dev:step` - Execute ONE PRD feature task in current session
- `/ralph-dev:run [count]` - Run multiple PRD steps with fresh context isolation

**Agent:**
- Triggers automatically when you say things like "run through the PRD features" or "execute 5 PRD steps"

**Required files:**
```
.ralph/
├── PRD.json       # Feature definitions with priorities
└── progress.txt   # Running log of completed work
```

**PRD.json format:**
```json
{
  "name": "Project Name",
  "version": "1.0.0",
  "features": [
    {
      "id": "feature-1",
      "name": "Feature name",
      "priority": 1,
      "status": "pending",
      "description": "What the feature should do"
    }
  ]
}
```

**Status values:** `pending`, `in_progress`, `completed`

## Creating New Plugins

To add a new plugin to this marketplace:

1. Create a new directory: `<plugin-name>/`
2. Add plugin manifest: `<plugin-name>/.claude-plugin/plugin.json`
3. Add components (commands, agents, skills, hooks) as needed
4. Update `.claude-plugin/marketplace.json` to include the new plugin
5. Commit and push changes

### Plugin Structure

```
<plugin-name>/
├── .claude-plugin/
│   └── plugin.json        # Plugin manifest
├── commands/              # Slash commands (.md files)
├── agents/                # Subagent definitions (.md files)
├── skills/                # Skills (subdirectories with SKILL.md)
└── hooks/                 # Event handlers (hooks.json)
```

### Marketplace Entry

Add to `.claude-plugin/marketplace.json`:

```json
{
  "name": "plugin-name",
  "description": "What the plugin does",
  "version": "0.1.0",
  "source": "./plugin-name",
  "category": "productivity",
  "author": {
    "name": "Your Name"
  }
}
```
