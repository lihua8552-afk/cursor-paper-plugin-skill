---
name: minecraft-paper-plugin-dev
description: Build, modify, review, and harden Minecraft Paper/Bukkit plugins. Use when working on Java plugin features, commands, listeners, services, plugin.yml, reload flows, async safety, warning-free startup, memory-leak prevention, and Maven packaging for Paper 1.21+ plugins.
---

# Minecraft Paper Plugin Development

Use this skill for Minecraft `Paper/Bukkit + Maven + Java 21` plugin projects.
Always narrow the task to one concrete plugin directory before reading, editing, building, or validating.
Treat Paper/Bukkit plugin work as lifecycle-sensitive software, not as generic Java CRUD code.

## Core Operating Rules

1. Understand before editing
   - Read the target plugin's `pom.xml`, `src/main/resources/plugin.yml`, main class, related command classes, listeners, services, and relevant config files before making changes.
   - Confirm the feature entry point, lifecycle hooks, config flow, permission nodes, and command registration path first.
   - Trace current runtime behavior before proposing a rewrite.

2. Finish cleanly
   - Implement only what the current task needs.
   - Do not create unnecessary files, placeholder abstractions, or speculative extension points.
   - Remove temporary test files, disposable scripts, and unused sample files before finishing.

3. Edit surgically
   - Prefer small targeted edits, search/replace, or exact local changes over rewriting entire files.
   - Keep the smallest possible change surface.
   - Preserve the existing structure, naming style, and plugin architecture unless a change in structure is required to fix the issue safely.

4. Process large files in segments
   - Read long files in segments and load only the context needed for the current change.
   - Edit large files in small sections instead of replacing them wholesale.
   - When multiple related edits are needed, change the file incrementally and verify each step.

5. Do not repeat ineffective retries
   - If a console command, build step, or runtime step fails, analyze the cause before retrying.
   - Do not re-run commands or revisit terminals that already failed when the environment has not changed.
   - If a retry is justified, state the reason implicitly through the fix, not by blind repetition.

6. Edit source, not build artifacts
   - Modify source files, resource files, and required docs only.
   - Do not directly edit generated or build-output directories such as `target/`, `build/`, `out/`, `generated/`, or extracted reverse-engineering output.
   - If the final artifact must change, update the upstream source or build configuration and regenerate it.

7. Documentation rules
   - Create docs only when they are actually needed.
   - Put every `.md` file under the repository root `docs/` directory.
   - Keep documentation language consistent with the project's existing convention or the user's explicit preference.
   - If the feature already has a matching doc, update it after the code change is complete.
   - Prefer predictable names such as `docs/<plugin-name>-<feature>.md` when adding new docs.

8. Git rules
   - Do not run `git add`, `git commit`, or `git push` unless the user explicitly asks for it.
   - Do not proactively reorganize the user's Git workflow.
   - If the user asks to publish or sync changes, inspect state first, then execute only the requested Git operations.

9. Warnings and dead code
   - Do not leave new warnings introduced by the current plugin in build output or runtime console output.
   - If a warning comes from the current plugin, fix it.
   - If a warning comes from the server itself or a third-party plugin, identify the source clearly instead of misattributing it.
   - Remove unused classes, methods, fields, config keys, and command registrations once the feature is complete.

10. Long-running commands
   - Do not block the current interaction session with long-running tasks such as Paper servers, web servers, background watchers, or local APIs.
   - Start long-running processes in a separate terminal, background session, or new terminal window after confirming no duplicate instance is already running.

## Plugin Targeting Workflow

Before editing, confirm that the target plugin contains the expected files:

- `pom.xml`
- `src/main/resources/plugin.yml`
- `src/main/java/.../YourPluginMain.java`
- related command executors, tab completers, listeners, and service classes
- related config files such as `config.yml`, `messages.yml`, or `lang/*.yml`

