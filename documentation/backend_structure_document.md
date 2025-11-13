# Backend Structure Document

## 1. Backend Architecture

Our backend is built on Next.js with API Routes. It follows a modular, component-driven pattern that cleanly separates concerns and makes it easy for developers of any level to understand and extend.

- We use the **App Router** paradigm in Next.js. Layouts and pages are organized under the `app/` directory.
- **API Routes** live under `app/api/` and handle all server-side logic (data fetching, authentication, business rules).
- **TypeScript** ensures type safety end-to-end, from API inputs to database models.
- **Drizzle ORM** provides a type-safe layer over PostgreSQL, preventing common mistakes when querying or mutating data.

How it supports key qualities:
- **Scalability**: Next.js automatically splits code into smaller chunks, and our modular API structure lets us add or remove features without impacting unrelated parts. Vercel’s serverless functions scale on demand.
- **Maintainability**: Clear directory structure (`app/`, `components/`, `db/`, `lib/`) and consistent naming conventions reduce cognitive load for new team members.
- **Performance**: Server-side rendering (SSR) and static-site generation (SSG) handle different pages appropriately. Built-in caching and edge functions in Vercel speed up API responses.

---

## 2. Database Management

We rely on a managed **PostgreSQL** database paired with **Drizzle ORM** for object-relational mapping.

- Type: Relational (SQL) database.
- System: PostgreSQL (works with any managed provider: AWS RDS, Supabase, DigitalOcean, etc.).
- ORM: Drizzle ORM for type-safe queries.

Data practices:
- **Migrations**: Drizzle’s migration tool keeps schema changes in version control.
- **Seeding**: Custom scripts populate sample data (artists, albums, tracks) for local development.
- **Connection pooling**: The Drizzle/PostgreSQL client uses built-in pooling to manage concurrent connections.
- **Naming conventions**: Tables are plural (`users`, `playlists`), columns are snake_case.

---

## 3. Database Schema

**Human-Readable Overview**
- **users**: Registered users with credentials and profile settings.
- **sessions**: Tracks user sessions (managed by `better-auth`).
- **artists**: Music artists or bands.
- **albums**: Collections of tracks by one artist.
- **tracks**: Individual songs, each tied to an album and one or more artists.
- **playlists**: User-created collections of tracks.
- **playlist_tracks**: Join table connecting playlists and tracks.

**SQL Schema (PostgreSQL)**
```sql
-- Users table (handled by better-auth)
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  name TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Sessions table (better-auth)
CREATE TABLE sessions (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  token TEXT UNIQUE NOT NULL,
  expires_at TIMESTAMPTZ NOT NULL
);

-- Artists
CREATE TABLE artists (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  bio TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Albums
CREATE TABLE albums (
  id UUID PRIMARY KEY,
  artist_id UUID REFERENCES artists(id) ON DELETE SET NULL,
  title TEXT NOT NULL,
  cover_url TEXT,
  release_date DATE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Tracks
CREATE TABLE tracks (
  id UUID PRIMARY KEY,
  album_id UUID REFERENCES albums(id) ON DELETE SET NULL,
  title TEXT NOT NULL,
  duration_seconds INTEGER,
  audio_url TEXT NOT NULL,
  track_number INTEGER,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Playlists
CREATE TABLE playlists (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  description TEXT,
  is_public BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Playlist ↔ Track join
CREATE TABLE playlist_tracks (
  playlist_id UUID REFERENCES playlists(id) ON DELETE CASCADE,
  track_id UUID REFERENCES tracks(id) ON DELETE CASCADE,
  added_at TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (playlist_id, track_id)
);
```  

---

## 4. API Design and Endpoints

We follow a RESTful approach using Next.js API Routes. Each resource (users, playlists, tracks, etc.) has its own route folder.

Key endpoints:

- `/api/auth/[...all]`  
  Purpose: Sign up, sign in, sign out, session management (provided by `better-auth`).

- `/api/users`  
  • GET `/api/users/me`: Fetch current user profile  
  • PUT `/api/users/me`: Update user settings  

- `/api/playlists`  
  • GET `/api/playlists`: List user’s playlists  
  • POST `/api/playlists`: Create a new playlist  
  • GET `/api/playlists/[id]`: Fetch playlist details & tracks  
  • PUT `/api/playlists/[id]`: Rename or reconfigure visibility  
  • DELETE `/api/playlists/[id]`: Remove playlist

