# Refined Product Backlog
## Project: AI-Powered Polyglot Translator
**Course:** BM321 / SENG 321 – Software Engineering  
**Team:** Team 25  
**Product Owner:** Hasan Mert Kaya  
**Version:** 2.0 (Sprint 2 Refinement)  
**Related Issue:** #23  

---

## 1. Backlog Overview & Refinement Summary
This document presents the refined Product Backlog for Sprint 2, expanding upon the initial backlog defined in Sprint 1 (`Release sprint-1`). All User Stories (`US-01` to `US-14`) are explicitly mapped to the functional requirements (`FR1`–`FR18`) specified in `docs/SRS.md`, prioritized using the **MoSCoW** method, estimated using Fibonacci **Story Points**, and assigned across team roles.

### Priority & Status Legend
- **Priority:** `Must Have` (Critical for core MVP), `Should Have` (High value, secondary), `Could Have` (Enhancement), `Won't Have` (Out of scope due to zero-budget constraint)
- **Status:** `In Progress` (Sprint 2 active), `Planned` (Sprint 2/3 scheduled), `Backlog` (Future sprint), `Excluded` (Out of scope)

---

## 2. Epic Summary

| Epic ID | Epic Name | Related FRs | Total Story Points | Primary Focus |
| :--- | :--- | :--- | :---: | :--- |
| **EP-01** | User Authentication & Profile Management | FR1, FR2, FR3 | 10 pts | Registration, SHA-256 hashing, JWT sessions, language preferences |
| **EP-02** | Real-Time Voice Conversation & AI Engine | FR4, FR5, FR6, FR7, FR8, FR15 | 24 pts | Topic selection, Web Speech STT/TTS, Gemini dialogue, hints, text fallback |
| **EP-03** | Automated Feedback & Progress Analytics | FR10, FR12, FR13, FR14 | 16 pts | Grammar/vocabulary analysis, post-session report, transcripts, dashboard |
| **EP-04** | Administrative & Content Management | FR16, FR17, FR18 | 11 pts | RBAC middleware, user registry management, topic CRUD |
| **EP-05** | Constraint-Excluded Capabilities | FR9, FR11 | 0 pts | Phoneme-level scoring & acoustic fluency (excluded due to budget) |

---

## 3. Detailed Product Backlog Items

### Epic 1: User Authentication & Profile Management (`EP-01`)

| Story ID | FR Ref | User Story | Breakdown Tasks | Acceptance Criteria | Priority | Story Points | Dependencies | Owner | Status |
| :--- | :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **US-01** | FR1 | As a **learner**, I want to register an account with my email and password so that my learning history is saved. | - T1.1: Design `users` MySQL table schema.<br>- T1.2: Implement SHA-256 password hashing in Node.js.<br>- T1.3: Build `POST /api/auth/register` endpoint & form validation. | Duplicate emails/usernames return HTTP `409`; valid registrations store SHA-256 hashed passwords in MySQL and return HTTP `201`. | Must Have | 3 pts | None | Ege Berk G. | In Progress |
| **US-02** | FR2 | As a **learner**, I want to log in securely and maintain an authenticated session so I can access personalized features. | - T2.1: Implement `POST /api/auth/login` credential verification.<br>- T2.2: Configure JWT token generation & auth middleware.<br>- T2.3: Build Login UI view and token storage. | Valid credentials return a signed JWT; invalid credentials return HTTP `401`; protected routes reject missing/expired tokens. | Must Have | 4 pts | US-01 | Ege Berk G. | In Progress |
| **US-03** | FR3 | As a **learner**, I want to select my target language (out of 12 languages) and proficiency level so the AI adapts to my skill level. | - T3.1: Add `target_language` and `proficiency_level` columns to `users`.<br>- T3.2: Build `PUT /api/users/preferences` endpoint.<br>- T3.3: Create language & level selector in UI. | Selected language (12 options) and level (`Beginner`, `Intermediate`, `Advanced`) persist in DB and inject into AI prompts. | Should Have | 3 pts | US-02 | Hasan Mert K. | Planned |