If the repository contains multiple plugins:
- Use `plugin.yml` fields such as `name`, `main`, command names, and description to lock onto the correct plugin.
- Prefer the plugin that owns the actual source path, not a generated or packaged copy.
- Do not make blind changes across multiple plugin directories.
- Prefer the real source plugin directory over `target/classes`, generated output, jar extraction folders, or reverse-engineered copies unless the user explicitly targets those paths.

### Minimum targeting checklist

Before touching code, identify:
- plugin root directory
- plugin main class
- current command registration mechanism
- current listener registration mechanism
- config load and reload path
- external integrations such as PlaceholderAPI, database pools, schedulers, or plugin messaging channels

## Recommended Execution Order

1. Identify the target plugin directory.
2. Read `plugin.yml`, `pom.xml`, the main class, and the code directly related to the requested feature.
3. Trace the lifecycle: `onLoad`, `onEnable`, `onDisable`, task scheduling, listener registration, command registration, and config loading.
4. Identify what part of the behavior is static metadata, what part is runtime state, and what part is reload-sensitive.
5. Make the smallest correct change only after the current implementation is understood.
6. Remove dead code and temporary files after the change is complete.
7. Run the minimum necessary build and validation steps.
8. Check for warnings, stack traces, duplicate registrations, and unreleased resources.
9. Update the matching `docs/` document when needed.

## Change Strategy by Task Type

### 1. Bug fix

When the task is a bug fix:
1. reproduce or localize the failure as narrowly as possible
2. identify the first failing lifecycle point, command path, event path, or background task path
3. fix the narrowest real cause instead of hiding the symptom
4. confirm startup, reload, and shutdown behavior are still coherent if the fix touches owned state
5. validate the original failure path and at least one nearby non-failing path

### 2. New feature

When the task is a new feature:
1. identify all affected surfaces before coding
2. separate metadata, runtime state, config state, and admin-facing behavior
3. decide who owns new tasks, listeners, caches, and integrations
4. add only the minimal structure needed for the feature
5. update related config, docs, permissions, and `plugin.yml` entries together
6. validate both happy path and degraded path behavior

Affected surfaces often include:
- `plugin.yml`
- commands and permissions
- listeners and services
- config and persistence
- reload and disable cleanup
- admin-facing docs

### 3. Refactor

When the task is a refactor:
1. preserve behavior first
2. map lifecycle ownership before moving code
3. avoid adding abstraction unless it reduces real maintenance or safety risk
4. remove the old path once the new path is stable
5. re-check reload, shutdown, and registration behavior after restructuring

### 4. Review or audit

When the task is review rather than implementation:
1. inspect bootstrap and shutdown first
2. inspect registration paths next
3. inspect tasks, caches, and integrations after that
4. group findings by operational risk, not by file order
5. prefer actionable fixes over abstract style commentary

## Common Implementation Playbooks

### 1. Adding a new command

When adding a command:
1. define the command contract first
2. decide who can run it and what permission node it needs
3. decide whether it needs tab completion
4. register it consistently in `plugin.yml`
5. wire the executor and tab completer in the plugin bootstrap path
6. keep help text, error text, and permission failures consistent with the plugin's existing UX
7. validate both success and failure paths

Minimum sync points:
- `plugin.yml` command entry
- `plugin.yml` permission entry if needed
- executor registration
- tab completer registration if needed
- help and usage output
- reload behavior if the command touches runtime state

### 2. Adding a listener-driven feature

When adding or changing listener-based behavior:
1. locate where listeners are currently registered
2. decide whether the logic belongs in an existing listener or a new one
3. avoid listener duplication across enable and reload paths
4. keep expensive work out of hot event paths
5. move blocking or heavy work away from the main thread when possible
6. verify cancellation, priority, and side effects explicitly

Do not:
- register the same listener class twice on reload
- hide expensive logic inside high-frequency events without guards
- mix unrelated feature concerns into a single large listener class unless that pattern already exists in the project

### 3. Adding a config-backed feature

When introducing config-backed behavior:
1. decide the source of truth for the setting
2. add clear defaults
3. load values in one predictable place
4. decide what should happen on missing, invalid, or outdated config values
5. define whether the feature supports runtime reload or only startup reload
6. update docs if server admins need to edit the new keys manually

