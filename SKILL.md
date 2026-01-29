---
name: rn-meta
description: Bootstrap and develop Expo apps with TypeScript, Expo Router, Expo Dev Client, and Uniwind/Tailwind v4. Use when creating a new React Native/Expo project, building mobile app UI, or diagnosing issues. Also triggers for UI component libraries, EAS builds, dev client distribution, TestFlight, and React Native performance optimization—routes to specialized extensions.
---

# React Native Meta Stack

Expo Router + TypeScript + Dev Client + Uniwind/Tailwind v4.

## New Project

```bash
./scripts/meta-start <name>
./scripts/meta-run <name>
npx expo start --clear
```

## Decision Tree

| Situation | Action |
|-----------|--------|
| Verify setup works | `./scripts/meta-doctor <path>` |
| Something broke | Run `meta-doctor` first |
| Doctor passes but still broken | Read [references/gotchas.md](references/gotchas.md) |
| Script failed mid-setup | Read [references/starting.md](references/starting.md) |

## Extensions

Install specialized skills as needed:

| Need | Command | Skill Name |
|------|---------|------------|
| Native UI patterns + navigation | `./scripts/meta-extend ui` | `building-native-ui` |
| UI component library (HeroUI) | `./scripts/meta-extend heroui` | `heroui-native` |
| Build + distribute dev clients | `./scripts/meta-extend dev-client` | `expo-dev-client` |
| Performance + best practices | `./scripts/meta-extend best-practices` | `react-native-skills` |

Run `./scripts/meta-extend` to see all options.

## Routing Logic

Before offering to install an extension, check if it's already installed:

1. Check if `~/.claude/skills/<skill-name>` exists (symlink or directory)
2. If **installed** → Tell the user: "The [skill-name] skill is already available. Invoking it now." Then use the Skill tool to invoke it directly (e.g., `/building-native-ui`)
3. If **not installed** → Offer to install via `./scripts/meta-extend <extension>`

## Daily Commands

```bash
./scripts/meta-run <path>   # Build + run (auto-detects iOS/Android)
npx expo start --clear      # Dev server + clear cache (after config changes)
npx expo start              # Dev server only (JS changes, no new packages)
```

Pass `android` as second arg to force Android when both platforms available.
