# React Native Mobile Development Rules

Applies to all work in `mobile/` — the cross-platform React Native app (iOS + Android).

## File Structure & Organization

- Use TypeScript for all screens, components, services, and hooks
- Mirror web types and API services from `frontend/src/` where endpoints overlap
- Folder layout:
  - `src/screens/` — full-page views (Login, Register, Home, etc.)
  - `src/navigation/` — React Navigation stacks and navigators
  - `src/services/` — Axios API clients (e.g. `authApi.ts`)
  - `src/hooks/` — custom hooks (e.g. `useAuth.tsx`)
  - `src/types/` — TypeScript interfaces shared with web where possible
  - `src/components/` — reusable UI (Button, Input, ErrorText)
- Platform-specific native config lives in `ios/` and `android/` only — keep JS/TS platform-agnostic

## Component Development

- Use functional components with hooks
- Extract complex logic into custom hooks (match web `useAuth` pattern in `frontend/src/hooks/useAuth.tsx`)
- Use React Navigation for all routing — no manual screen switching with boolean state
- Write platform-agnostic screens; use `Platform.OS` or platform-specific files (`*.ios.tsx`, `*.android.tsx`) only when UI or behavior genuinely differs

## Styling

- Use React Native `StyleSheet` consistently — no CSS Modules
- Do not import web CSS or use inline styles for entire screens; colocate styles with components

## API Integration

- Axios in dedicated service files — no raw `fetch` in screens
- Mirror endpoint paths and types from `frontend/src/services/authApi.ts` and `frontend/src/types/auth.ts`
- Handle errors via shared `getErrorMessage()` matching the web client
- **Phase 4+:** single `API_BASE_URL` → API Gateway (`:8080`); do not call auth/wallet ports from the app
- **Phase 1:** session cookies via `@react-native-cookies/cookies` (`JSESSIONID` from Spring Security)
- **Phase 2+:** JWT via Axios interceptors; store tokens in Keychain/Keystore via `react-native-keychain`
- Never store passwords or tokens in AsyncStorage

## Security

- Sanitize user inputs before sending to API
- Use secure storage (Keychain/Keystore) for tokens after Phase 2
- Use HTTPS in production; cleartext HTTP only for local dev (see platform dev rules)

## Performance

- Use `FlatList` for lists; avoid `ScrollView` for long transaction lists (Phase 3+)
- Minimize re-renders; memoize expensive list items where needed

## Testing Requirements

- Test every feature on **both** iOS and Android before marking a phase complete
- See `mobile-ios-dev.md` and `mobile-android-dev.md` for platform setup and API URL rules

## Related Rules

- iOS / Xcode setup: `mobile-ios-dev.md`
- Android / Android Studio setup: `mobile-android-dev.md`
- Web client conventions: `frontend.md`
