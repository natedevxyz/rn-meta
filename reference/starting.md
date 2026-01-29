# Manual Setup

Use when `meta-start` fails mid-way. Complete remaining steps from where it stopped.

## Steps

### 1. Scaffold project
```bash
pnpm create expo-app@latest my-app --template default
cd my-app
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

### 7. Import global.css in app/_layout.tsx
Add at the top:
```tsx
import '../global.css';
```

### 8. Restart Metro
```bash
npx expo start --clear
```

### 9. Verify
Run `./scripts/meta-doctor /path/to/project` - all checks should pass.
