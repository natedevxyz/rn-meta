# Approved Libraries

Before recommending or installing any React Native package, check this list.

**Rules:**
- ✅ = Use this
- ❌ = Never use, suggest the approved alternative
- If unlisted → Use judgment, prefer minimal dependencies

## Conflicts & Existing Projects

- If the user explicitly asks for an avoided library, explain the rn-meta preferred alternative and ask before proceeding with the avoided library.
- If an existing project already uses an avoided library, do not refactor unrelated code just to enforce this policy. Prefer the approved library for new work, and migrate existing usage only when it is in scope.
- If a companion skill recommends an avoided library, keep rn-meta's library policy in force unless the user approves an exception.
- If a project constraint makes the approved library impractical, surface the constraint and choose the smallest exception that solves the task.

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
| ✅ Use | `@legendapp/state@beta` with MMKV persistence - Default for shared, persisted, derived, synced, or performance-sensitive app state |
| ✅ Use | `react` (`useState`, `useReducer`, context) - Default for simple local or scoped component state |
| ❌ Avoid | `zustand` - Use Legend State for new app state |
| ❌ Avoid | `redux` / `@reduxjs/toolkit` - Too complex, use Legend State |
| ❌ Avoid | `mobx` - Use Legend State |

**State ownership:**
| State kind | Owner |
|------------|-------|
| Simple component-only values, toggles, sheet visibility, ephemeral UI flags | `useState` / `useReducer` |
| Scoped dependency injection or a small subtree value | React context, usually with local reducer/state |
| Server data, request lifecycle, cache invalidation, background refetch | React Query |
| Shared app state, persisted preferences, derived/computed state, local-first or offline-write workflows, high-frequency UI state | Legend State |
| Tokens, credentials, or other secrets | `expo-secure-store` |

**Legend State rules:**
- ✅ Start new projects with `@legendapp/state@beta` while Legend State v3 is in beta.
- ✅ Name observables with a `$` suffix, read them in React with `useValue`, and update them with `set()`.
- ✅ Use Legend State for state that is shared across screens, persisted, derived/computed, synced, offline-first, or updated frequently enough that fine-grained reactivity matters.
- ✅ Use `useState`/`useReducer` for boring local state: toggles, modal or sheet visibility, temporary component-only values, local animation flags, and values that do not need sharing or persistence.
- ✅ Persist only small durable app state: user preferences, session flags, onboarding flags, UI state.
- ❌ Do not replace every `useState` with Legend State. Local React state is still the right default for simple component state.
- ❌ Never persist large query caches in Legend State (use react-query persistence instead).
- ❌ Never store secrets in MMKV (use expo-secure-store)
- ❌ Do not mirror ordinary server state into Legend State when React Query already owns it. Use Legend State sync deliberately for local-first/offline-write workflows.

**Project conventions:**
- Put shared app stores in `src/state/*.ts` unless the app already has a clear state directory.
- Use a `$` suffix for observables (`appState$`, `session$`, `preferences$`).
- Prefer `useValue(...)` for React reads.
- Export small command functions for writes when they clarify intent or keep screen code readable.
- Keep tiny screen-only observables inside the component only when `useState`/`useReducer` is not enough.
- Do not use React context as a general app store.

**Migration guardrails:**
- Avoid Zustand for new work.
- Do not refactor existing Zustand code only to satisfy this policy.
- Migrate an existing Zustand store only when the user asks for the migration, or when you are already changing that state domain and the migration lowers complexity.

**Legend State + MMKV setup:**
```typescript
import { observable } from "@legendapp/state"
import { useValue } from "@legendapp/state/react"
import { ObservablePersistMMKV } from "@legendapp/state/persist-plugins/mmkv"
import { syncObservable } from "@legendapp/state/sync"

type ColorSchemePreference = "system" | "light" | "dark"

export const appState$ = observable({
  preferences: {
    colorScheme: "system" as ColorSchemePreference,
    hasSeenOnboarding: false,
  },
})

syncObservable(appState$, {
  persist: {
    name: "app-state",
    plugin: ObservablePersistMMKV,
  },
})

export function useColorSchemePreference() {
  return useValue(appState$.preferences.colorScheme)
}

export function setColorSchemePreference(value: ColorSchemePreference) {
  appState$.preferences.colorScheme.set(value)
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
| ✅ Use | `@expo/ui` `BottomSheet` - Default for native universal bottom sheets on Android, iOS, and web |
| ⚠️ Use sparingly | `@gorhom/bottom-sheet` - Only when you need advanced behavior not covered by `@expo/ui`, such as highly customized gestures or preserving complex mounted state while hidden |
| ❌ Avoid | `react-native-bottom-sheet` - Use `@expo/ui` `BottomSheet`, or `@gorhom/bottom-sheet` only for advanced cases |

Rule of thumb: use `@expo/ui` `BottomSheet` for bottom sheet UI. This includes action menus, filters, pickers, short forms, and other state-controlled sheet content.

Expo Router `formSheet` is a navigation presentation, not the default bottom sheet component. Do not use it for ordinary bottom sheet UI. Only consider it when the sheet must be a routed screen that participates in back navigation, deep links, route layouts, and screen lifecycle.

```tsx
import { useState } from "react";
import { BottomSheet, Button, Column, Host, Text } from "@expo/ui";

export function BottomSheetExample() {
  const [isPresented, setIsPresented] = useState(false);

  return (
    <Host style={{ flex: 1 }}>
      <Button label="Open sheet" onPress={() => setIsPresented(true)} />
      <BottomSheet
        isPresented={isPresented}
        onDismiss={() => setIsPresented(false)}
        snapPoints={["half", "full"]}
      >
        <Column spacing={12}>
          <Text textStyle={{ fontSize: 18, fontWeight: "700" }}>
            Sheet contents
          </Text>
          <Text>Drag down or tap the overlay to dismiss.</Text>
          <Button label="Close" onPress={() => setIsPresented(false)} />
        </Column>
      </BottomSheet>
    </Host>
  );
}
```

## Authentication
| Decision | Library |
|----------|---------|
| ✅ Use | `@clerk/expo` - Default auth provider |
| ✅ Use | `expo-apple-authentication` - Native Apple Sign In |
| ❌ Avoid | `@supabase/supabase-js` for auth - Use Clerk instead |
| ❌ Avoid | Supabase OAuth for Google - Use Clerk social auth instead |
| ❌ Avoid | Firebase Auth - Use Clerk instead |

**Auth flow:**
- Use Clerk as the identity provider and keep backend/database choices separate from auth.

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
