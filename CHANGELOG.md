# Changelog

## v0.2.1 — 2026-07-23

Adds three CLI verbs and fixes a status-reporting gap, both surfaced by a real end-to-end
install test (marketplace add → install → arm → provoke crossings → status) run for the first
time after v0.2.0's initial release.

- **`environments [query]`** — lists every environment in the manifest (name, description,
  declared-pattern counts), or searches them. Search has two modes: substring match against
  name/description/pattern text, and `fnmatch` of the query *as a concrete identifier* against
  each declared glob — so `environments FileMaker:schema-builder` answers "which environment
  would this exact call land in?" even though the manifest only ever declares `FileMaker:*`.
- **`switch <env> [session_id]`** — manually sets the active environment for a session. The
  third way the active environment changes, alongside `SessionStart`'s project-home lookup and
  `PostToolUse`'s automatic switch-on-skill-run. Refuses unknown environment names, listing the
  real ones.
- **`classify <env> [--skill P] [--agent P] [--command P] [--mcp P] [--path P] [--create
  "description"]`** — adds patterns to an environment's declaration, or creates a new one with
  `--create`. The write-side companion to `audit` (finds what's unassigned) and `environments`
  (finds what's already assigned where). Idempotent (re-adding a pattern is a no-op); refuses to
  silently redefine an existing environment if `--create` is passed for one that already exists;
  backs up the manifest before writing, same discipline as `install`'s settings.json backup.
- **Fixed `status`'s `hooks_registered_in_settings` field**, which only ever checked the
  *standalone* arming path and reported `false` even when armed correctly via the plugin path.
  Found during the live install test: the plugin was provably armed (`claude plugin details`
  confirmed 3 hooks registered) while `status` reported no hooks registered at all. New
  `armed_via` field (`plugin` / `standalone` / `both` / `none`) reports the real state; the old
  field is kept for backward compatibility.
- **Selftest**: 20 → 40 cases, covering all of the above plus the plugin-arming detection.
- **Added this CHANGELOG** — v0.2.0 shipped without one, missing `cadenceux-plugin-creator`'s
  own release checklist requirement. Retroactively documented below.

## v0.2.0 — 2026-07-17

Initial public release. `install` / `uninstall [--purge]` / `audit` / `status` / `selftest`
verbs; SessionStart / PreToolUse / PostToolUse hooks; warn / ask / deny policy per manifest;
`crossings.log` audit trail. Packaged as a Claude Code plugin (`.claude-plugin/plugin.json` +
`marketplace.json`, `hooks/hooks.json`, bundled `agentic-gate-manage` skill) as well as a
standalone install path. MIT (code) / CC BY 4.0 (docs) dual licensing. Published to
`github.com/CadenceUX/agentic-gate`.

Renamed twice during development before this release: `SkillGuardrail` → `Agentic-Flow`
(abandoned after a GitHub/npm name collision with an unrelated, actively-maintained project) →
`Agentic-Gate` (final). Only this name was ever pushed publicly.
