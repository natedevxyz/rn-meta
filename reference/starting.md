# Expo + TypeScript + Uniwind (Dev Client) — Manual Setup

Goal: create a new Expo app (default template: Expo Router + TS), run as a dev build in iOS Simulator, and add Uniwind + Tailwind v4.

## Steps

### 1. Scaffold the project (Expo Router + TS)
```bash
pnpm create expo-app@latest my-app --template default
cd my-app
```

### 2. Install dev client + build for iOS Simulator
```bash
npx expo install expo-dev-client
npx expo run:ios
```

If the build succeeds but no simulator opens:
```bash
open -a Simulator
npx expo run:ios --device
```

### 3. Install Uniwind + Tailwind v4
```bash
pnpm add uniwind tailwindcss
```

### 4. Create global.css in project root
```css
@import 'tailwindcss';
@import 'uniwind';
```

### 5. Configure Metro for Uniwind (IMPORTANT)
Create `metro.config.js`:
```js
const { getDefaultConfig } = require('expo/metro-config');
const { withUniwindConfig } = require('uniwind/metro');

const config = getDefaultConfig(__dirname);

module.exports = withUniwindConfig(config, {
  cssEntryFile: './global.css',
});
```

**Gotcha:** Do NOT use `uniwind/config`. The correct import is `uniwind/metro`.

### 6. Import global.css from inside Router code
Add this at the top of `app/_layout.tsx`:
```tsx
import '../global.css';
```

### 7. Restart Metro (cache clear)
```bash
npx expo start --clear
```

### 8. Confirm Uniwind works
Add a `className` prop to any component:
```tsx
<Text className="text-green-500">Uniwind is working!</Text>
```

---

## Common Gotchas

1. **Wrong import path in Metro**
   - Must be: `require('uniwind/metro')`
   - Not: `require('uniwind/config')`

2. **global.css import must be inside Router code**
   - Must be at top of `app/_layout.tsx`: `import '../global.css';`

3. **You must restart Metro after Metro config changes**
   - Use: `npx expo start --clear`

---

## Daily Workflow

Rebuild native dev app (native changes):
```bash
npx expo run:ios
```

Fast refresh / dev server:
```bash
npx expo start
```

After config changes:
```bash
npx expo start --clear
```