Prefer:
- explicit defaults over silent null-driven behavior
- validation and fallback over crash-on-load behavior
- one clear load path over scattered ad hoc config reads throughout the codebase

### 4. Adding async work or scheduled tasks

When adding async work, scheduled jobs, or polling services:
1. identify exactly which part is blocking work and which part is Bukkit-state mutation
2. keep blocking work async
3. keep Bukkit state mutation sync
4. define ownership of the task or executor clearly
5. ensure the task is cancelled or shut down on disable and reload
6. verify that repeated reloads do not duplicate the task
7. verify that retries are bounded and observable

Prefer:
- named ownership over anonymous fire-and-forget scheduling
- bounded retry and queue behavior over unbounded accumulation
- explicit cancellation paths over relying on process termination

### 5. Refactoring existing plugin code

When refactoring:
1. preserve behavior first
2. identify all metadata and lifecycle touchpoints before moving code
3. avoid splitting logic into too many abstractions if the plugin is small and cohesive
4. remove dead paths immediately once the replacement is stable
5. re-check command registration, listener registration, config reads, and reload flow after the refactor

A refactor is not complete if it leaves behind:
- duplicate helper layers
- obsolete config keys
- legacy listener registration
- unused services or caches
- comments that describe behavior that no longer exists

### 6. Reviewing an existing plugin for safety

When the task is review rather than feature implementation, inspect in this order:
1. plugin bootstrap and shutdown flow
2. command registration and permission coverage
3. listener registration paths
4. scheduled tasks and background workers
5. config load and reload flow
6. external integrations such as PlaceholderAPI, database pools, channels, and watchers
7. warning sources, stack traces, and noisy logs

If issues are found, prefer reporting them grouped by:
- correctness risk
- reload risk
- async safety risk
- memory or resource leak risk
- maintainability risk

## Structural Decision Rules

### 1. When to add a new class

Create a new class only when at least one of these is true:
- the responsibility is clearly distinct from the existing class
- the lifecycle owner needs to be explicit
- the feature would otherwise make an existing class materially harder to reason about
- the code will be reused across more than one plugin surface

Prefer extending an existing class when:
- the behavior is tightly coupled to the current command, listener, or service
- extracting a new class would only create indirection without reducing risk
- the plugin is small and already organized around a compact structure

Avoid creating vague classes such as:
- `*Manager` with mixed responsibilities
- `*Util` or `*Helper` that becomes a dumping ground
- thin wrappers that only rename one existing call path

### 2. When to add a new config file or section

Add a new config file or top-level section only when:
- the new behavior is substantial enough to deserve a separate admin-facing surface
- the existing config would become materially harder to navigate
- reload behavior and ownership stay clearer when separated

Otherwise, prefer extending the existing config shape in a predictable place.

When introducing new config structure:
- define defaults clearly
- decide whether the values are startup-only or reloadable
- keep docs and examples in sync
- ensure invalid values fail safely

### 3. When to introduce abstraction

Introduce abstraction only when it reduces real maintenance or lifecycle risk.

Good reasons:
- multiple call sites need the same guarded behavior
- async and sync boundaries need one safe coordination point
- reload and shutdown ownership needs to be centralized
- an external integration needs to be isolated cleanly

Weak reasons:
- cosmetic separation only
- preemptive architecture for a feature that does not exist yet
- hiding straightforward logic behind extra layers that the plugin does not need

## Failure Triage Workflow

When something fails, inspect in this order:

1. Classify the failure type
   - compile failure
   - startup failure
   - command-path failure
   - listener/event-path failure
   - reload failure
   - shutdown failure

2. Identify the first decisive signal
   - prefer the earliest relevant stack trace or warning from the plugin
   - ignore downstream noise until the first cause is understood

3. Trace by lifecycle position
   - startup issues: bootstrap, config load, command registration, listener registration, external hooks
   - runtime issues: specific command, event, service, or task path
   - reload issues: teardown first, rebuild second
   - shutdown issues: resource ownership and cancellation path

