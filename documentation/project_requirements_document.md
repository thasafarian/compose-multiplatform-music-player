# Project Requirements Document for compose-multiplatform-music-player

## 1. Project Overview

This project is a Next.js–based full-stack starter kit for building a modern, web-based music player service. It bundles user authentication, a protected library, a database schema, and a polished UI so you can get a feature-rich music app up and running in minutes. Instead of wiring up every piece from scratch (sign-in forms, secure routes, database tables, UI components), you start with a solid foundation and focus on your unique features—like music discovery, social sharing, or advanced playback.

We’re building this because creating a reliable, scalable music platform requires a lot of boilerplate work: secure account handling, relational data models for tracks and playlists, responsive UI elements, theming, deployment pipelines, and more. Key objectives for success are:

• Users can sign up, sign in, and have private libraries and playlists.
• The app has a responsive, polished interface that works on desktop, tablet, and mobile.
• Developers can deploy locally via Docker and to production on Vercel with minimal fuss.
• The project can also serve as a headless API backend for a native mobile or desktop client.

## 2. In-Scope vs. Out-of-Scope

In-Scope (Version 1):

• User Accounts & Profile Management via **better-auth** (secure signup, signin, session handling).
• Protected Routes: only logged-in users can view their library, playlists, and settings.
• Dashboard Layout: header (search bar), left sidebar (playlists), main content area, and persistent footer with player controls.
• Playlist CRUD: create, read, update, delete playlists and track associations via Next.js API Routes.
• Music Catalog Schema: Drizzle ORM models for users, tracks, albums, artists, playlists, plus join tables.
• UI Components: album/artist cards, sortable data table for tracks, interactive listening-stats chart (using **shadcn/ui** and **Tailwind CSS**).
• Theming (dark/light mode) with CSS variables and Tailwind v4.
• Local Development: Docker setup for PostgreSQL.
• Deployment: ready for Vercel hosting.
• Type Safety: TypeScript end-to-end.

Out-of-Scope (Planned for Later Phases):

• Native audio playback engine (e.g., Howler.js or advanced streaming). Only a placeholder player is included.
• Native mobile or desktop client implementation (Kotlin Multiplatform / Jetpack Compose).
• Advanced recommendations or social features (sharing, comments, follows).
• Payment or subscription management.
• Offline/PWA support and client-side caching.
• Full accessibility audit (beyond base library defaults).

## 3. User Flow

A new visitor lands on the marketing or landing page (could be a public homepage or artist showcase). They click “Sign Up,” fill out the email and password form on `/app/sign-up`, and submit. The backend (Next.js API Route powered by **better-auth**) creates a new user record in the PostgreSQL database via Drizzle ORM, then issues a secure session cookie. The user is automatically redirected to the main `/app/dashboard` route.

On the Dashboard, the layout always shows a search bar in the header, a collapsible playlist sidebar on the left, and a player control bar fixed at the bottom. In the center, the main content area displays recommended albums, playlists, or artist pages using the `section-cards.tsx` component. The user clicks on a playlist in the sidebar, which navigates to `/app/playlists/[id]` and loads a sortable track table (`data-table.tsx`). Everything updates without a full page reload (client-side navigation), and the user can add or remove tracks, create a new playlist, or switch to dark mode using the theme toggle.

## 4. Core Features

- **Authentication & Profile**: Secure sign-up, sign-in, session cookies, and user profiles via `better-auth`.
- **Protected Routes**: Next.js App Router layouts enforce login checks before rendering private pages.
- **Dashboard Layout**: Header (search), sidebar (playlists), main content, footer (persistent player controls).
- **Playlist Management**: CRUD operations through Next.js API Routes (`app/api/playlists/route.ts`).
- **Music Catalog API**: Endpoints for fetching albums, tracks, artists using Drizzle ORM.
- **UI Components**: 
  • Album/Artist Cards (`section-cards.tsx`)  
  • Sortable Track Table (`data-table.tsx`)  
  • Interactive Chart (`chart-area-interactive.tsx`)
- **Database Schema**: PostgreSQL schema for users, tracks, albums, artists, playlists with many-to-many relations.
- **Theming**: Dark/light mode toggle with CSS variables.
- **Styling**: Tailwind CSS v4 utility classes.
- **Containerization**: Docker Compose for local PostgreSQL and dev server.
- **Deployment**: Vercel configuration for production hosting.
- **TypeScript**: Static typing from DB schema to UI props.
- **Extensibility Hooks**: Stub for `use-audio-player` to add custom playback logic later.

## 5. Tech Stack & Tools

• **Next.js (App Router)**: React–based framework for both frontend and backend API routes. Supports server‐side rendering (SSR) and client navigation.
• **React**: UI library for building interactive components.
• **shadcn/ui**: Accessible, pre-built component library that works with Tailwind CSS.
• **Tailwind CSS v4**: Utility-first CSS framework for rapid styling.
• **better-auth**: Authentication library for Next.js that handles signup, signin, and session management.
• **PostgreSQL**: Relational database for storing users, music catalog, playlists.
• **Drizzle ORM**: Type-safe database toolkit for defining schemas and queries in TypeScript.
• **Docker**: Container platform for consistent local development (runs PostgreSQL).
• **Vercel**: Cloud platform optimized for hosting Next.js applications.
• **TypeScript**: Superset of JavaScript that adds static types.
• **IDE Plugins** (optional): VS Code, Cursor (AI–powered code suggestions), Windsurf (ChatGPT integration).

## 6. Non-Functional Requirements

• **Performance**: API responses under 200 ms, initial page load under 1 s on 4G mobile.
• **Security**: HTTPS everywhere, secure HTTP-only cookies, CSRF protection, OWASP top-10 compliance, input validation on server side.
• **Usability**: Fully responsive design across breakpoints, intuitive navigation, theme toggle persisted in local storage.
• **Scalability**: Connection pooling for PostgreSQL, stateless API routes, ready for Vercel’s serverless scaling.
• **Maintainability**: Modular code organization, ESLint rules, Prettier formatting, clear naming conventions.
• **Accessibility**: Baseline WCAG 2.1 AA support via shadcn/ui components and semantic HTML.
• **Availability**: Aim for 99.9% uptime in production.

## 7. Constraints & Assumptions

• Requires Node.js v18+ and Docker installed for local dev.  
• Uses Next.js App Router paradigm—developers must follow that file-based routing style.  
• Assumes modern browsers (Chrome, Firefox, Safari, Edge) with ES6 support.  
• The authentication library (`better-auth`) must support Next.js layouts.  
• Drizzle ORM migrations must be run on each environment to keep schemas in sync.  
• No built-in cloud storage for audio files—developers must plug in their own CDN or storage solution later.

## 8. Known Issues & Potential Pitfalls

• **CORS & API Routes**: If you serve a separate native client, you’ll need correct CORS headers on API routes.  
• **Database Migrations**: Keep development and production schemas synchronized; consider a migration tool or CI check.  
• **Media Streaming Performance**: Large audio files on the same server may slow down requests. Plan for a CDN or object storage.  
• **Mobile Autoplay Restrictions**: Some mobile browsers block programmatic audio play; testing on real devices is essential.  
• **Dependency Upgrades**: Major upgrades of Next.js or Tailwind may introduce breaking changes; schedule periodic dependency reviews.  
• **Error Handling**: The starter covers basic flows—implement robust `try/catch` on API routes and global error boundaries in React.


---
This document is the single source of truth for the AI model and future technical specs. It leaves no room for guesswork about features, scope, or the development environment.