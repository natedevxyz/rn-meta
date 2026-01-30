# Approved Libraries

Before recommending or installing any React Native package, check this list.

**Rules:**
- ✅ = Use this
- ❌ = Never use, suggest the approved alternative
- If unlisted → Use judgment, prefer minimal dependencies

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
| ❌ Avoid | `@react-navigation/*` directly - Use expo-router instead |

## Lists
| Decision | Library |
|----------|---------|
| ✅ Use | `FlatList` (built-in) - Sufficient for most cases |
| ✅ Use | `SectionList` (built-in) - For grouped data |
| ❌ Avoid | `@shopify/flash-list` - Premature optimization; use FlatList unless you've profiled and proven it's needed |

## State Management
| Decision | Library |
|----------|---------|
| ✅ Use | `zustand` - Simple, minimal, no boilerplate |
| ❌ Avoid | `redux` / `@reduxjs/toolkit` - Too complex, use zustand |
| ❌ Avoid | `mobx` - Use zustand |
| ✅ Use | `react` (useState, useReducer, context) - For simple local state |

## Data Fetching
| Decision | Library |
|----------|---------|
| ✅ Use | `@tanstack/react-query` - Server state management |
| ✅ Use | `fetch` (built-in) - Simple requests |
| ❌ Avoid | `axios` - Unnecessary dependency, use fetch |
| ❌ Avoid | `swr` - Use react-query instead |

## Storage
| Decision | Library |
|----------|---------|
| ✅ Use | `expo-secure-store` - Sensitive data (tokens, credentials) |
| ✅ Use | `@react-native-async-storage/async-storage` - Non-sensitive data |
| ❌ Avoid | `mmkv` - Use async-storage unless you've proven performance need |

## Forms
| Decision | Library |
|----------|---------|
| ✅ Use | `react-hook-form` - Performant, minimal re-renders |
| ❌ Avoid | `formik` - Use react-hook-form instead |

## Animations
| Decision | Library |
|----------|---------|
| ✅ Use | `react-native-reanimated` - Complex animations |
| ✅ Use | `Animated` (built-in) - Simple animations |
| ❌ Avoid | `moti` - Use reanimated directly |

## Icons
| Decision | Library |
|----------|---------|
| ✅ Use | `@expo/vector-icons` - Included with Expo |
| ❌ Avoid | `react-native-vector-icons` - Use @expo/vector-icons |

## HTTP Client
| Decision | Library |
|----------|---------|
| ✅ Use | `fetch` + `react-query` - Standard pattern |
| ❌ Avoid | `axios` - Unnecessary abstraction |

## Date/Time
| Decision | Library |
|----------|---------|
| ✅ Use | `date-fns` - Modular, tree-shakeable |
| ❌ Avoid | `moment` - Deprecated, large bundle |
| ❌ Avoid | `dayjs` - Use date-fns |
