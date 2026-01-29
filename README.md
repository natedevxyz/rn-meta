# rn-meta

Opinionated React Native stack: Expo Router + TypeScript + Expo Dev Client + Uniwind/Tailwind v4.

This repo is intended to be installed as a Claude Code skill, but you can also clone it and run the scripts directly.

## Quick Start

```bash
# Install the skill (replace with your git URL / local path)
npx skills add <your-repo>/rn-meta --global --agent claude-code

# Create a new project
./scripts/meta-start my-app
./scripts/meta-run my-app
npx expo start --clear
```

## What’s Included

| Script | Purpose |
|--------|---------|
| `scripts/meta-start` | Scaffold a new Expo project with the full stack |
| `scripts/meta-run` | Build + run (auto-detects iOS/Android) |
| `scripts/meta-doctor` | Diagnose configuration issues |
| `scripts/meta-extend` | Install companion extensions (UI, HeroUI, dev-client, perf) |

## Extensions

```bash
./scripts/meta-extend ui          # Native UI patterns
./scripts/meta-extend heroui      # HeroUI component library
./scripts/meta-extend dev-client  # EAS builds + TestFlight
./scripts/meta-extend perf        # Performance best practices
./scripts/meta-extend all         # Everything
```

Run `./scripts/meta-extend` to see all options.

## References

- `references/starting.md` — what to do when setup fails mid-way
- `references/gotchas.md` — common issues and fixes

## Troubleshooting

```bash
./scripts/meta-doctor ./my-app
```

If the doctor passes but things are still broken, start with `references/gotchas.md`.

## Stack

- [Expo Router](https://docs.expo.dev/router/introduction/) — file-based routing
- [Expo Dev Client](https://docs.expo.dev/develop/development-builds/introduction/) — custom native runtime
- [Uniwind](https://github.com/nickshanks347/uniwind) — Tailwind utilities for React Native
- [Tailwind CSS v4](https://tailwindcss.com/) — utility-first CSS

## License

MIT (see `LICENSE`).

## Notes

`SKILL.md` is the Claude Code-facing entrypoint (name/description + agent instructions). `README.md` is for humans browsing the repo.