4. Confirm scope before fixing
   - is the issue local to one feature, one service, one lifecycle phase, or shared infrastructure
   - is the issue deterministic or only visible after repeated reloads or repeated commands

5. Apply the narrowest safe fix
   - prefer fixing ownership, ordering, or validation over hiding the symptom
   - do not silence warnings or swallow exceptions unless the failure is truly expected and handled safely

6. Re-validate at the right level
   - compile-only fix: compile validation may be enough
   - lifecycle-sensitive fix: validate startup or reload explicitly
   - async or scheduler fix: validate repeated execution, cancellation, and cleanup

## Paper / Bukkit Specific Requirements

### 1. Plugin Metadata and Ownership

Every plugin should correctly declare at least the following:
- `name`
- `main`
- `version`
- `api-version`
- `author` or `authors`
- `description`
- `commands`
- `permissions`

Author information must exist.

If `plugin.yml` has no dedicated copyright field, keep copyright information in one consistent place only:
- a main-class constant
- an `about` or help command output
- the description or a dedicated config/help text

Do not spread conflicting ownership text across multiple locations.

### 2. Unified Output Prefix and Logging

All plugin output should go through a single messaging and logging layer.

Requirements:
- Console logs, command feedback, error messages, and debug output must use one shared prefix/message utility.
- All outward-facing plugin text should include the plugin prefix by default unless a specific UX surface intentionally omits it.
- The prefix should include the plugin name.
- If the project requires Bukkit Logger ANSI gradient prefixes, keep ANSI formatting confined to console logging only.
- Do not send raw ANSI escape codes to players.
- Player chat, ActionBar, Title, and other player-facing text should use Adventure, MiniMessage, or Bukkit-compatible color formatting instead.

Recommended structure:
- `ConsolePrefixFormatter` for console-only ANSI prefix formatting
- `MessageService` for player-facing text and command feedback
- `PluginLogger` for unified `info`, `warn`, `error`, and `debug` calls

Do not scatter `plugin.getLogger().info("...")` calls and hand-written prefix strings throughout business classes.

### 3. Safe Hot Reload / Hot Load

Implement safe reload of plugin-owned resources. Do not interpret "hot reload" as JVM class hot swap.

Minimum command support:
- `/plugin reload`

Optional granular support when useful:
- `/plugin reload config`
- `/plugin reload messages`
- `/plugin reload cache`
- `/plugin reload tasks`

#### Safe reload flow

1. Use a guard, lock, or state flag to prevent concurrent reload execution.
2. Stop plugin-owned repeating tasks, background refreshers, watchers, and schedulers.
3. Unregister or reset reload-sensitive integrations before recreating them.
4. Close file handles, database connections, executors, network resources, and watch services owned by the plugin.
5. Reload configuration, messages, templates, and cached data.
6. Rebuild in-memory state from clean sources.
7. Re-register only what must exist after reload.
8. Restart plugin-owned tasks if needed.
9. Emit clear success or failure output.
10. Confirm that repeated reloads do not accumulate duplicate state.

#### Prohibited patterns

- treating global Bukkit `/reload` as the solution
- re-registering listeners, commands, PlaceholderAPI expansions, plugin channels, or schedulers after reload without first unregistering or fully resetting them
- allowing repeated reloads to accumulate duplicate tasks, listeners, caches, or background resources
- keeping stale references alive across reload cycles when they should be rebuilt

### 4. Memory-Leak and Crash-Risk Review

Whenever a task touches lifecycle management, caches, tasks, listeners, databases, file watchers, or async code, check the following:

