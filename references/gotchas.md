# Gotchas

Runtime issues that `meta-doctor` can't detect. Run doctor first - if it passes and you still have problems, check here.

## Simulator Issues

**Build succeeds but simulator doesn't open**
```bash
open -a Simulator
npx expo run:ios --device
```

**"No development build" error in Expo Go**
- Meta uses dev client, not Expo Go
- Rebuild: `npx expo run:ios`

**App crashes on launch**
- Check Xcode console for native errors
- Try clean rebuild: `npx expo run:ios --clean`

## Styles Not Working

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

## Coming Soon

Additional gotchas for: state management, lists, storage, navigation patterns.
