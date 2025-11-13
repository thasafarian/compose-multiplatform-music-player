# Tech Stack Document for Compose Multiplatform Music Player

This document explains, in everyday terms, the technology choices behind the `compose-multiplatform-music-player` project. It’s designed to be clear and approachable, so anyone—technical or not—can understand why we picked each tool and how it contributes to a modern web-based music service.

## 1. Frontend Technologies

These are the building blocks that make up what you see and interact with in your browser.

- **Next.js (App Router)**
  - A framework built on top of React that helps pages load quickly, supports server-side rendering (for faster first loads), and lets us split our UI into reusable pieces called “layouts” and “pages.”
- **React**
  - A popular library for creating dynamic user interfaces. We use React components for things like the play/pause buttons, album cards, and track lists.
- **TypeScript**
  - A version of JavaScript that adds “types,” which are like simple labels that help catch mistakes early. This makes our code more reliable as the project grows.
- **Tailwind CSS v4**
  - A utility-first styling approach that gives us ready-made classes (for margins, colors, fonts, etc.) so we can build custom designs very quickly without writing a lot of CSS from scratch.
- **shadcn/ui**
  - A set of pre-built, accessible user-interface components (forms, tables, modals, etc.) that we can style with Tailwind. This accelerates development and ensures consistency.
- **CSS Variables & Theming**
  - We use simple variables for colors and spacing, enabling a light/dark toggle and easy brand customizations.

How these choices enhance the user experience:

- Fast and smooth navigation with Next.js’s smart loading.
- Consistent look and feel thanks to Tailwind and shadcn/ui.
- Fewer visual glitches and more confidence in new features because TypeScript catches errors at “compile time.”

## 2. Backend Technologies

This is the part that runs on the server, handles data storage, and keeps your information safe.

- **Next.js API Routes**
  - Built into Next.js, these allow us to write server-side endpoints in the same codebase as our frontend. They power features like fetching playlists, saving new songs, and updating user settings.
- **better-auth**
  - A lightweight authentication library that manages user sign-up, sign-in, and session storage (who is logged in right now). It plugs directly into our Next.js API Routes.
- **PostgreSQL**
  - A reliable, open-source relational database that stores all the information: users, tracks, albums, playlists, and listening history.
- **Drizzle ORM**
  - A “type-safe” layer between our code and the database. Instead of writing raw database queries, we define simple models in code and let Drizzle generate safe queries for us.
- **TypeScript (Backend)**
  - The same type-checking benefits we enjoy on the frontend carry over here, reducing errors when reading or writing data.

How these components work together:

1. A client (your browser or a native app) calls one of our Next.js API endpoints.
2. The endpoint uses better-auth to check if you’re allowed to make that request.
3. If you’re authorized, Drizzle ORM translates our friendly code commands into SQL queries against PostgreSQL.
4. The database returns data, and the API sends it back to you in a structured format.

## 3. Infrastructure and Deployment

These choices determine where the app lives, how it’s updated, and how we ensure it stays up and running.

- **Docker (Local Development)**
  - Packages our database (and any other services) into containers so everyone on the team works with the same setup—no “it works on my machine” surprises.
- **Vercel (Production Hosting)**
  - A cloud platform optimized for Next.js apps. It handles automatic builds, global content delivery (CDN), and serverless functions for our API Routes.
- **Git & GitHub (Version Control)**
  - We track all changes in Git, host the code on GitHub, and collaborate through pull requests and code reviews.
- **CI/CD Pipeline**
  - Every time we push code, Vercel (and optional GitHub Actions) automatically runs tests and deploys the latest version if everything passes.

Benefits of these decisions:

- **Reliability**: Vercel’s global network means fast page loads almost everywhere.
- **Scalability**: As traffic grows, serverless functions and Docker containers can scale out without manual intervention.
- **Ease of Deployment**: Automatic builds on every code change keep our staging and production environments in sync.

## 4. Third-Party Integrations

These are external tools or services that we’ve plugged into our project to save time and add functionality.

- **better-auth**
  - Manages sign-up, sign-in, password reset flows, and session cookies out of the box.
- **shadcn/ui**
  - Offers a curated library of accessible UI components that we can drop into our design.
- **Tailwind CSS**
  - While not an external service, Tailwind is a maintained open-source project that accelerates styling.
- **Vercel Analytics (Optional)**
  - We can enable built-in analytics to monitor real-time traffic and page performance.

How they enhance functionality:

- Eliminates the need to build complex auth, styles, and UI patterns from zero.
- Provides battle-tested solutions that save development time and reduce bugs.

## 5. Security and Performance Considerations

We’ve built in safeguards and optimizations to keep data safe and the app snappy.

Security Measures:

- **Authentication & Session Management**: better-auth stores and verifies secure session cookies.
- **Protected Routes**: Next.js layouts check your session at every route, so private pages stay private.
- **Input Validation**: All API calls validate incoming data before touching the database.
- **Type Safety**: TypeScript and Drizzle prevent type mismatches and unexpected data shapes.

Performance Optimizations:

- **Server-Side Rendering (SSR)** for landing pages: Content is pre-rendered to reduce load times.
- **Code-Splitting**: Only the JavaScript needed for each page is sent to the browser.
- **Tailwind’s JIT Engine**: Generates only the CSS classes we actually use, keeping stylesheet size small.
- **Caching & CDNs**: Vercel automatically caches static assets around the globe.

## 6. Conclusion and Overall Tech Stack Summary

What we’ve chosen and why it matters:

- **Next.js & React** give us a single framework for both frontend and simple backend APIs, delivering a fast, scalable experience.
- **TypeScript & Drizzle ORM** provide type-safe code from the database all the way to the browser, reducing runtime errors.
- **Tailwind CSS & shadcn/ui** accelerate UI development, ensuring a clean, responsive design across all devices.
- **better-auth** secures user accounts without reinventing the wheel.
- **Docker & Vercel** handle our development and production environments, ensuring a consistent workflow and reliable deployment.

Unique aspects of this stack:

- A **headless architecture** that allows this codebase to serve both as a full web app and as a backend API for native clients (for example, a Kotlin Multiplatform mobile app).
- A **modern, type-safe approach** to building a data-intensive music service—everything from user sessions to playlists is covered by a single, coherent tech ecosystem.

Together, these technologies form a robust foundation for building and scaling a modern, feature-rich music player service, freeing you to focus on the creative aspects of your product—like discovery algorithms, unique listening features, and delightful user experiences.