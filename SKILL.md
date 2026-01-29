---
name: rn-meta
description: Bootstrap a new Expo app with TypeScript, Expo Router, Expo Dev Client, and Uniwind + Tailwind v4. Use when creating a new React Native/Expo project or when the user wants to start a mobile app. Also use when diagnosing or fixing issues with an existing Meta stack project.
---

# React Native Meta Stack

Expo Router + TypeScript + Dev Client + Uniwind/Tailwind v4.

## Decision Tree

| Situation | Action |
|-----------|--------|
| New project | Run `./scripts/meta-start <name>` |
| Verify setup works | Run `./scripts/meta-doctor <path>` |
| Something broke | Run `meta-doctor` first |
| Doctor passes but still broken | Read [reference/gotchas.md](reference/gotchas.md) |
| Script failed mid-setup | Read [reference/starting.md](reference/starting.md) |

## New Project

```bash
./scripts/meta-start $ARGUMENTS
./scripts/meta-run $ARGUMENTS
npx expo start --clear
```

## Diagnose

```bash
./scripts/meta-doctor /path/to/project
```

Use `--json` for machine-readable output.

## Daily Commands

```bash
./scripts/meta-run <path>   # Build + run (auto-detects iOS/Android)
npx expo start --clear      # Dev server + clear cache (after config changes)
npx expo start              # Dev server only (JS changes, no new packages)
```

Pass `android` as second arg to force Android when both platforms available.
