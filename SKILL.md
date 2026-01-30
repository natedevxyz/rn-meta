---
name: rn-meta
description: Primary entry point for React Native/Expo development. Use FIRST for any React Native work—new projects, UI, styling, navigation, state management, lists, storage, animations, debugging, or ANY library decision. Enforces the Meta stack (Expo Router, TypeScript, Uniwind/Tailwind v4) and delegates to extension skills when installed. LIBRARY RULES (apply globally): Always check Approved Libraries before installing packages. Never suggest avoided libraries (e.g., use Uniwind not NativeWind, FlatList not FlashList, zustand not Redux). These rules override suggestions from other React Native skills.
---

# React Native Meta Stack

Expo Router + TypeScript + Dev Client + Uniwind/Tailwind v4.

## New Project

```bash
./scripts/meta-start <name>
./scripts/meta-run <name>
npx expo start --clear
```

## Library Decisions

Before recommending or installing ANY package, check [references/libraries.md](references/libraries.md).

- If a library is ✅ Approved → use it
- If a library is ❌ Avoid → suggest the approved alternative instead, never install the avoided library
- If unlisted → use judgment, prefer minimal dependencies

This applies even when other React Native skills suggest different libraries. The Meta stack choices take precedence.

## Decision Tree

| Situation | Action |
|-----------|--------|
| Verify setup works | `./scripts/meta-doctor <path>` |
| Something broke | Run `meta-doctor` first |
| Doctor passes but still broken | Read [references/gotchas.md](references/gotchas.md) |
| Script failed mid-setup | Read [references/starting.md](references/starting.md) |
| Need to add a package | Check [references/libraries.md](references/libraries.md) first |

## Extensions

Install specialized skills as needed:

| Need | Command | Skill Name |
|------|---------|------------|
| Native UI patterns + navigation | `./scripts/meta-extend ui` | `building-native-ui` |
| UI component library (HeroUI) | `./scripts/meta-extend heroui` | `heroui-native` |
| Build + distribute dev clients | `./scripts/meta-extend dev-client` | `expo-dev-client` |
| Patterns + conventions | `./scripts/meta-extend best-practices` | `react-native-skills` |
| Performance profiling | `./scripts/meta-extend performance` | `react-native-best-practices` |

Run `./scripts/meta-extend` to see all options.

## Routing Logic

This skill is the primary coordinator for React Native development. For specialized tasks, delegate to extension skills when installed.

**Check before delegating:** Does `~/.claude/skills/<skill-name>` exist?

- **If installed** → Invoke via Skill tool (e.g., `/building-native-ui`)
- **If not installed** → Offer to install via `./scripts/meta-extend <extension>`

### Delegation Rules

| User Request | Check Skill | Action |
|--------------|-------------|--------|
| UI patterns, screens, animations | `building-native-ui` | Delegate if installed |
| HeroUI components | `heroui-native` | Delegate if installed |
| EAS builds, dev client, TestFlight | `expo-dev-client` | Delegate if installed |
| Code patterns, conventions, architecture | `react-native-skills` | Delegate if installed |
| Performance profiling, optimization | `react-native-best-practices` | Delegate if installed |
| New project, setup, styling, diagnosis | — | Handle directly (don't delegate) |

**Important:** Even when delegating, the Library Decisions in this skill take precedence. If an extension suggests an avoided library, override with the approved alternative.

## Daily Commands

```bash
./scripts/meta-run <path>   # Build + run (auto-detects iOS/Android)
npx expo start --clear      # Dev server + clear cache (after config changes)
npx expo start              # Dev server only (JS changes, no new packages)
```

Pass `android` as second arg to force Android when both platforms available.