---

### Epic 2: Real-Time Voice Conversation & AI Engine (`EP-02`)

| Story ID | FR Ref | User Story | Breakdown Tasks | Acceptance Criteria | Priority | Story Points | Dependencies | Owner | Status |
| :--- | :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **US-04** | FR4 | As a **learner**, I want to browse and select a conversation topic so I can practice realistic real-life scenarios. | - T4.1: Seed `topics` table with initial scenarios.<br>- T4.2: Create `GET /api/topics` & `POST /api/sessions` endpoints.<br>- T4.3: Implement Topic Selection Dashboard UI. | Selecting a topic creates an active session in MySQL and triggers an initial contextual greeting from the AI partner. | Must Have | 3 pts | US-02 | Hasan Mert K. | In Progress |
| **US-05** | FR5 | As a **learner**, I want my spoken words transcribed into text in real time so I can speak naturally without typing. | - T5.1: Integrate browser `SpeechRecognition` (Web Speech API).<br>- T5.2: Handle microphone permissions and interim/final transcript states.<br>- T5.3: Display live speech transcription in chat UI. | Spoken audio in Chrome/Edge converts to accurate text in real time; clear error alert shown if mic permission is denied. | Must Have | 5 pts | US-04 | Ege Berk G. | In Progress |
| **US-06** | FR6 | As a **learner**, I want the AI partner to generate natural, context-aware responses so the dialogue feels realistic. | - T6.1: Integrate Google Gemini API SDK in Express backend.<br>- T6.2: Build prompt builder incorporating topic, proficiency, and chat history.<br>- T6.3: Handle API rate limits and timeouts gracefully. | Backend returns a relevant, grammatically natural AI reply within acceptable latency and logs both turns in MySQL. | Must Have | 5 pts | US-04, US-05 | M. Yasin H. | In Progress |
| **US-07** | FR7 | As a **learner**, I want to hear the AI's responses spoken aloud so I can improve my listening comprehension. | - T7.1: Integrate browser `speechSynthesis` (TTS) API.<br>- T7.2: Map 12 supported languages to matching browser voice locales.<br>- T7.3: Add replay/mute audio controls to chat bubbles. | AI text responses automatically vocalize in the target language accent with synchronized text display and replay option. | Must Have | 3 pts | US-06 | Ege Berk G. | In Progress |
| **US-08** | FR8 | As a **learner**, I want to request an instant translation or reply hint during a conversation when I get stuck. | - T8.1: Create `POST /api/sessions/:id/hint` endpoint using Gemini API.<br>- T8.2: Add "Translate" and "Suggest Hint" buttons to conversation UI. | Clicking hint/translate displays native-language translation or 2 suggested responses inline without breaking session state. | Could Have | 5 pts | US-06 | Hasan Mert K. | Backlog |
| **US-09** | FR15 | As a **learner**, I want to type my messages manually when I cannot use a microphone so I can still practice anywhere. | - T9.1: Add text input field alongside mic toggle in Conversation Room.<br>- T9.2: Route typed messages to the standard `POST /api/sessions/:id/message` flow. | Typed text submits seamlessly to Gemini API and triggers standard AI reply and grammar logging. | Should Have | 3 pts | US-06 | Ege Berk G. | Planned |

---

### Epic 3: Automated Feedback & Progress Analytics (`EP-03`)

