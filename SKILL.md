---
name: rn-meta
description: Bootstrap a new Expo app with TypeScript, Expo Router, Expo Dev Client, and Uniwind + Tailwind v4. Use when creating a new React Native/Expo project or when the user wants to start a mobile app. Also use when diagnosing or fixing issues with an existing Meta stack project.
---

# React Native Meta Stack

Bootstrap a production-ready Expo app with:
- Expo Router + TypeScript
- Expo Dev Client (not Expo Go)
- Uniwind + Tailwind v4

## New Project

```bash
./scripts/meta-start $ARGUMENTS
cd $ARGUMENTS
npx expo run:ios
npx expo start --clear
```

## Diagnose Issues

Run the doctor script from the skill directory, passing the project path:

```bash
./scripts/meta-doctor /path/to/project
```

Or run from inside the project:

```bash
/path/to/skill/scripts/meta-doctor .
```

The doctor checks:
- Expo project structure
- expo-dev-client installed
- Uniwind + Tailwind installed
- global.css with correct imports
- metro.config.js using uniwind/metro (not uniwind/config)
- app/_layout.tsx importing global.css
- Conflicting packages (nativewind)

Use `--json` for machine-readable output.

## Workflow

1. Create project: `meta-start my-app`
2. Verify setup: `meta-doctor my-app`
3. If something breaks later: run `meta-doctor` again

## Manual Setup

If scripts fail, see [reference/starting.md](reference/starting.md) for step-by-step instructions.
