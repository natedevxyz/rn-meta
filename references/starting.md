# Manual Setup

Use when `meta-start` fails mid-way. Complete remaining steps from where it stopped.

## Steps

### 1. Scaffold project
```bash
pnpm create expo-app@latest /path/to/my-app --template default
cd /path/to/my-app
```

### 2. Install dev client
```bash
npx expo install expo-dev-client
```

### 3. Build for iOS Simulator
```bash
npx expo run:ios
```

### 4. Install Uniwind + Tailwind
```bash
pnpm add uniwind tailwindcss
```

### 5. Create global.css
```css
@import 'tailwindcss';
@import 'uniwind';
```

### 6. Create metro.config.js
```js
const { getDefaultConfig } = require('expo/metro-config');
const { withUniwindConfig } = require('uniwind/metro');

const config = getDefaultConfig(__dirname);

module.exports = withUniwindConfig(config, {
  cssEntryFile: './global.css',
});
```

### 7. Add CSS TypeScript declarations
Create `global.d.ts`:
```ts
import 'react-native';

declare module '*.css';
declare module '*.module.css';

declare module 'react-native' {
  interface ViewProps {
    className?: string;
  }

  interface TextProps {
    className?: string;
  }
}
```

### 8. Import global.css in the Router layout
Current Expo default templates may use either `app/` or `src/app/`.

If your layout is `app/_layout.tsx`, add at the top:
```tsx
import '../global.css';
```

If your layout is `src/app/_layout.tsx`, add at the top:
```tsx
import '../../global.css';
```

### 9. Add a deliberate UniWind smoke component
Use `src/components/MetaSmoke.tsx` when the app root is `src/app/` or the project already has `src/`; otherwise use `components/MetaSmoke.tsx`.

```tsx
import { Text, View } from 'react-native';

export function MetaSmoke() {
  return (
    <View
      testID="rn-meta-uniwind-smoke"
      className="my-6 rounded-2xl border border-emerald-300 bg-emerald-500 px-5 py-4 shadow-lg"
    >
      <Text className="text-center text-lg font-bold text-white">
        UniWind works
      </Text>
      <Text className="mt-1 text-center text-sm font-medium text-emerald-50">
        rn-meta smoke proof
      </Text>
    </View>
  );
}
```

Render `<MetaSmoke />` from the visible home route: `app/index.tsx`, `src/app/index.tsx`, `app/(tabs)/index.tsx`, or `src/app/(tabs)/index.tsx`.

### 10. Run TypeScript
```bash
npx tsc --noEmit
```

### 11. Build the dev client
```bash
./scripts/meta-run /path/to/my-app
```

Use a specific iOS simulator/device when needed:
```bash
./scripts/meta-run /path/to/my-app ios --device <UDID-or-name>
```

### 12. Restart Metro
```bash
npx expo start --clear
```

`expo-dev-client` means `npx expo start --ios` expects the development build from `meta-run` to already exist on the simulator/device.

### 13. Verify
Run `./scripts/meta-doctor /path/to/project` - all checks should pass.

Runtime verification should include a screenshot or accessibility snapshot showing `UniWind works`.
