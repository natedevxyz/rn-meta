---
name: rn-meta
description: Bootstrap a new Expo app with TypeScript, Expo Router, Expo Dev Client, and Uniwind + Tailwind v4. Use when creating a new React Native/Expo project or when the user wants to start a mobile app.
argument-hint: <app-name>
---

# React Native Meta Stack

Bootstrap a production-ready Expo app with:
- Expo Router + TypeScript
- Expo Dev Client (not Expo Go)
- Uniwind + Tailwind v4

## Usage

Run the bootstrap script with an app name:

```bash
./scripts/meta-start $ARGUMENTS
```

Then build and run:

```bash
cd $ARGUMENTS
npx expo run:ios
npx expo start --clear
```

## If the script fails

Follow the manual steps in [reference/starting.md](reference/starting.md).

## Common issues

See the "Common Gotchas" section in [reference/starting.md](reference/starting.md) for:
- Wrong Metro import path
- global.css import location
- Metro restart requirements
