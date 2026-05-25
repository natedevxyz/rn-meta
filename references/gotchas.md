# Gotchas

Runtime issues that `meta-doctor` can't detect. Run doctor first - if it passes and you still have problems, check here.

## Simulator Issues

**Build succeeds but simulator doesn't open**
```bash
open -a Simulator
./scripts/meta-run /path/to/app ios --device <UDID-or-name>
```

**Xcode reports no usable simulator destination**
- Open Xcode → Settings → Platforms
- Install the iOS Simulator runtime that matches the Xcode destination error
- Retry with a specific simulator if needed:
```bash
./scripts/meta-run /path/to/app ios --device <UDID-or-name>
```

**"No development build" error in Expo Go**
- Meta uses dev client, not Expo Go
- Rebuild: `./scripts/meta-run /path/to/app`
- After the dev client is installed on the simulator/device, start Metro with `npx expo start --clear`

**App crashes on launch**
- Check Xcode console for native errors
- Try clean rebuild: `npx expo run:ios --clean`

## Styles Not Working

**Doctor fails because the UniWind smoke proof is missing**
- Ensure `MetaSmoke.tsx` exists under `src/components/` for `src/app/` projects or projects that already use `src/`, or `components/` for root `app/` projects
- Ensure the visible home route renders `<MetaSmoke />`
- Expected home routes: `app/index.tsx`, `src/app/index.tsx`, `app/(tabs)/index.tsx`, or `src/app/(tabs)/index.tsx`

**Smoke proof is present but not styled on device**
- Confirm the visible app screen shows `UniWind works`
- Restart Metro after config changes:
```bash
npx expo start --clear
```
- Rebuild the dev client if native dependencies or config changed:
```bash
./scripts/meta-run /path/to/app
```

**className has no effect (doctor passes)**
- Component may not support className - wrap in View with className
- Check component is from react-native, not a custom component missing style forwarding

**Styles work on iOS but not Android (or vice versa)**
- Some Tailwind utilities have platform-specific behavior
- Check Uniwind docs for platform support

## Metro Issues

**"Unable to resolve module" after installing package**
```bash
npx expo start --clear
```

**Changes not appearing (hot reload broken)**
- Save file again to trigger refresh
- If persists: `npx expo start --clear`

**Red screen with bundler error**
- Read the error message - usually syntax error or missing import
- Fix and save - Metro should recover

## Build Issues

**iOS build fails with CocoaPods error**
```bash
cd ios && pod install --repo-update && cd ..
npx expo run:ios
```

**"Signing requires a development team" error**
- Open `ios/*.xcworkspace` in Xcode
- Select target → Signing & Capabilities → set Team
