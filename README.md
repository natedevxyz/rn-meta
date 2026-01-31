# rn-meta

An opinionated React Native/Expo skill: a curated stack + library rules so you ship faster with fewer decisions.

Stack: Expo Router + TypeScript + Expo Dev Client + Uniwind/Tailwind v4.

## Install

```bash
npx skills add natedevxyz/rn-meta --global --agent claude-code
```

## What You Get

- A default stack that’s hard to mess up (routing, styling, state, storage, data fetching).
- One source of truth for libraries: approved picks + setup examples.
- Scripts to scaffold, run, and diagnose projects.
- Optional “extensions” you can install for UI patterns, HeroUI, dev-client workflows, best practices, and performance.

## Approved Libraries (TL;DR)

Full details (behavioral notes + code examples): [references/libraries.md](references/libraries.md)

| Category | Use |
|---------|-----|
| Styling | `uniwind` |
| Navigation | `expo-router`, `NativeTabs`, `react-native-bottom-tabs` (fallback) |
| Lists | `@legendapp/list`, `FlatList` (small lists) |
| State Management | `zustand` + MMKV persist, React built-ins |
| Data Fetching | `@tanstack/react-query` + persist + `axios` |
| Storage | `react-native-mmkv`, `expo-secure-store` (sensitive) |
| Forms & Inputs | `react-hook-form` + `zod`, `react-native-keyboard-controller` |
| Toasts | `sonner-native` |
| Animations | `@shopify/react-native-skia` Skottie (Lottie), `react-native-reanimated` (gestures/transitions via `ui` extension) |
| Bottom Sheets | Expo Router `formSheet` (iOS), `@gorhom/bottom-sheet` (cross-platform) |
| Authentication | `@supabase/supabase-js` (anonymous first), `expo-apple-authentication`, Supabase OAuth (Google) |
| Camera | `react-native-vision-camera` |
| Subscriptions | `react-native-purchases` (RevenueCat) |
| Analytics | `posthog-react-native` |
| Images | `expo-image` |
| Error Monitoring | `@sentry/react-native` |
| Push Notifications | `expo-notifications` |

## Quick Start (New Project)

```bash
./scripts/meta-start <app-name>
./scripts/meta-run <app-name>
npx expo start --clear
```

## What’s Included

| Script | Purpose |
|--------|---------|
| `scripts/meta-start` | Scaffold a new Expo project with the full stack |
| `scripts/meta-run` | Build + run (auto-detects iOS/Android) |
| `scripts/meta-doctor` | Diagnose configuration issues |
| `scripts/meta-extend` | Install companion extensions (UI, HeroUI, dev-client, best-practices, performance) |

## Extensions

This skill can install companion skills from other authors:

| Extension | Skill | Source |
|-----------|-------|--------|
| `ui` | Native UI patterns | [expo/skills](https://github.com/expo/skills) (`building-native-ui`) |
| `heroui` | HeroUI component library | [heroui-inc/heroui](https://github.com/heroui-inc/heroui) (`heroui-native`) |
| `dev-client` | EAS builds + TestFlight | [expo/skills](https://github.com/expo/skills) (`expo-dev-client`) |
| `best-practices` | Patterns + conventions | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) (`react-native-skills`) |
| `performance` | Profiling + optimization | [callstackincubator/agent-skills](https://github.com/callstackincubator/agent-skills) (`react-native-best-practices`) |

```bash
./scripts/meta-extend ui              # Native UI patterns
./scripts/meta-extend heroui          # HeroUI component library
./scripts/meta-extend dev-client      # EAS builds + TestFlight
./scripts/meta-extend best-practices  # Patterns + conventions
./scripts/meta-extend performance     # Profiling + optimization
./scripts/meta-extend all             # Everything
```

Run `./scripts/meta-extend` to see all options.

## Troubleshooting

```bash
./scripts/meta-doctor ./my-app
```

If the doctor passes but things are still broken:

- Check [references/gotchas.md](references/gotchas.md) (runtime issues doctor can’t detect)
- If `meta-start` failed mid-way: [references/starting.md](references/starting.md)

## Stack

- [Expo Router](https://docs.expo.dev/router/introduction/) — file-based routing
- [Expo Dev Client](https://docs.expo.dev/develop/development-builds/introduction/) — custom native runtime
- [Uniwind](https://uniwind.dev/) — Tailwind utilities for React Native
- [Tailwind CSS v4](https://tailwindcss.com/) — utility-first CSS

## Claude Code Fallback References

- [references/libraries.md](references/libraries.md) — the library rules + examples
- [references/gotchas.md](references/gotchas.md) — issues + fixes
- [references/starting.md](references/starting.md) — manual setup if scripts fail

## License

MIT (see `LICENSE`).

## Notes

`SKILL.md` is the Claude Code-facing entrypoint (name/description + agent instructions). `README.md` is for repo visitors.