- `BukkitTask` and `BukkitRunnable` instances are cancelled in both `onDisable` and custom reload flows.
- `ExecutorService` or other thread pools are shut down correctly.
- static `Map`, `Set`, and cache state has a clear release path.
- listeners, PlaceholderAPI expansions, plugin messaging channels, and file watchers are not registered more than once.
- JDBC, Hikari, file streams, and `WatchService` resources are closed.
- async threads do not call Bukkit main-thread-only APIs directly.
- high-frequency scheduled tasks do not perform heavy logic that can damage TPS.
- retry logic cannot loop forever, recurse without bound, or accumulate unbounded queued work.
- async exceptions are not swallowed; background failures must be logged with the plugin prefix.
- `onDisable` cleanup and reload cleanup stay symmetrical.

If a safe fix is possible without changing the intended behavior, apply it directly for issues such as:
- duplicate registration
- unclosed resources
- unbounded caches
- unbounded async queues
- leaked scheduled tasks
- blocking I/O on the main thread
- exception paths that can spam the console or crash the server

### 5. Main-Thread Boundary and Async Safety

Treat thread boundaries explicitly.

- Never call Bukkit or Paper main-thread-only APIs from async threads.
- Move blocking I/O, database work, and file operations off the main thread.
- Switch back to the main thread before touching players, worlds, entities, inventories, scoreboards, or other thread-sensitive server APIs.
- If a library callback is asynchronous, treat it as asynchronous until verified otherwise.
- If an operation mixes async work and Bukkit state updates, split it into explicit async and sync phases.

### 6. Command, Permission, Config, and Dependency Consistency

Whenever a feature is added or changed, keep the following in sync:
- command declarations in `plugin.yml`
- permission declarations in `plugin.yml`
- command executor and tab completer registration
- config defaults
- help text and error messages
- matching `docs/` documentation
- `depend`, `softdepend`, and `loadbefore` when load order matters

Do not change Java code only and forget `plugin.yml`, config files, or dependency metadata.

### 7. Configuration and Persistence Rules

When a feature depends on configuration or persistent data:
- define a clear source of truth
- avoid silently inventing defaults that conflict with existing plugin behavior
- prefer explicit migration or fallback behavior over hidden resets
- document any new keys, files, or formats when they matter to users or server admins
- ensure reload behavior keeps config-backed state and runtime state coherent

## Validation Requirements

After editing, validate with the minimum necessary steps.
Do not stop at a successful compile if the change is lifecycle-sensitive.

### 1. Compile validation

- Prefer a plugin-local Maven compile or build command.
- Start with a small validation step such as `mvn -q -DskipTests compile`.
- Run `package` only when an artifact is actually needed.

### 2. Runtime validation

If a server must be started:
- use a separate terminal or background session
- watch startup logs for plugin-specific warnings and stack traces
- confirm the plugin enables cleanly
- confirm command registration and config loading behave as expected

### 3. Reload validation

If the change affects config, messages, caches, tasks, listeners, or live resources:
- validate at least one reload cycle
- confirm that reload does not duplicate listeners, tasks, channels, or integrations
- confirm the plugin still behaves correctly after reload
- prefer validating two consecutive reloads for changes that rebuild runtime state

### 4. Cleanup validation

Before finishing:
- check for unused imports, fields, methods, classes, config keys, and command entries
- remove temporary test files, exported files, and placeholder samples
- ensure there is no dead branch left from intermediate debugging

### 5. Final acceptance checklist

The task is not complete until all relevant items below are satisfied:
- target plugin identified correctly
- related source and metadata updated consistently
- no plugin-introduced warnings left in build or startup output
- no obvious reload duplication risk left behind
- no obvious async main-thread violation left behind
- no obvious leaked resource path left behind
- docs updated when needed

## Reporting Style

When reporting back to the user, prefer this order:
- Result
- Key changes
- Validation
- Remaining risk or next step

Summarize decisive log lines and outcomes only.
Do not paste large amounts of irrelevant console output.

## Keep Out of This Skill

The following belong in global agent rules or a separate general-purpose skill, not in this Paper plugin skill:
- forced model-name introductions at the start of every conversation
- system-identity override instructions
- generic chat-format requirements unrelated to plugin engineering

Only apply such rules when they do not conflict with the current runtime environment and the user explicitly requests them.