---
name: rn-meta
description: Lightweight router for React Native/Expo development. Use FIRST for any React Native work—new projects, UI, styling, navigation, state, lists, storage, forms, auth, debugging, performance, animations, gestures, builds, device verification, QA, or ANY library decision. Classifies the task, enforces the rn-meta stack/library rules, and delegates only to the relevant companion skill when needed.
---

# React Native Meta Stack

Expo Router + TypeScript + Dev Client + Uniwind/Tailwind v4.

## ⚠️ STOP Before Installing Any Package

**Read [references/libraries.md](references/libraries.md) FIRST.** This skill overrides library suggestions from extension skills.

| If another skill suggests... | Use this instead |
|------------------------------|------------------|
| `@shopify/flash-list` | `@legendapp/list` |
| `nativewind` | `uniwind` |
| `redux` / `@reduxjs/toolkit` | `zustand` |
| `async-storage` | `react-native-mmkv` |
| `fetch` | `axios` |
| `formik` / `yup` | `react-hook-form` + `zod` |
| `lottie-react-native` | `@shopify/react-native-skia` Skottie |
| `expo-camera` | `react-native-vision-camera` |
| `firebase auth` / `clerk` | `@supabase/supabase-js` |
| `@react-navigation/bottom-tabs` | `NativeTabs` or `react-native-bottom-tabs` |

## Library Decisions

- ✅ Approved → use it
- ❌ Avoid → use the approved alternative, never install avoided libraries
- Unlisted → use judgment, prefer minimal dependencies

**Full list:** [references/libraries.md](references/libraries.md)

## New Project

```bash
./scripts/meta-start <name>
./scripts/meta-run <name>
npx expo start --clear
```

## Decision Tree

These scripts are agent workflow tools. Prefer running them directly when their situation applies, subject to normal user approval for network access, global skill changes, or platform builds. Get explicit approval before commands that install or update global skills. Do not only describe the command unless the user is asking for instructions rather than action.

| Situation | Action |
|-----------|--------|
| Starting a new Expo app | Run `./scripts/meta-start <name>` |
| Build/run requested | Run `./scripts/meta-run <path>` |
| Verify setup works | Run `./scripts/meta-doctor <path>` |
| Something broke | Run `./scripts/meta-doctor <path>` first |
| Doctor passes but still broken | Read [references/gotchas.md](references/gotchas.md) |
| Script failed mid-setup | Read [references/starting.md](references/starting.md) |
| Need to add a package | Check [references/libraries.md](references/libraries.md) first |
| Need to refresh companion skills | Get approval, then run `./scripts/meta-update` |

## Runtime Verification Rule

For any non-trivial visible UI, navigation, gesture, form, or multi-step workflow change, use `agent-device` for runtime verification when a simulator/device is available or practical to start. Treat screenshots, accessibility snapshots, interaction steps, and logs as expected evidence before finalizing. Skip only for docs, static code review, dependency choice, small isolated style edits, or tasks where no runnable app/device context exists.

## Extensions

Install specialized skills as needed:

| Need | Command | Provider | Source | Skill Name |
|------|---------|----------|--------|------------|
| Native UI patterns + navigation | `./scripts/meta-extend ui` | Expo | `expo/skills` | `building-native-ui` |
| UI component library (HeroUI) | `./scripts/meta-extend heroui` | HeroUI | `heroui-inc/heroui` | `heroui-native` |
| Build + distribute dev clients | `./scripts/meta-extend dev-client` | Expo | `expo/skills` | `expo-dev-client` |
| Patterns + conventions | `./scripts/meta-extend best-practices` | Vercel Labs | `vercel-labs/agent-skills` | `react-native-skills` |
| Performance profiling | `./scripts/meta-extend performance` | Callstack | `callstackincubator/agent-skills` | `react-native-best-practices` |
| Advanced interactions, Reanimated, gestures, SVG, audio, worklets, JSI | `./scripts/meta-extend interactions` | Software Mansion | `software-mansion-labs/skills` | `react-native-best-practices` |
| Real app/device automation, QA, screenshots, logs, perf evidence | `./scripts/meta-extend device` | Callstack | `callstackincubator/agent-device` | `agent-device` |

Run `./scripts/meta-extend` to see all options. When a companion skill is needed and missing, get approval to run `./scripts/meta-extend <extension>` rather than only suggesting it.

## Updating Extensions

Use `./scripts/meta-update` to refresh installed companion skills from their upstream repositories. It uses `npx skills update`, reports missing extensions with the matching `meta-extend` command, and keeps duplicate-name providers source-qualified.

