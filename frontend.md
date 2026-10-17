# Frontend Development Rules

## React Web Application

### File Structure & Organization
- Use TypeScript for all React components and services
- Organize components by feature/page in separate folders
- Use CSS Modules for component-specific styling (e.g., `login.module.css`)
- Place shared components in `src/components/`
- Keep page-specific components in `src/pages/[PageName]/`

### Component Development
- Use functional components with hooks
- Prefer custom hooks for complex logic extraction
- Use React.memo() for performance optimization when needed
- Implement proper error boundaries for error handling
- Use React.lazy() for code splitting of routes

### State Management
- Use React Context API for global state (user auth, wallet balance)
- Use useState/useReducer for local component state
- Implement proper state normalization for complex data
- Use React Query/TanStack Query for server state management

### Styling Guidelines
- Use CSS Modules exclusively for component styling
- Follow BEM methodology within CSS modules
- Use CSS custom properties for theming
- Implement responsive design mobile-first
- Maintain consistent spacing using CSS variables

### API Integration
- Create dedicated service classes for API calls
- Use Axios interceptors for JWT token management
- Implement automatic token refresh logic
- Handle API errors gracefully with user-friendly messages
- Use TypeScript interfaces for API response types

### Security
- Sanitize all user inputs
- Store sensitive data securely (no localStorage for tokens)
- Implement Content Security Policy headers
- Use HTTPS for all API communications
- Validate data on both client and server sides

## React Native Mobile Application

The cross-platform mobile client lives in `mobile/` (iOS via Xcode, Android via Android Studio). It consumes the same Java Spring Boot APIs as this web app.

For all mobile work, follow the dedicated rules:

- **`mobile.md`** — shared RN conventions, folder layout, API integration, auth patterns
- **`mobile-ios-dev.md`** — Xcode setup, simulator vs device API URLs, ATS config
- **`mobile-android-dev.md`** — Android Studio setup, emulator networking, cleartext HTTP config

Mirror web types and services from `frontend/src/types/` and `frontend/src/services/` in the mobile app where endpoints overlap.