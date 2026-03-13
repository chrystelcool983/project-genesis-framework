# Example: Mobile Consumer App

**Stack:** React Native (Expo) · TypeScript · Firebase · Node.js API (Cloud Run) · Google Cloud

**Project type:** Consumer iOS/Android app with offline support and real-time sync

---

## Stack Decisions

| Layer | Choice | Reason |
|-------|--------|--------|
| Mobile | React Native + Expo | Cross-platform, large ecosystem, Expo managed workflow for OTA updates |
| Backend | Node.js + Express on Cloud Run | Serverless scaling, minimal ops, same language as frontend |
| Database | Firestore | Real-time sync, offline-first, flexible schema for rapidly evolving product |
| Auth | Firebase Auth | Tight Firestore integration, social login out of the box |
| Storage | Firebase Storage | CDN-backed, direct mobile uploads without backend proxy |
| Push Notifications | FCM (via Expo) | Universal Android + iOS delivery |
| Analytics | Firebase Analytics + Mixpanel | User behaviour at the event level |
| Crash Reporting | Sentry | Better crash grouping than Firebase Crashlytics for React Native |

---

## Agents Used

**Standard:** `changelog`, `frontend`, `retro`, `docs`, `security`, `api`

**Mobile:** `mobile-ux`, `mobile-performance`, `mobile-platform`

**Custom:** `sync` — handles Firestore offline/online reconciliation, conflict resolution, and optimistic update rollbacks

---

## MCP Configuration

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/project"],
      "description": "Project file access"
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "Persistent context across sessions"
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "description": "Complex reasoning for multi-step tasks"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN_HERE" },
      "description": "Issue and PR management"
    },
    "expo": {
      "command": "npx",
      "args": ["-y", "@expo/mcp"],
      "env": { "EXPO_TOKEN": "YOUR_EXPO_TOKEN_HERE" },
      "description": "OTA updates, build management, Expo config"
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-mcp"],
      "env": {
        "FIREBASE_PROJECT_ID": "your-project-dev",
        "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/service-account-dev.json"
      },
      "description": "Firestore queries, Auth inspection, Remote Config — DEV project only"
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"],
      "description": "Testing Cloud Run API endpoints and webhooks"
    },
    "sentry": {
      "command": "npx",
      "args": ["-y", "@sentry/mcp"],
      "env": {
        "SENTRY_AUTH_TOKEN": "YOUR_SENTRY_TOKEN_HERE",
        "SENTRY_ORG": "your-org-slug",
        "SENTRY_PROJECT": "your-project-slug"
      },
      "description": "Crash reports, error trends, release health"
    }
  }
}
```

---

## Key Decisions & Rationale

**Why Expo over bare React Native?**
OTA updates are critical for a consumer app — we can ship bug fixes without App Store review delays. Expo's managed workflow also removes native build complexity for a small team.

**Why Firestore over PostgreSQL?**
The product is offline-first. Firestore's built-in offline persistence and real-time sync listeners would have taken weeks to replicate with a custom sync layer over REST.

**Why no Playwright?**
Mobile E2E uses Detox (React Native native testing), not browser-based testing. `playwright` MCP is replaced by manual Detox runs in CI.

**Mobile agents, not web agents:**
The app has no public web presence needing SEO. `mobile-platform` is critical because App Store submission is a recurring process with evolving requirements (Apple's privacy manifest requirements in iOS 17 caught many teams off-guard).

**Why Sentry over Firebase Crashlytics?**
Sentry's React Native SDK produces better-grouped crash reports with full JS stack traces. Crashlytics required additional native symbolication setup that added friction.
