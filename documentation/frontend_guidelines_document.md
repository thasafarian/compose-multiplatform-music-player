# Frontend Guidelines: compose-multiplatform-music-player

This document describes the frontend setup for the `compose-multiplatform-music-player` starter project. It explains the architecture, design approach, technologies, and best practices so you can confidently build and scale your web-based music service.

## 1. Frontend Architecture

### Frameworks and Libraries
- **Next.js (App Router)**: Provides file-based routing, server-side rendering (SSR), and client-side navigation in one package. It lets pages feel fast like a single-page app while still supporting SEO-friendly server rendering for public pages.
- **React**: Powers interactive UI components (play controls, dynamic lists, charts). We mix server components (for data fetching) with client components (for user interactions).
- **TypeScript**: Ensures type safety throughout the stack—from database schema to UI props—reducing runtime errors in a data-heavy app like a music player.
- **Tailwind CSS v4**: A utility-first styling framework that accelerates custom design without leaving your markup.
- **shadcn/ui**: A library of accessible, pre-built React components styled with Tailwind. It offers forms, tables, modals, and more, ready to adapt to your brand.
- **better-auth**: Handles secure sign-up, sign-in, and session management via Next.js API Routes.

### Scalability, Maintainability, Performance
- **Modular File Structure**: We separate features by folder (`app/`, `components/`, `hooks/`, `lib/`) so new functionality plugs in without chaos.
- **Server and Client Boundaries**: Data fetching lives in server components or API routes, while interactive logic lives in client components. This split keeps bundles small and loads only what the user needs.
- **Type Safety End-to-End**: From Drizzle ORM schema definitions to component props, TypeScript catches mismatches early.
- **SSR and Static Optimization**: Next.js automatically optimizes pages that don’t need real-time data, reducing load times.

## 2. Design Principles

### Usability
- Clear, consistent layouts (header, sidebar, footer) let users find play controls, search, and navigation in the same place on every screen.
- Pre-built patterns (cards, tables, charts) guide developers toward familiar interaction models.

### Accessibility
- All interactive components from `shadcn/ui` follow ARIA best practices and keyboard-navigation support out of the box.
- We include semantic HTML elements and proper `alt` text on images.

### Responsiveness
- Tailwind’s responsive utilities ensure the design adapts to mobile, tablet, and desktop breakpoints.
- Navigation transforms (e.g., collapsing sidebar) happen automatically based on viewport width.

## 3. Styling and Theming

### Styling Approach
- **Utility-First with Tailwind CSS**: Write small, composable classes in JSX instead of large CSS files. This reduces context-switching and keeps styles close to markup.
- **No Traditional CSS Methodologies**: Tailwind’s scoped classes replace BEM or SMACSS.
- **Component Variants**: Use `shadcn/ui` variants along with Tailwind’s `@apply` in global CSS for shared utilities.

### Theming
- **Dark/Light Toggle**: A CSS variable strategy toggles colors on the root element. The user’s preference is stored in local storage.
- **Consistent Look & Feel**: All `shadcn/ui` components pick up theme colors automatically.

### Visual Style
- **Modern, Flat Design** with subtle glassmorphism touches on overlays (e.g., frosted sidebar background).
- **Color Palette**:
  • Primary: #1DB954 (vibrant green)  
  • Secondary: #191414 (deep charcoal)  
  • Accent: #FDD835 (warm yellow)  
  • Background Light: #F5F5F5  
  • Background Dark: #121212  
  • Text Light: #FFFFFF  
  • Text Dark: #333333

- **Font**: Inter (sans-serif) for a clean, modern look. Fallback to system UI fonts.

## 4. Component Structure

### Organization
- **`app/`**: Page and layout files. Folders map to routes (e.g., `/app/dashboard`).  
- **`components/`**: Reusable pieces like `SectionCards`, `DataTable`, `ChartAreaInteractive`.  
- **`hooks/`**: Custom hooks (e.g., `usePlayback`) for shared client logic.  
- **`lib/`**: Utilities and core logic (e.g., `lib/auth.ts` manages session checks). 

### Reuse and Maintainability
- Components accept props to customize data and appearance.  
- Shared UI patterns live in one place—update a card component once, and every instance updates.  
- Small, focused files are easier to test and refactor.

## 5. State Management

### Current Approach
- **Server-First Data**: Pages fetch their data via server components or `getServerSideProps`-style patterns, reducing client-side state needs for static content.
- **UI State with React**: Local component state handles toggles, form inputs, and filter options.

### Global Playback State (Recommendation)
- Use a lightweight store like **Zustand** or **Jotai** for:  
  • Current track and queue  
  • Play/pause status and volume  
  • Theme (dark/light) and user preferences  

This ensures the music keeps playing as users navigate the app.

## 6. Routing and Navigation

### Next.js App Router
- **File-Based Routing**: Folders inside `app/` become routes (e.g., `app/sign-in/`, `app/playlists/[id]/`).
- **Layouts**: Shared UI (header, sidebar, footer player) lives in `app/dashboard/layout.tsx`, so it persists across nested pages.
- **Dynamic Routes**: Bracket syntax (`[id]`) handles playlists, albums, or artist pages dynamically.

### User Flow
1. Sign up or sign in via `/sign-up` or `/sign-in`.  
2. Dashboard (`/dashboard`) checks session in layout and loads sidebar and controls.  
3. Click a playlist in sidebar to navigate to `/playlists/123`, which uses `DataTable` to show tracks.

## 7. Performance Optimization

- **Code Splitting**: Next.js automatically splits code per page.  
- **Dynamic Imports**: Load heavy components (e.g., charts) only when needed with `next/dynamic`.  
- **Image Optimization**: Use `next/image` for responsive, lazy-loaded images.  
- **Tailwind Purge**: Removes unused CSS classes in production, keeping bundle size small.  
- **Caching & CDN**: Vercel serves static assets and SSR pages from edge locations for ultra-fast delivery.

## 8. Testing and Quality Assurance

### Unit Tests
- **Jest + React Testing Library**: Test individual components (e.g., card rendering, button clicks).  

### Integration Tests
- Combine multiple components or pages to verify flows, such as logging in and viewing the dashboard.

### End-to-End Tests
- **Cypress or Playwright**: Automate critical user journeys:  
  • Sign up / Sign in  
  • Create and view a playlist  
  • Play, pause, and skip tracks

### Linting and Formatting
- **ESLint** with recommended React/TypeScript rules.  
- **Prettier** for consistent code style.  
- **Husky** pre-commit hooks to run tests and linters before code lands.

## 9. Conclusion and Overall Frontend Summary

This frontend guideline outlines a modern, scalable setup for a web-based music player:  
• Next.js with server and client components for fast, SEO-friendly pages.  
• React and `shadcn/ui` for accessible, reusable UI.  
• Tailwind CSS and CSS variables for rapid theming and responsive design.  
• TypeScript and Drizzle ORM for data integrity from database to UI.  
• A modular file structure that grows with your feature set.  
• Performance optimizations (code splitting, image optimization) for snappy user experiences.  
• A testing strategy that ensures quality from unit to end-to-end.

By following these guidelines, you’ll be able to deliver a polished, maintainable, and high-performance music service that delights users across devices.