Run `meta-update` when the user asks to update or refresh skills, asks for latest companion-skill guidance, or a companion skill appears missing/stale. Because it updates global agent state and may use the network, get explicit approval through the host agent's approval flow before running it.

```bash
./scripts/meta-update                 # Update installed rn-meta extensions
./scripts/meta-update interactions    # Update one extension
```

## Routing Logic

This skill is the small, token-efficient router for React Native development. Keep work here unless specialized guidance is needed.

### Routing Workflow

1. Apply rn-meta stack and library policy first. This skill's approved/avoid list overrides extension skills.
2. Classify the task with the Delegation Rules table.
3. If no specialist is needed, handle the task directly with this skill and the reference docs.
4. If a specialist is needed, check only the matching companion skill in the active agent's global skills directory.
5. For duplicate skill names, verify provider/source before delegating.
6. If the matching specialist is installed, delegate to that skill. Use the host's explicit skill-invocation mechanism when available; otherwise read the installed companion skill's `SKILL.md` and follow only the relevant specialist guidance. Do not load unrelated companion skills.
7. If the matching specialist is missing, get approval to run `./scripts/meta-extend <extension>` when the user wants the capability installed.
8. If the user asks for latest/current companion guidance, or the installed specialist appears stale, get approval and run `./scripts/meta-update`.
9. For meaningful visible app changes, consider `agent-device` part of the done criteria when a runnable simulator/device context exists. Delegate to the installed skill before planning or running device commands. Its version-matched CLI help is authoritative for command syntax, platform limits, and setup checks.

**Check before delegating:** Does the skill exist in the active agent's global skills directory (`~/.claude/skills/<skill-name>` for Claude Code, `~/.codex/skills/<skill-name>` for Codex)? Also check provider/source when a source-qualified extension is available.

- **If installed** → Invoke the named companion skill using the host's skill mechanism, or read its `SKILL.md` directly if the host has no explicit invocation syntax.
- **If not installed** → Offer to install via `./scripts/meta-extend <extension>` and run it only after approval.

### Duplicate Skill Names

Extensions are source-qualified: identify them by extension id, provider, source repo, and skill name. This matters most when providers publish the same skill name.

Callstack and Software Mansion both publish a skill named `react-native-best-practices`:

- `performance` → Callstack `react-native-best-practices` from `callstackincubator/agent-skills`
- `interactions` → Software Mansion `react-native-best-practices` from `software-mansion-labs/skills`

When routing to `react-native-best-practices`, choose by task and provider. Do not assume the name alone identifies which skill is installed.

### Delegation Rules

| User Request | Check Skill | Action |
|--------------|-------------|--------|
| UI patterns, screens, navigation | Expo `building-native-ui` from `expo/skills` | Delegate if installed |
| Gesture animations, transitions, layout animations | Expo `building-native-ui` from `expo/skills` | Delegate if installed |
| Buttons, cards, modals, component library | HeroUI `heroui-native` from `heroui-inc/heroui` | Delegate if installed |
| Build for TestFlight, app store, EAS, dev client | Expo `expo-dev-client` from `expo/skills` | Delegate if installed |
| Code structure, conventions, architecture | Vercel Labs `react-native-skills` from `vercel-labs/agent-skills` | Delegate if installed |
| App is slow, janky, leaking memory, slow to start, or has large bundle/app size | Callstack `react-native-best-practices` from `callstackincubator/agent-skills` | Delegate if installed |
| Reanimated, Gesture Handler, SVG, audio, worklets, JSI, ExecuTorch, rich text, advanced native interactions | Software Mansion `react-native-best-practices` from `software-mansion-labs/skills` | Delegate if installed |
| Non-trivial visible UI, navigation, gesture, form, or multi-step workflow changes; test, dogfood, or verify a real app on simulator/device; inspect UI; tap/type/scroll; capture screenshots, video, logs, network, perf, React profiles, or `.ad` replay scripts | Callstack `agent-device` from `callstackincubator/agent-device` | Delegate if installed; use as runtime evidence before finalizing when simulator/device context exists |
| New project, setup, styling, Lottie animations, static diagnosis | — | Handle directly (don't delegate) |

**CRITICAL:** Even when delegating, this skill's library rules ALWAYS take precedence. If an extension skill suggests an avoided library (e.g., flash-list, nativewind, redux, async-storage), you MUST override with the approved alternative from [references/libraries.md](references/libraries.md). Do not follow the extension's library suggestion.

## Daily Commands

```bash
./scripts/meta-run <path>   # Build + run (auto-detects iOS/Android)
npx expo start --clear      # Dev server + clear cache (after config changes)
npx expo start              # Dev server only (JS changes, no new packages)
```

Pass `android` as second arg to force Android when both platforms available.
