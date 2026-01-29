# React Native Meta Stack (`rn-meta`)

A Claude Code skill (and standalone script) that bootstraps a production-ready Expo app with:

- Expo Router + TypeScript (default template)
- Expo Dev Client (not Expo Go)
- Uniwind + Tailwind v4, wired correctly

## Structure

- `SKILL.md` - Skill entrypoint (what Claude Code reads)
- `scripts/meta-start` - Bootstrap script (creates a brand-new Expo app)
- `reference/starting.md` - Manual fallback steps + common gotchas

## Usage

### From the terminal

Prereqs: `node`, `pnpm`, and for iOS Simulator: Xcode + Command Line Tools.

```bash
./scripts/meta-start <app-name>
cd <app-name>
npx expo run:ios
npx expo start --clear
```

If the script fails, follow [`reference/starting.md`](reference/starting.md).

### In Claude Code

Clone (or copy) this repo into your Claude skills directory so the folder contains `SKILL.md`.

Typical locations:

- `~/.claude/skills/rn-meta/` (user-level)
- `.claude/skills/rn-meta/` (project-level, if supported by your setup)

Then invoke the skill with an app name (Claude will prompt based on `argument-hint: <app-name>`).

## What It Generates

The script creates a fresh Expo app and then:

- Adds `global.css` with Tailwind v4 + Uniwind imports
- Writes `metro.config.js` using `uniwind/metro`
- Ensures `app/_layout.tsx` imports `../global.css`
- Adds a small Uniwind proof element (a `className`) so you can confirm styling works

## Common Gotchas

- Metro import must be `uniwind/metro` (not `uniwind/config`)
- `global.css` import must be inside Router code (`app/_layout.tsx`)
- Restart Metro after config changes: `npx expo start --clear`