- `/api/tracks`  
  • GET `/api/tracks/[id]`: Fetch track metadata and audio URL

- `/api/albums`  
  • GET `/api/albums/[id]`: Fetch album details and track list

- `/api/artists`  
  • GET `/api/artists/[id]`: Fetch artist bio and albums

- `/api/search`  
  • GET `/api/search?q=...`: Full-text search across artists, albums, tracks

All routes:
- Validate inputs in `route.ts`.  
- Use Drizzle ORM for database operations.  
- Return JSON responses with consistent shapes (e.g., `{ data: ..., error: ... }`).

---

## 5. Hosting Solutions

**Platform**: Vercel (recommended) for the Next.js application; managed PostgreSQL for the database.

- **Vercel** automatically deploys on each push, provides serverless functions for APIs, and global CDN for static assets.
- **Managed PostgreSQL** (Supabase, AWS RDS, DigitalOcean) ensures automated backups and failover.

Benefits:
- **Reliability**: Built-in health checks and rollback on Vercel.  
- **Scalability**: Vercel scales serverless functions horizontally; the database can scale vertically and horizontally (read replicas).  
- **Cost-effectiveness**: Pay-as-you-go on Vercel; open-source ORM and local Docker setup keep development costs low.

---

## 6. Infrastructure Components

- **Load Balancer / Edge Network**: Handled by Vercel’s edge network, routing requests to the nearest serverless function.
- **CDN**: Vercel CDN caches static assets (images, CSS, JavaScript) at the edge.
- **Caching**: 
  • **Server-side**: Next.js ISR, SWR for stale-while-revalidate caching.  
  • **Database**: Optional Redis layer for heavy read patterns (e.g., top charts, popular playlists).
- **Docker**: Used locally to spin up a consistent PostgreSQL environment.

How they interact:
- Client requests hit the Vercel edge.  
- Static files served from CDN.  
- API calls routed to serverless functions with minimal cold start latency.  
- Serverless functions query the PostgreSQL database (or Redis cache) and return JSON.

---

## 7. Security Measures

- **Authentication**: `better-auth` using secure, HTTP-only cookies.  
- **Authorization**: Middleware in Next.js layouts checks user sessions and redirects unauthorized access.  
- **Data Encryption**: TLS for all network traffic.  
- **Input Validation**: All API payloads validated with Zod or a similar schema library before database operations.  
- **Secrets Management**: Environment variables in Vercel for database URL, JWT secrets, OAuth keys.  
- **Rate Limiting**: Optional edge function or third-party service (e.g., Cloudflare) to throttle abusive requests.
- **Database Security**: Least-privileged DB user; separate read-only role for analytics if needed.

---

## 8. Monitoring and Maintenance

- **Monitoring**:  
  • Vercel’s built-in analytics for request rates, latencies, and error rates.  
  • Optional integration with Sentry for error tracking in serverless functions.  
  • Database metrics via your provider’s dashboard (CPU, connections, slow queries).
- **Logging**: API functions log to stdout; Vercel aggregates logs. For deeper insights, integrate with Logflare or Datadog.
- **Maintenance**:  
  • Automated database migrations with Drizzle CLI on deploy.  
  • Scheduled database backups and health checks by the managed provider.  
  • Regular dependency updates using a bot (Dependabot) and manual security reviews.

---

## 9. Conclusion and Overall Backend Summary

This backend is a modern, scalable, and secure foundation for a web-based music player or a headless API for native clients. Key points:

- **Next.js + TypeScript + Drizzle ORM** deliver type safety and developer productivity.  
- **PostgreSQL** stores relational music data with clear schemas for users, playlists, tracks, albums, and artists.  
- **Next.js API Routes** expose a well-structured RESTful API for all core features: authentication, library management, search, and playback metadata.  
- **Vercel + Docker** ensure effortless local development and production deployment with global performance via built-in CDN and serverless scaling.  
- **Security**, **monitoring**, and **maintenance** practices are in place to protect user data and keep the service reliable.

Unique advantages:
- Ready to serve both a web UI and native Kotlin/Compose clients in a headless architecture.  
- Modular design allows you to plug in features—like real-time listening charts, collaborative playlists, or third-party catalog integrations—without rewiring the core.

This setup gets you safely past boilerplate and into building the features that make your music service stand out.