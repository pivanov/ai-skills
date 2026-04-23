# Changelog

All notable changes to this project will be documented in this file.

## [1.2.0] - 2026-04-23

### Added
- **ask-json** (v1.0.0): Use whenever you need validated typed JSON from a sub-agent call (classify, extract, triage, routing)
  - Assertively routes main Claude away from "call Agent, regex the JSON out of prose" toward `npx @pivanov/claude-wire@^0.1.4 ask-json` with a real schema
  - Default model `haiku` with one-tier escalation to `sonnet` on validation failure (no Opus thrash)
  - Covers inline `--schema` vs `--schema-file` tradeoffs, exit-code handling (1–4), and JSON output parsing
  - Version pinned to `^0.1.5` (minimum with the working symlink-safe CLI entry guard); patches and minors flow automatically

## [1.1.0] - 2025-02-10

### Added
- **architect-workflow** (v1.0.0): Deterministic AI engineering workflow with multi-agent teams
  - Architect Mode: 6-phase framework with enforced planning gates
  - Consistency Sweep: 4-agent verification for docs/config/architecture sync
  - Pipeline Health: 4-agent audit for content generation pipelines
  - Includes `/architect` command and 3 team workflow definitions

## [1.0.0] - 2025-02-01

### Added
- **split-tasks** (v1.0.0): Split 3+ independent tasks across parallel agents for faster execution
