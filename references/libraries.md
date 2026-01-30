# Approved Libraries

Before recommending or installing any React Native package, check this list.

**Rules:**
- ✅ = Use this
- ❌ = Never use, suggest the approved alternative
- If unlisted → Use judgment, prefer minimal dependencies

## Contents
[Styling](#styling) | [Navigation](#navigation) | [Lists](#lists) | [State Management](#state-management) | [Data Fetching](#data-fetching) | [Storage](#storage) | [Forms & Inputs](#forms--inputs) | [Toasts](#toasts) | [Animations](#animations) | [Bottom Sheets](#bottom-sheets) | [Authentication](#authentication) | [Camera](#camera) | [Subscriptions](#subscriptions--in-app-purchases) | [Analytics](#analytics) | [Images](#images) | [Error Monitoring](#error-monitoring) | [Push Notifications](#push-notifications)

## Styling
| Decision | Library |
|----------|---------|
| ✅ Use | `uniwind` - Tailwind v4 for React Native |
| ❌ Avoid | `nativewind` - Use uniwind instead |
| ❌ Avoid | `styled-components` - Use uniwind instead |
| ❌ Avoid | `tamagui` - Use uniwind instead |

## Navigation
| Decision | Library |
|----------|---------|
| ✅ Use | `expo-router` - File-based routing, built on React Navigation |
| ✅ Use | `NativeTabs` from `expo-router/unstable-native-tabs` - Default for tab bars |
| ✅ Use | `react-native-bottom-tabs` - Fallback if NativeTabs has issues |
| ❌ Avoid | `@react-navigation/bottom-tabs` - Use NativeTabs or react-native-bottom-tabs instead |
| ❌ Avoid | `@react-navigation/*` directly - Use expo-router instead |

## Lists
| Decision | Library |
|----------|---------|
| ✅ Use | `@legendapp/list` - Default for feeds and virtualized lists |
| ✅ Use | `FlatList` (built-in) - Ok for small lists |
| ❌ Avoid | `@shopify/flash-list` - Use @legendapp/list instead |

## State Management
| Decision | Library |
|----------|---------|
| ✅ Use | `zustand` with persist middleware + MMKV storage - For persisted state |
| ✅ Use | `react` (useState, useReducer, context) - For local/shared state |
| ❌ Avoid | `redux` / `@reduxjs/toolkit` - Too complex, use zustand |
| ❌ Avoid | `mobx` - Use zustand |

**Zustand persistence rules:**
- ✅ Persist only small data: user prefs, session flags, UI state
- ❌ Never persist large query caches in zustand (use react-query persistence instead)
- ❌ Never store secrets in MMKV (use expo-secure-store)

**Zustand + MMKV setup:**
```typescript
import { StateStorage } from 'zustand/middleware'
import { MMKV } from 'react-native-mmkv'

const storage = new MMKV()

export const zustandStorage: StateStorage = {
  setItem: (name, value) => storage.set(name, value),
  getItem: (name) => storage.getString(name) ?? null,
  removeItem: (name) => storage.delete(name),
}
```

## Data Fetching
| Decision | Library |
|----------|---------|
| ✅ Use | `@tanstack/react-query` + `@tanstack/react-query-persist-client` + `@tanstack/query-sync-storage-persister` + `axios` - Server state with MMKV persistence and axios as HTTP client |
| ❌ Avoid | `fetch` (built-in) - Use axios instead |
| ❌ Avoid | `swr` - Use react-query instead |

**React Query + MMKV setup:**
```typescript
import { createSyncStoragePersister } from '@tanstack/query-sync-storage-persister'
import { MMKV } from 'react-native-mmkv'

const storage = new MMKV()

const mmkvStorage = {
  setItem: (key: string, value: string) => storage.set(key, value),
  getItem: (key: string) => storage.getString(key) ?? null,
  removeItem: (key: string) => storage.delete(key),
}

export const queryPersister = createSyncStoragePersister({ storage: mmkvStorage })

// In App.tsx:
// <PersistQueryClientProvider persistOptions={{ persister: queryPersister }}>
```

## Storage
| Decision | Library |
|----------|---------|
| ✅ Use | `react-native-mmkv` - Default for all local storage |
| ✅ Use | `expo-secure-store` - Only for sensitive data (tokens, credentials) when encryption is required |
| ❌ Avoid | `@react-native-async-storage/async-storage` - Use mmkv instead |

## Forms & Inputs
| Decision | Library |
|----------|---------|
| ✅ Use | `react-hook-form` + `zod` + `@hookform/resolvers` - Any screen with inputs should use react-hook-form |
| ✅ Use | `react-native-keyboard-controller` - Keyboard handling (KeyboardAwareScrollView, KeyboardToolbar, KeyboardStickyView) |
| ❌ Avoid | `formik` - Use react-hook-form instead |
| ❌ Avoid | `yup` - Use zod instead |
| ❌ Avoid | `KeyboardAvoidingView` (built-in) - Use react-native-keyboard-controller instead |

## Toasts
| Decision | Library |
|----------|---------|
| ✅ Use | `sonner-native` - Toast notifications |
| ❌ Avoid | `react-native-toast-message` - Use sonner-native instead |
| ❌ Avoid | `burnt` - Use sonner-native instead |

## Animations
| Decision | Library |
|----------|---------|
| ✅ Use | `@shopify/react-native-skia` with Skottie - For Lottie animations |
| ❌ Avoid | `lottie-react-native` - Use Skia's Skottie instead |

## Bottom Sheets
| Decision | Library |
|----------|---------|
| ✅ Use | Expo Router native sheets (see example below) - iOS only |
| ✅ Use | `@gorhom/bottom-sheet` - Cross-platform, or when you need to persist state while sheet is hidden |
| ❌ Avoid | `react-native-bottom-sheet` - Use expo-router formSheet or @gorhom/bottom-sheet |

```tsx
// iOS only - formSheet presentation
<Stack.Screen
  name="<your-route-name>"
  options={{
    presentation: "formSheet",
    gestureDirection: "vertical",
    animation: "slide_from_bottom",
    headerShown: false,
    sheetGrabberVisible: true,
    sheetInitialDetentIndex: 0,
    sheetAllowedDetents: [0.33, 0.5, 1.0],
    contentStyle: {
      backgroundColor: "#fff",
      flex: 1,
      justifyContent: "flex-end",
    },
  }}
/>
```

## Authentication
| Decision | Library |
|----------|---------|
| ✅ Use | `@supabase/supabase-js` - Auth provider, start with anonymous users |
| ✅ Use | `expo-apple-authentication` - Native Apple Sign In |
| ✅ Use | Supabase OAuth for Google - Web-based Google Sign In via `signInWithOAuth({ provider: 'google' })` |
| ❌ Avoid | `@react-native-google-signin/google-signin` - Use Supabase OAuth instead |
| ❌ Avoid | Firebase Auth - Use Supabase instead |
| ❌ Avoid | Clerk - Use Supabase instead |

**Auth flow:**
- Start anonymous → link Apple/Google to existing user (no second account created)

## Camera
| Decision | Library |
|----------|---------|
| ✅ Use | `react-native-vision-camera` - Camera with frame processors |
| ❌ Avoid | `expo-camera` - Use react-native-vision-camera instead |

## Subscriptions & In-App Purchases
| Decision | Library |
|----------|---------|
| ✅ Use | `react-native-purchases` - RevenueCat SDK for subscriptions and IAP |
| ❌ Avoid | `expo-in-app-purchases` - Use RevenueCat instead |
| ❌ Avoid | `react-native-iap` - Use RevenueCat instead |

## Analytics
| Decision | Library |
|----------|---------|
| ✅ Use | `posthog-react-native` - Analytics and feature flags |
| ❌ Avoid | `@amplitude/analytics-react-native` - Use PostHog instead |
| ❌ Avoid | `@react-native-firebase/analytics` - Use PostHog instead |
| ❌ Avoid | `mixpanel-react-native` - Use PostHog instead |

## Images
| Decision | Library |
|----------|---------|
| ✅ Use | `expo-image` - Image display with caching and performance |
| ❌ Avoid | `react-native` Image - Use expo-image instead |
| ❌ Avoid | `react-native-fast-image` - Use expo-image instead |

## Error Monitoring
| Decision | Library |
|----------|---------|
| ✅ Use | `@sentry/react-native` - Error tracking and performance monitoring |
| ❌ Avoid | `bugsnag-react-native` - Use Sentry instead |
| ❌ Avoid | `@datadog/mobile-react-native` - Use Sentry instead |

## Push Notifications
| Decision | Library |
|----------|---------|
| ✅ Use | `expo-notifications` - Push notifications |
| ❌ Avoid | `@react-native-firebase/messaging` - Use expo-notifications instead |
| ❌ Avoid | `react-native-push-notification` - Use expo-notifications instead |

