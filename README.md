# Minecraft Paper Plugin Dev Skill for Cursor

Build Paper/Bukkit plugins with safer reloads, cleaner edits, and fewer async mistakes.

A production-minded Cursor skill for Java plugin authors who want less guesswork, less metadata drift, and fewer broken server reloads.

## Why people use it

- safer reload and hot-load flows
- stronger async and main-thread guardrails
- memory-leak and crash-risk checks
- tighter edits with less file churn
- warning-aware validation
- better command, config, permission, and dependency consistency

## Best fit

Paper, Bukkit, and Spigot plugin authors working with Java + Maven projects.

## Install

Copy `minecraft-paper-plugin-dev` into either:

- `~/.cursor/skills/` for personal use
- `.cursor/skills/` in a project repository

## Prompt ideas

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