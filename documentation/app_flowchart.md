flowchart TD
    Start[User visits site] --> SignUp[Sign Up page]
    Start --> SignIn[Sign In page]
    SignUp --> AuthAPI[Auth API Route]
    SignIn --> AuthAPI
    AuthAPI --> DB[Drizzle ORM and Postgres]
    DB --> Dashboard[Dashboard page]
    Dashboard --> SessionCheck{Session valid}
    SessionCheck -->|Yes| Layout[Dashboard layout]
    SessionCheck -->|No| SignIn
    Layout --> Sidebar[Fetch playlists sidebar]
    Layout --> Main[Recommended albums main content]
    Sidebar -->|Select playlist| Playlist[Playlist page]
    Playlist --> DataTable[Display tracks table]
    Main -->|Click album| Album[Album details page]
    Playlist --> Player[Footer player controls]
    Album --> Player