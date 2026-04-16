# Minecraft Paper Plugin Dev Skill for Cursor

![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)
![Cursor Skill](https://img.shields.io/badge/Cursor-Skill-blue)
![GitHub Repo stars](https://img.shields.io/github/stars/lihua8552-afk/minecraft-paper-plugin-dev-skill?style=social)
![Minecraft](https://img.shields.io/badge/Minecraft-Paper%20%7C%20Bukkit%20%7C%20Spigot-5b8c2a)
![Java](https://img.shields.io/badge/Java-21%2B-orange)
![Build Tool](https://img.shields.io/badge/Maven-friendly-c71a36)

> Built for Minecraft Paper/Bukkit/Spigot authors who want safer reloads, cleaner plugin updates, and fewer async-thread mistakes.

Build Minecraft Paper/Bukkit plugins with safer reloads, cleaner edits, and fewer async mistakes.

A production-minded Cursor skill for Java plugin authors who want less guesswork, less metadata drift, and fewer broken server reloads.

## Why it stands out

Generic AI can write Java, but it often misses the Paper/Bukkit details that break real plugin projects. This skill pushes Cursor toward safer, production-ready changes by enforcing patterns around:

- reload-safe resource handling
- async and main-thread boundaries
- listener, task, and cache cleanup
- `plugin.yml`, command, permission, and config consistency
- warning-aware validation before calling work done

## What it helps prevent

- broken or duplicated reload behavior
- leaked tasks, watchers, executors, and caches
- async access to Bukkit main-thread-only APIs
- drift between Java code and `plugin.yml`
- noisy warning-heavy plugin updates
- large, unnecessary file rewrites

## Before vs after

**Without a plugin-aware skill**
- generic Java changes that ignore Bukkit lifecycle ownership
- reload support that silently duplicates listeners or tasks
- async code that touches Bukkit APIs unsafely
- features added in Java without fully updating `plugin.yml`, permissions, or config

**With this skill**
- tighter, smaller edits with clearer ownership
- safer reload and shutdown behavior
- cleaner separation between async work and main-thread mutation
- more consistent command, config, permission, and metadata updates

## Best fit

Use this if you build or maintain:

- Paper plugins
- Bukkit plugins
- Spigot plugins
- Java + Maven Minecraft plugin projects

## Quick install

Copy `minecraft-paper-plugin-dev` into either:

- `~/.cursor/skills/` for personal use
- `.cursor/skills/` in a project repository

Or clone this repository and copy the skill folder into your Cursor skills directory.

Final structure should look like this:

```text
.cursor/
  skills/
    minecraft-paper-plugin-dev/
      SKILL.md
```

## Inside the skill

This is not just a short prompt. The skill now includes structured guidance for:

- plugin targeting before edits
- bug-fix, feature, refactor, and audit strategies
- command, listener, config, async, and scheduler playbooks
- reload-safe lifecycle handling
- structural decision rules for classes, config shape, and abstraction
- failure triage and validation checklists

## Example prompts

Ask Cursor things like:

- "Add a reload-safe config cache to my Minecraft Paper plugin."
- "Review this plugin for duplicate listener registration and task leaks."
- "Implement a command and keep `plugin.yml`, permissions, and reload flow in sync."
- "Refactor this async code so it respects Bukkit main-thread boundaries."
- "Audit this plugin for warning sources, leaked tasks, and unsafe reload behavior."

## Included skill

- `minecraft-paper-plugin-dev/SKILL.md`

## Why this exists

Generic AI code often gets plugin reload behavior, cleanup, async boundaries, and `plugin.yml` consistency wrong. This skill pushes Cursor toward safer, more production-ready Paper/Bukkit changes instead of quick hacks.

## License

MIT