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
[Styling](#styling) | [Navigation](#navigation) | [Lists](#lists) | [Backend--API](#backend--api) | [State Management](#state-management) | [Data Fetching](#data-fetching) | [Storage](#storage) | [Forms & Inputs](#forms--inputs) | [Toasts](#toasts) | [Animations](#animations) | [Bottom Sheets](#bottom-sheets) | [Authentication](#authentication) | [Media & Files](#media--files) | [Camera](#camera) | [Subscriptions](#subscriptions--in-app-purchases) | [Analytics](#analytics) | [Images](#images) | [Error Monitoring](#error-monitoring) | [Push Notifications](#push-notifications)

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

## Backend & API
| Decision | Library |
|----------|---------|
| ✅ Use | `convex@^1.25.0` - The only app backend the client talks to; database, realtime sync, and server runtime |
| ✅ Use | Convex queries, mutations, actions, HTTP actions, and native React hooks |
| ❌ Avoid | Client-to-third-party API calls for business logic - Move vendor calls into Convex functions |
| ❌ Avoid | Generic client HTTP layers (`fetch`, `axios`, GraphQL clients, tRPC clients) for app data - Use Convex functions instead |
| ❌ Avoid | Direct database or storage SDKs from the client unless explicitly sanctioned below |

**Core rule:**
- The mobile client talks only to Convex for application data and business workflows.
- Convex is authoritative for database writes, realtime subscriptions, auth decisions, server-side business workflows, and media upload grants.
- Sanctioned exceptions are RevenueCat's SDK, approved telemetry/notification SDKs, and direct-to-R2 uploads using Convex-issued presigned URLs.
- Keep business logic in plain TypeScript modules. Convex functions should be thin adapters around those modules, and `lib/` must not import from Convex.

## State Management
| Decision | Library |
|----------|---------|
| ✅ Use | `@legendapp/state@beta` - Ephemeral client state and the dedicated MMKV last-known-good cache layer |
| ✅ Use | `react` (`useState`, `useReducer`, context) - Default for simple local or scoped component state |
| ❌ Avoid | `zustand` - Use Legend State for new app state |
| ❌ Avoid | `redux` / `@reduxjs/toolkit` - Too complex, use Legend State |
| ❌ Avoid | `mobx` - Use Legend State |

**State ownership:**
| State kind | Owner |
|------------|-------|
| Simple component-only values, toggles, sheet visibility, ephemeral UI flags | `useState` / `useReducer` |
| Scoped dependency injection or a small subtree value | React context, usually with local reducer/state |
| Server data, request lifecycle, cache invalidation, realtime updates | Convex hooks |
| Shared ephemeral app state, derived/computed client state, high-frequency UI state | Legend State |
| Durable non-secret preferences | MMKV directly, or a tiny dedicated wrapper that is not mixed with server data |
| Cold-start server-data paint before Convex connects | Dedicated MMKV last-known-good cache, overwritten by live Convex results |
| Tokens, credentials, or other secrets | `expo-secure-store` |

**Legend State rules:**
- ✅ Start new projects with `@legendapp/state@beta` while Legend State v3 is in beta.
- ✅ Name observables with a `$` suffix, read them in React with `useValue`, and update them with `set()`.
- ✅ Use Legend State for ephemeral state that is shared across screens, derived/computed client state, or values updated frequently enough that fine-grained reactivity matters.
- ✅ Use `useState`/`useReducer` for boring local state: toggles, modal or sheet visibility, temporary component-only values, local animation flags, and values that do not need sharing or persistence.
- ✅ Persist only small durable non-secret client state in MMKV, separate from server data.
- ❌ Do not replace every `useState` with Legend State. Local React state is still the right default for simple component state.
- ❌ Never use general Legend State stores as a server-data mirror.
- ❌ Never store secrets in MMKV (use expo-secure-store)
- ❌ Do not create client-side offline write queues for Convex data unless the task explicitly requires an offline-first product design.

**MMKV last-known-good cache:**
- Use a thin, dedicated Legend State persisted-observable layer to remember the last successful Convex query result for cold starts.
- Read this cache synchronously before the Convex websocket/session is ready so initial screens do not flash skeletons.
- Treat cached data as stale display data only. Do not mutate it directly, derive business decisions from it, or expose it as a general app store.
- Once `useQuery` or `usePaginatedQuery` returns live data, overwrite the cached value and render the Convex result.
- Scope cache keys by authenticated subject or anonymous guest id to avoid cross-account leaks.

**Project conventions:**
- Put shared app stores in `src/state/*.ts` unless the app already has a clear state directory.
- Use a `$` suffix for observables (`uiState$`, `draftState$`, `interactionState$`).
- Prefer `useValue(...)` for React reads.
- Export small command functions for writes when they clarify intent or keep screen code readable.
- Keep tiny screen-only observables inside the component only when `useState`/`useReducer` is not enough.
- Do not use React context as a general app store.

**Migration guardrails:**
- Avoid Zustand for new work.
- Do not refactor existing Zustand code only to satisfy this policy.
- Migrate an existing Zustand store only when the user asks for the migration, or when you are already changing that state domain and the migration lowers complexity.

**MMKV last-known-good cache sketch:**
```typescript
import { observable } from "@legendapp/state"
import { useValue } from "@legendapp/state/react"
import { ObservablePersistMMKV } from "@legendapp/state/persist-plugins/mmkv"
import { syncObservable } from "@legendapp/state/sync"

type DocumentSummary = { id: string; title: string }
type LkgState<T> = { value: T | null; updatedAt: number | null }

export function createDocumentListLkg(subjectId: string) {
  const documentListLkg$ = observable<LkgState<DocumentSummary[]>>({
    value: null,
    updatedAt: null,
  })

  syncObservable(documentListLkg$, {
    persist: {
      name: `lkg:document-list:${subjectId}`,
      plugin: ObservablePersistMMKV,
    },
  })

  return {
    useValue: () => useValue(documentListLkg$.value),
    remember: (value: DocumentSummary[]) =>
      documentListLkg$.set({ value, updatedAt: Date.now() }),
  }
}
```

## Data Fetching
| Decision | Library |
|----------|---------|
| ✅ Use | Convex native hooks: `useQuery`, `useMutation`, `useAction`, `usePaginatedQuery` |
| ✅ Use | MMKV last-known-good cache for synchronous cold-start display only |
| ❌ Avoid | `@tanstack/react-query`, React Query persistence, and SWR - Convex owns server state and subscriptions |
| ❌ Avoid | Client `fetch` / `axios` for app data - use Convex functions |

**Query rules:**
- Prefer narrow, indexed, paginated Convex queries. Treat broad subscriptions as a cost and performance bug.
- Use `usePaginatedQuery` for feeds, history, galleries, and document lists.
- Mutate through Convex mutations. Do not update server data through client stores.
- Use the MMKV last-known-good cache only as a stale read fallback during boot.

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
| ✅ Use | `@convex-dev/better-auth` - Better Auth running as a Convex component |
| ✅ Use | `better-auth@~1.6.x` and `@better-auth/expo@~1.6.x` |
| ✅ Use | `expo-secure-store` - Secure Better Auth cookie/session storage |
| ✅ Use | `expo-network`, `expo-linking`, `expo-web-browser`, `expo-constants` - Expo client integration pieces |
| ✅ Optional | `expo-apple-authentication` - Native Apple account upgrade with `identityToken` forwarded to Better Auth |
| ✅ Optional | `@react-native-google-signin/google-signin` - Native Google account upgrade, forwarding `idToken` to Better Auth |
| ❌ Avoid | `@clerk/expo` - Replaced by Better Auth inside Convex; avoids user-sync webhooks/JWT bridging and supports anonymous-first flows |
| ❌ Avoid | `@supabase/supabase-js` for auth - Use Better Auth Convex component instead |
| ❌ Avoid | Supabase OAuth for Google - Use native Google idToken flow into Better Auth instead |
| ❌ Avoid | Firebase Auth - Use Better Auth Convex component instead |

**Auth flow:**
- Boot every install into an anonymous Better Auth session.
- Anonymous-only apps are complete and valid. Do not require Apple or Google unless the product needs account recovery, cross-device continuity, subscriptions tied to an identity, or user-requested sign-in.
- Optional upgrades use native Apple/Google sheet flows by passing provider `idToken` values to `authClient.signIn.social`.
- Immediately call a Convex linking mutation after upgrade. The mutation must migrate documents from the anonymous guest id to the upgraded user id in one authoritative server-side workflow.
- Sign-out must never strand documents. Either preserve/restore an anonymous session or explicitly transfer ownership before ending the upgraded session.

**Better Auth Convex component setup:**
```bash
npm install convex@^1.25.0 @convex-dev/better-auth
npm install better-auth@~1.6 @better-auth/expo@~1.6
npx expo install expo-secure-store expo-network expo-linking expo-web-browser expo-constants

# Optional Apple account upgrade
npx expo install expo-apple-authentication

# Optional Google account upgrade
npm install @react-native-google-signin/google-signin
```

Register the component:
```ts
// convex/convex.config.ts
import { defineApp } from "convex/server";
import betterAuth from "@convex-dev/better-auth/convex.config";

const app = defineApp();
app.use(betterAuth);
export default app;
```

Configure Convex auth:
```ts
// convex/auth.config.ts
import { getAuthConfigProvider } from "@convex-dev/better-auth/auth-config";
import type { AuthConfig } from "convex/server";

export default {
  providers: [getAuthConfigProvider()],
} satisfies AuthConfig;
```

Add the Expo scheme and trusted origins. Include development `exp://` origins only in development:
```json
{
  "expo": {
    "scheme": "myapp"
  }
}
```

```ts
// convex/auth.ts
import { createClient, type GenericCtx } from "@convex-dev/better-auth";
import { convex } from "@convex-dev/better-auth/plugins";
import { betterAuth } from "better-auth/minimal";
import { anonymous } from "better-auth/plugins";
import { expo } from "@better-auth/expo";
import { components } from "./_generated/api";
import type { DataModel } from "./_generated/dataModel";
import authConfig from "./auth.config";

export const authComponent = createClient<DataModel>(components.betterAuth);

export const createAuth = (ctx: GenericCtx<DataModel>) =>
  betterAuth({
    trustedOrigins: [
      "myapp://",
      ...(process.env.NODE_ENV === "development" ? ["exp://", "exp://**"] : []),
    ],
    database: authComponent.adapter(ctx),
    plugins: [
      expo(),
      anonymous({
        disableDeleteAnonymousUser: true,
      }),
      convex({ authConfig }),
    ],
  });
```

For anonymous-only apps, omit `socialProviders`. Add only the upgrade providers the product actually supports inside `betterAuth({ ... })`:
```ts
socialProviders: {
  apple: {
    clientId: process.env.APPLE_CLIENT_ID!,
    clientSecret: process.env.APPLE_CLIENT_SECRET!,
  },
  google: {
    clientId: [
      process.env.GOOGLE_WEB_CLIENT_ID!,
      process.env.GOOGLE_IOS_CLIENT_ID!,
      process.env.GOOGLE_ANDROID_CLIENT_ID!,
    ],
    clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
  },
}
```

Mount routes in Convex HTTP:
```ts
// convex/http.ts
import { httpRouter } from "convex/server";
import { authComponent, createAuth } from "./auth";

const http = httpRouter();
authComponent.registerRoutes(http, createAuth);
export default http;
```

Initialize the Expo client:
```ts
// src/lib/auth-client.ts
import { createAuthClient } from "better-auth/react";
import { anonymousClient } from "better-auth/client/plugins";
import { convexClient } from "@convex-dev/better-auth/client/plugins";
import { expoClient } from "@better-auth/expo/client";
import Constants from "expo-constants";
import * as SecureStore from "expo-secure-store";

const scheme = Constants.expoConfig?.scheme as string;

export const authClient = createAuthClient({
  baseURL: process.env.EXPO_PUBLIC_CONVEX_SITE_URL,
  plugins: [
    expoClient({
      scheme,
      storagePrefix: scheme,
      storage: SecureStore,
    }),
    anonymousClient(),
    convexClient(),
  ],
});
```

On first launch, ensure a session exists before authenticated Convex queries are expected to hydrate. If there is no current Better Auth session, call `authClient.signIn.anonymous()`.

Wrap Expo Router with Convex and Better Auth. Keep `expectAuth: true` so queries wait for the session:
```tsx
// app/_layout.tsx
import { Slot } from "expo-router";
import { ConvexReactClient, ConvexProvider } from "convex/react";
import { ConvexBetterAuthProvider } from "@convex-dev/better-auth/react";
import { authClient } from "@/lib/auth-client";

const convex = new ConvexReactClient(process.env.EXPO_PUBLIC_CONVEX_URL as string, {
  expectAuth: true,
  unsavedChangesWarning: false,
});

export default function Layout() {
  return (
    <ConvexProvider client={convex}>
      <ConvexBetterAuthProvider client={convex} authClient={authClient}>
        <Slot />
      </ConvexBetterAuthProvider>
    </ConvexProvider>
  );
}
```

Optional native Apple upgrade:
```ts
import * as AppleAuthentication from "expo-apple-authentication";
import { authClient } from "@/lib/auth-client";

export async function upgradeWithApple() {
  const credential = await AppleAuthentication.signInAsync({
    requestedScopes: [
      AppleAuthentication.AppleAuthenticationScope.FULL_NAME,
      AppleAuthentication.AppleAuthenticationScope.EMAIL,
    ],
  });

  if (!credential.identityToken) throw new Error("Apple did not return an identity token");

  await authClient.signIn.social({
    provider: "apple",
    idToken: {
      token: credential.identityToken,
    },
  });
}
```

Optional native Google upgrade:
```ts
import { GoogleSignin } from "@react-native-google-signin/google-signin";
import { authClient } from "@/lib/auth-client";

GoogleSignin.configure({
  webClientId: process.env.EXPO_PUBLIC_GOOGLE_WEB_CLIENT_ID,
});

export async function upgradeWithGoogle() {
  await GoogleSignin.hasPlayServices();
  const result = await GoogleSignin.signIn();
  const tokens = await GoogleSignin.getTokens();
  const idToken = result.data?.idToken ?? tokens.idToken;

  if (!idToken) throw new Error("Google did not return an id token");

  await authClient.signIn.social({
    provider: "google",
    idToken: {
      token: idToken,
      accessToken: tokens.accessToken,
    },
  });
}
```

Account-linking mutation requirement:
- Store each user-owned document with the current Better Auth subject and, while anonymous, a stable guest id.
- After native idToken upgrade succeeds, call one Convex mutation that verifies the current session and moves all guest-owned documents to the upgraded user.
- Keep `disableDeleteAnonymousUser: true` on the anonymous plugin so the guest record cannot disappear before the migration completes.
- Make the mutation idempotent. It should be safe to retry after app restarts or network loss.

Boot sequence:
1. Render MMKV last-known-good data synchronously.
2. Establish or restore a Better Auth session. If none exists, create an anonymous session.
3. Connect the Convex client with `expectAuth: true`.
4. Subscribe through Convex hooks.
5. Replace cached data with live query results and persist the new last-known-good snapshot.

## Media & Files
| Decision | Library |
|----------|---------|
| ✅ Use | `@convex-dev/r2` - R2 component for presigned-URL media uploads and served media |
| ✅ Use | Convex file storage only for small, private, rarely served files |
| ❌ Avoid | Convex file storage for user media feeds, generated images/video/audio, thumbnails, or frequently served public files |

**R2 rules:**
- Ask Convex for a presigned upload URL, upload bytes directly to R2, then call a Convex mutation to attach the uploaded object key to app data.
- The presigned URL is a narrow exception to the "client only talks to Convex" rule. The client must not hold permanent R2 credentials.
- Keep access policy, object key generation, content validation, and ownership decisions in Convex.

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
