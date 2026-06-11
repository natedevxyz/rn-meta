# rn-meta

An opinionated React Native/Expo skill: a curated stack + library rules so you ship faster with fewer decisions.

Stack: Expo Router + TypeScript + Expo Dev Client + Uniwind/Tailwind v4.

## Install

```bash
npx skills add natedevxyz/rn-meta --global --agent claude-code --agent codex
```

## What You Get

- A default stack that’s hard to mess up (routing, styling, state, storage, data fetching).
- One source of truth for libraries: approved picks + setup examples.
- Agent workflow scripts to scaffold, run, diagnose projects, and refresh companion skills.
- Optional “extensions” you can install for UI patterns, HeroUI, dev-client workflows, best practices, performance, and advanced interactions.
- Core runtime verification with Callstack `agent-device` when a simulator/device is available.

## Approved Libraries (TL;DR)

Full details (behavioral notes + code examples): [references/libraries.md](references/libraries.md)

| Category | Use |
|---------|-----|
| Styling | `uniwind` |
| Navigation | `expo-router`, `NativeTabs`, `react-native-bottom-tabs` (fallback) |
| Lists | `@legendapp/list`, `FlatList` (small lists) |
| State Management | `@legendapp/state@beta` for ephemeral client state, React built-ins for simple local state |
| Data Fetching | Convex hooks: `useQuery`, `useMutation`, `useAction`, `usePaginatedQuery` |
| Storage | `react-native-mmkv`, `expo-secure-store` (sensitive) |
| Forms & Inputs | `react-hook-form` + `zod`, `react-native-keyboard-controller` |
| Toasts | `sonner-native` |
| Animations | `@shopify/react-native-skia` Skottie (Lottie), `react-native-reanimated` (gestures/transitions via `ui` extension) |
| Bottom Sheets | `@expo/ui` `BottomSheet` |
| Authentication | Better Auth on Convex with anonymous baseline; optional Apple/Google upgrade |
| Camera | `react-native-vision-camera` |
| Subscriptions | `react-native-purchases` (RevenueCat) |
| Analytics | `posthog-react-native` |
| Images | `expo-image` |
| Error Monitoring | `@sentry/react-native` |
| Push Notifications | `expo-notifications` |

## Quick Start (New Project)

```bash
./scripts/meta-start <app-name>
./scripts/meta-setup <app-name>
./scripts/meta-run <app-name>
```

Full verification for a new app includes an `agent-device` screenshot or accessibility snapshot proving the `MetaSmoke` text `UniWind works` is visible.

## Agent Workflow Scripts

| Script | Purpose |
|--------|---------|
| `scripts/meta-start` | Agent scaffolds a new Expo project with the full stack |
| `scripts/meta-run` | Agent builds + runs the app (auto-detects iOS/Android) |
| `scripts/meta-setup` | Agent diagnoses rn-meta setup, config, and native wiring issues |
| `scripts/meta-doctor` | Agent runs TypeScript + React Doctor static quality checks |
| `scripts/meta-extend` | Agent installs companion extensions globally for Claude Code and Codex |
| `scripts/meta-update` | Agent updates installed companion skills globally using `npx skills update` |

## Quality Checks

Use `meta-doctor` after non-trivial component, state/effect, performance, accessibility, or architecture changes:

```bash
./scripts/meta-doctor ./my-app
./scripts/meta-doctor ./my-app --full
```

By default it runs TypeScript plus `npx react-doctor@latest --diff --no-score`, so agents focus on changed code instead of inherited backlog. Use `--full` for a full React Doctor audit. This is separate from `meta-setup`: `meta-setup` checks Expo/RN setup and native configuration, while `meta-doctor` checks React/RN code quality.

React Doctor's optional installer (`npx react-doctor@latest install`) can add CI and agent hooks, but `meta-doctor` does not install or mutate that setup by default.

## Extensions

This skill can install companion skills from other authors:

| Extension | Provider | Source | Skill | Purpose |
|-----------|----------|--------|-------|---------|
| `ui` | Expo | [expo/skills](https://github.com/expo/skills) | `building-native-ui` | Native UI patterns |
| `heroui` | HeroUI | [heroui-inc/heroui](https://github.com/heroui-inc/heroui) | `heroui-native` | HeroUI component library |
| `dev-client` | Expo | [expo/skills](https://github.com/expo/skills) | `expo-dev-client` | EAS builds + TestFlight |
| `best-practices` | Vercel Labs | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | `react-native-skills` | Patterns + conventions |
| `performance` | Callstack | [callstackincubator/agent-skills](https://github.com/callstackincubator/agent-skills) | `react-native-best-practices` | Profiling + optimization |
| `interactions` | Software Mansion | [software-mansion-labs/skills](https://github.com/software-mansion-labs/skills) | `react-native-best-practices` | Reanimated, gestures, SVG, audio, worklets, JSI, ExecuTorch |
| `device` | Callstack | [callstackincubator/agent-device](https://github.com/callstackincubator/agent-device) | `agent-device` | App/device automation, QA, screenshots, logs, perf evidence |

### Duplicate Skill Names

Callstack and Software Mansion both publish a skill named `react-native-best-practices`. `rn-meta` treats them as separate extensions by source:

- `performance` → Callstack `react-native-best-practices` from `callstackincubator/agent-skills`
- `interactions` → Software Mansion `react-native-best-practices` from `software-mansion-labs/skills`

Some agents and installers store skills in flat folders keyed by skill name. If one `react-native-best-practices` skill is already installed in that location, `meta-extend` will warn instead of silently overwriting or skipping the other provider.

```bash
./scripts/meta-extend ui              # Native UI patterns
./scripts/meta-extend heroui          # HeroUI component library
./scripts/meta-extend dev-client      # EAS builds + TestFlight
./scripts/meta-extend best-practices  # Patterns + conventions
./scripts/meta-extend performance     # Profiling + optimization
./scripts/meta-extend interactions    # Reanimated, gestures, SVG, audio, worklets
./scripts/meta-extend device          # App/device automation + QA evidence
./scripts/meta-extend all             # Everything possible for Claude Code + Codex
```

Run `./scripts/meta-extend` to see all options. Install `device` when you want full rn-meta runtime verification:

```bash
./scripts/meta-extend device
```

### Runtime Verification

`rn-meta` treats `agent-device` as required for full runtime verification when a simulator/device is available or practical to start. It is not part of the static `meta-setup` pass/fail result; if the companion skill is missing, install it with `./scripts/meta-extend device`.

For new apps, full verification means:

```text
meta-setup passes
meta-run launches the app
agent-device proves "UniWind works" is visible
```

For meaningful visible UI, navigation, gesture, form, and multi-step workflow changes, expected evidence includes screenshots, accessibility snapshots, interaction steps, and logs. Skip runtime verification only for docs, static code review, dependency choices, tiny isolated style edits, or tasks without runnable app/device context.

## Updating Extensions

Use `meta-update` to refresh installed companion skills from their upstream repositories for Claude Code and Codex:

```bash
./scripts/meta-update                 # Update every installed rn-meta extension
./scripts/meta-update performance     # Update one extension
```

`meta-update` uses the official `npx skills update` command. If an extension is not installed for one of the target agents, it prints the matching `meta-extend` command instead of installing it implicitly.

## Troubleshooting

Use the full diagnostic ladder before treating an issue as unknown:

```bash
./scripts/meta-setup ./my-app
./scripts/meta-doctor ./my-app
./scripts/meta-run ./my-app
```

Then use `agent-device` to reproduce the issue on a simulator/device and collect runtime evidence: screenshot or accessibility snapshot, interaction steps, and logs. For a new app, the required smoke proof is `UniWind works` visible on screen.

If the issue remains after setup, static quality, and runtime evidence:

- Check [references/gotchas.md](references/gotchas.md) for known runtime and Metro issues
- If `meta-start` failed mid-way: [references/starting.md](references/starting.md)

## Stack

- [Expo Router](https://docs.expo.dev/router/introduction/) — file-based routing
- [Expo Dev Client](https://docs.expo.dev/develop/development-builds/introduction/) — custom native runtime
- [Uniwind](https://uniwind.dev/) — Tailwind utilities for React Native
- [Tailwind CSS v4](https://tailwindcss.com/) — utility-first CSS

## Agent Fallback References

- [references/libraries.md](references/libraries.md) — the library rules + examples
- [references/gotchas.md](references/gotchas.md) — issues + fixes
- [references/starting.md](references/starting.md) — manual setup if scripts fail

## License

MIT (see `LICENSE`).

## Notes

`SKILL.md` is the agent-facing entrypoint (name/description + agent instructions). `README.md` is for repo visitors.