| Story ID | FR Ref | User Story | Breakdown Tasks | Acceptance Criteria | Priority | Story Points | Dependencies | Owner | Status |
| :--- | :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **US-10** | FR10 | As a **learner**, I want the system to detect grammar and vocabulary mistakes in my sentences so I learn from my errors. | - T10.1: Design structured JSON evaluation prompt for Gemini API.<br>- T10.2: Parse error category, original phrase, correction, and explanation.<br>- T10.3: Store feedback records in `feedback_logs` table. | System accurately identifies grammatical/lexical errors per user turn and persists structured corrections in MySQL. | Must Have | 5 pts | US-06 | M. Cavit D. | Planned |
| **US-11** | FR12 | As a **learner**, I want a post-session feedback summary screen so I can review all my mistakes after finishing a dialogue. | - T11.1: Create `GET /api/sessions/:id/summary` aggregation endpoint.<br>- T11.2: Build Post-Session Feedback Summary UI component. | Ending a session displays highlighted errors, corrected versions, explanations, and overall session accuracy score. | Must Have | 5 pts | US-10 | M. Cavit D. | Planned |
| **US-12** | FR13 | As a **learner**, I want to view my past conversation transcripts so I can revisit previous dialogues and corrections. | - T12.1: Create `GET /api/sessions/history` endpoint.<br>- T12.2: Build Session History list & transcript modal view. | Authenticated users can view a chronological list of past sessions and inspect full dialogue logs and feedback. | Should Have | 3 pts | US-11 | Furkan Y. | Backlog |
| **US-13** | FR14 | As a **learner**, I want a progress dashboard showing my completed sessions and frequent mistake types so I can track growth. | - T13.1: Write SQL aggregation queries for session counts and error categories.<br>- T13.2: Build Progress Dashboard metrics cards in UI. | Dashboard accurately displays total sessions, practiced languages, total turns, and top grammar error patterns. | Could Have | 3 pts | US-12 | Furkan Y. | Backlog |

---

### Epic 4: Administrative & Content Management (`EP-04`)

| Story ID | FR Ref | User Story | Breakdown Tasks | Acceptance Criteria | Priority | Story Points | Dependencies | Owner | Status |
| :--- | :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **US-14** | FR16, FR17 | As an **admin**, I want role-based access control and a user management view so I can secure the platform and manage accounts. | - T14.1: Add `role` (`learner`/`admin`) check middleware.<br>- T14.2: Create `GET /api/admin/users` & `PATCH /api/admin/users/:id/status`.<br>- T14.3: Build Admin User Management table. | Non-admin requests to `/api/admin/*` return HTTP `403`; admins can view users and toggle active/inactive status. | Should Have | 5 pts | US-02 | Yiğit G. | Planned |
| **US-15** | FR18 | As an **admin**, I want to add, edit, and delete conversation topics so learners always have fresh practice scenarios. | - T15.1: Implement `POST`, `PUT`, `DELETE` endpoints for `/api/admin/topics`.<br>- T15.2: Build Topic Management form in Admin Panel. | Admins can create, update, or remove topics with custom AI prompt instructions; changes reflect immediately for learners. | Should Have | 6 pts | US-14 | Furkan Y. | Backlog |

---

### Epic 5: Constraint-Excluded Items (`EP-05` – Out of Scope)

| Item ID | FR Ref | Feature Description | Exclusion Rationale | Priority |
| :--- | :---: | :--- | :--- | :---: |
| **EX-01** | FR9 | Phoneme-Level Pronunciation Accuracy Scoring | Excluded due to zero-budget academic constraint; requires paid commercial speech-assessment APIs (e.g., ELSA/Azure Pronunciation Assessment). | Won't Have |
| **EX-02** | FR11 | Acoustic Fluency & Prosody Analysis | Excluded due to absence of paid raw-audio acoustic processing engines; fluency is evaluated strictly via textual structure. | Won't Have |

---

## 4. Sprint 2 Execution Scope (Current Sprint)
For **Sprint 2**, the team is delivering complete architectural, UI/UX, requirements, quality, and API specifications alongside core foundational setup:
- **Delivered in Sprint 2:** `docs/SRS.md`, `docs/PRODUCT_BACKLOG.md`, `docs/ARCHITECTURE.md` (C4 & UML), `docs/testing/` (NFR & Quality Specs), `ui-prototype.html`, `user-flows.md`, `openapi.yaml`, and `CONTRIBUTIONS.md`.
- **Target Implementation Baseline (Sprint 3):** `US-01`, `US-02`, `US-04`, `US-05`, `US-06`, `US-07`, `US-10`, `US-11`.
