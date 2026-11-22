# Sprint 2 - DevOps, Deployment & Environment Specification

## 1. Local Development & Deployment Architecture
The **AI-Powered Polyglot Translator** runs in a zero-budget local development setup consisting of four core layers:
- **Frontend:** Chromium-based desktop browser (Google Chrome / Microsoft Edge) utilizing HTML, CSS, TypeScript, and the native Web Speech API for Speech-to-Text (STT) and Text-to-Speech (TTS).
- **Backend API:** Node.js + Express.js server written in TypeScript, exposing REST endpoints defined in `openapi.yaml` (default local server: `http://localhost:4499/api/v1`).
- **Database:** Local MySQL server managed via XAMPP for storing user profiles, hashed passwords (SHA-256), conversation history, and topic records.
- **External AI Service:** Google Gemini API accessed securely from the backend over HTTPS.

## 2. Environment Configuration & Secret Management
To prevent credential leaks in the repository:
- All sensitive keys (`GEMINI_API_KEY`, `JWT_SECRET`, `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`) must be stored exclusively in a local `.env` file.
- `.env` and `node_modules/` must remain listed in `.gitignore` and never be committed.
- A template file (`.env.example`) with placeholder values will be maintained for team onboarding.

## 3. Operational Non-Functional Standards (DevOps)
- **API Quota & Fault Handling:** Because the project uses the free tier of the Google Gemini API, rate-limit or timeout responses must be caught gracefully by the backend and returned as standardized JSON errors (`500` / `429`) without crashing the Node.js process.
- **Security:** User passwords are never stored in plain text (SHA-256 hashing), and session authentication uses JWT tokens.
- **Browser Compatibility:** Microphone access permissions and Web Speech API compatibility are verified on desktop Chromium browsers (Chrome and Edge).

## 4. Git-Flow & Quality Gate Rules
- **`main` branch:** Protected production/release branch. Direct pushes are disabled; updates occur only via reviewed Pull Requests from `develop`.
- **`develop` branch:** Protected integration branch where all Sprint deliverables are merged.
- **`feature/*` branches:** Every task is tracked via a GitHub Issue, implemented on a dedicated feature branch, and merged into `develop` after at least one peer approval (Code Review).
- **Commit History Integrity:** Pull Requests are merged using standard merge commits (no force-push or history rewriting) so every team member's contributions remain fully auditable.
- **Release Tagging:** Each completed sprint is frozen on `main` using semantic sprint tags (`sprint-1`, `sprint-2`, etc.).
