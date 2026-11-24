# Software Requirements Specification (SRS)
## Project: AI-Powered Polyglot Translator
**Course:** BM321 / SENG 321 – Software Engineering  
**Team:** Team 25  
**Document Owner:** Hasan Mert Kaya (Product Owner)  
**Version:** 2.0 (Sprint 2 Deliverable)  
**Related Issue:** #23  

---

## 1. Introduction

### 1.1 Purpose
This Software Requirements Specification (SRS) defines the complete functional requirements, external interfaces, system constraints, and use-case specifications for the **AI-Powered Polyglot Translator**. It establishes a baseline for implementation, architecture modeling, and quality assurance verification during Sprint 2 and subsequent development cycles.

### 1.2 Scope
The **AI-Powered Polyglot Translator** is an interactive, web-based language learning and conversational practice platform. It enables learners to engage in real-time spoken dialogues with an AI conversational partner across **12 supported languages** (primarily English, alongside 11 additional target languages), receive instant grammar and vocabulary feedback, review session transcripts, and track progress over time. Administrators manage conversation topics, user accounts, and system health.

### 1.3 Definitions, Acronyms, and Abbreviations
- **SRS:** Software Requirements Specification
- **STT:** Speech-to-Text (via Browser Web Speech API)
- **TTS:** Text-to-Speech (via Browser Web Speech API)
- **NLU:** Natural Language Understanding
- **LLM:** Large Language Model (Google Gemini API)
- **JWT:** JSON Web Token
- **CRUD:** Create, Read, Update, Delete
- **PO:** Product Owner

### 1.4 References
- *Team 25 – Sprint 1 Report (Release `sprint-1`)*
- *OpenAPI Specification (`openapi.yaml`)*
- *System Architecture Documentation (`docs/ARCHITECTURE.md`)*
- *User Flows Documentation (`user-flows.md`)*

---

## 2. Overall Description

### 2.1 Product Perspective
The system follows a client-server web architecture:
- **Frontend:** Browser-based Single Page Application (SPA) utilizing HTML/CSS/TypeScript and the native **Web Speech API** for audio capture (STT) and vocal synthesis (TTS).
- **Backend API:** **Node.js / Express.js** RESTful server managing authentication, business logic, session orchestration, and database persistence.
- **External AI Engine:** **Google Gemini API** providing contextual dialogue generation, translation assistance, and grammatical/lexical analysis.
- **Database:** Local **MySQL (XAMPP)** relational database storing user credentials, profiles, conversation topics, session transcripts, and feedback logs.

### 2.2 User Classes and Characteristics
1. **Primary Users (Learners):** Language learners seeking accessible, low-anxiety, real-time conversational speaking practice with automated grammatical feedback and progress tracking.
2. **Administrators:** System managers responsible for maintaining conversation topics, managing user accounts/roles, and monitoring platform usage and API quotas.

### 2.3 Operating Environment & Assumptions
- **Client Environment:** Chromium-based desktop web browsers (Google Chrome, Microsoft Edge) with active microphone hardware and granted browser audio permissions.
- **Server Environment:** Node.js runtime environment connected to a local MySQL instance via XAMPP.
- **Network:** Active internet connection required for communicating with the Google Gemini API.

### 2.4 Design and Implementation Constraints
- **Zero-Budget Constraint:** The system must rely exclusively on open-source technologies and free-tier APIs (Google Gemini API free tier, native browser Web Speech API).
- **Out-of-Scope Features (Constraint-Driven):**
  - **FR9 (Phoneme-Level Pronunciation Accuracy Evaluation):** Excluded because raw phonetic waveform scoring requires paid commercial speech-assessment APIs.
  - **FR11 (Advanced Audio-Based Fluency Measurement):** Excluded due to the absence of specialized paid acoustic/prosodic analysis engines.
- **API Rate Limits:** Free-tier Gemini API quotas may introduce occasional latency; the system must handle rate limits gracefully.

---

## 3. Specific Functional Requirements

### 3.1 User Authentication & Profile Management (FR1 – FR3)

- **FR1 – User Registration:**
  - *Description:* The system shall allow new learners to register an account using a unique username, email address, and password.
  - *Input:* Username, email, plaintext password, preferred native/target language.
  - *Processing:* Validate input formats, check email/username uniqueness, hash the password using **SHA-256**, and persist the user record in MySQL.
  - *Output:* Account creation confirmation and automatic redirection or token issuance.

- **FR2 – User Authentication & Session Management:**
  - *Description:* The system shall authenticate registered users and maintain secure stateless sessions.
  - *Input:* Email/username and password.
  - *Processing:* Hash submitted password with SHA-256, verify against stored hash, and generate a signed **JWT** upon successful match.
  - *Output:* JWT session token returned to the client; unauthorized attempts return HTTP `401`.

- **FR3 – User Language & Proficiency Preferences:**
  - *Description:* The system shall allow authenticated users to select and update their target practice language (from 12 supported languages) and proficiency level (Beginner, Intermediate, Advanced).
  - *Input:* Selected language code and proficiency level.
  - *Processing:* Update user profile preferences in the database to tailor AI prompt context.
  - *Output:* Updated user profile state reflected in subsequent practice sessions.

### 3.2 Real-Time Voice Conversation & AI Interaction (FR4 – FR8)

- **FR4 – Topic & Scenario Selection:**
  - *Description:* The system shall present a categorized list of conversation topics (e.g., Travel, Daily Life, Job Interview, Dining) for the user to initiate a session.
  - *Input:* Selected topic ID and target language.
  - *Processing:* Initialize a new conversation session record in MySQL and load the corresponding system prompt context.
  - *Output:* Active conversation workspace initialized with an opening AI greeting.

- **FR5 – Real-Time Speech-to-Text (STT) Audio Capture:**
  - *Description:* The system shall capture the user's spoken voice via the browser microphone and transcribe it into text in real time using the Web Speech API.
  - *Input:* Live microphone audio stream in the selected target language.
  - *Processing:* Stream audio through Web Speech Recognition API and display interim/final transcribed text in the UI.
  - *Output:* Accurate text transcript of the user's utterance ready for AI submission.

- **FR6 – Contextual AI Dialogue Generation:**
  - *Description:* The system shall send the user's transcribed message along with conversation history and topic context to the Google Gemini API to generate a natural, context-aware reply.
  - *Input:* User utterance text, session context, target language, and user proficiency level.
  - *Processing:* Construct a structured prompt and invoke the Google Gemini API endpoint via the Node.js backend.
  - *Output:* Contextual AI response text tailored to maintain natural conversational flow.

- **FR7 – Text-to-Speech (TTS) Vocal AI Response:**
  - *Description:* The system shall vocalize the AI partner's text response aloud using the browser's Web Speech Synthesis API.
  - *Input:* AI response text and target language locale.
  - *Processing:* Select an appropriate language voice profile and synthesize speech output.
  - *Output:* Audible voice playback accompanied by synchronized text display in the chat interface.

- **FR8 – Real-Time Contextual Translation & Hint Assistance:**
  - *Description:* The system shall allow learners to request an on-demand translation or suggested reply hint during an active conversation if they get stuck.
  - *Input:* "Translate message" or "Suggest hint" user trigger on a specific dialogue turn.
  - *Processing:* Query Gemini API for native-language translation or level-appropriate response hints without terminating the session.
  - *Output:* Inline translation or hint displayed cleanly in the UI.

### 3.3 Exclusions & Scope Boundaries (FR9, FR11)

- **FR9 – Phoneme-Level Pronunciation Evaluation *(Out of Scope)*:**
  - *Status:* Explicitly excluded from implementation due to zero-budget academic constraints prohibiting commercial phonetic scoring APIs.

- **FR11 – Acoustic Fluency & Prosody Scoring *(Out of Scope)*:**
  - *Status:* Explicitly excluded from implementation; fluency feedback relies strictly on textual grammar and lexical structure rather than raw waveform analysis.

### 3.4 Grammar Analysis, Feedback & Progress Tracking (FR10, FR12 – FR15)

- **FR10 – Automated Grammar & Vocabulary Error Detection:**
  - *Description:* The system shall analyze user utterances for grammatical errors, unnatural phrasing, and vocabulary misuse using the Google Gemini API.
  - *Input:* User's transcribed messages from the conversation turn or completed session.
  - *Processing:* Prompt Gemini API to return structured JSON identifying original text, error type, corrected version, and brief explanation.
  - *Output:* Structured list of grammatical and lexical corrections.

- **FR12 – Post-Session Feedback Summary View:**
  - *Description:* Upon ending a conversation session, the system shall display a comprehensive feedback report highlighting mistakes, suggested corrections, and stronger vocabulary alternatives.
  - *Input:* Session ID upon session completion.
  - *Processing:* Aggregate all turn-level feedback items stored for the session and compute an overall session accuracy indicator.
  - *Output:* Visual session summary screen with side-by-side original vs. corrected comparisons.

- **FR13 – Conversation Transcript History:**
  - *Description:* The system shall persist complete dialogue transcripts and allow users to review past sessions.
  - *Input:* Authenticated user ID.
  - *Processing:* Query past sessions and associated message logs from MySQL ordered by timestamp.
  - *Output:* Chronological list of past sessions with viewable full transcripts and feedback notes.

- **FR14 – Learning Progress & Activity Dashboard:**
  - *Description:* The system shall track and display user learning metrics, including total completed sessions, practiced languages, total spoken turns, and recurring grammar error categories.
  - *Input:* Aggregated user session and feedback records.
  - *Processing:* Compute summary statistics from MySQL tables.
  - *Output:* Dashboard metrics helping learners monitor improvement over time.

- **FR15 – Manual Text Input Fallback:**
  - *Description:* The system shall allow users to type their responses manually if microphone hardware is temporarily unavailable or in noisy environments.
  - *Input:* Text typed into the chat input field.
  - *Processing:* Route typed text through the exact same Gemini dialogue and grammar analysis pipeline (FR6, FR10) as STT input.
  - *Output:* Seamless continuation of the AI conversation.

### 3.5 Administrative Management (FR16 – FR18)

- **FR16 – Role-Based Access Control (RBAC):**
  - *Description:* The system shall enforce role separation between standard `Learner` accounts and `Admin` accounts.
  - *Input:* JWT claims containing user role (`learner` vs. `admin`).
  - *Processing:* Backend middleware validates role permissions before granting access to `/api/admin/*` endpoints.
  - *Output:* Authorized access for admins; HTTP `403 Forbidden` for non-admin users.

- **FR17 – User Account Administration:**
  - *Description:* The system shall allow administrators to view registered users, inspect account statuses, and activate or deactivate accounts.
  - *Input:* Admin user management actions (list, filter, toggle status).
  - *Processing:* Query or update user status flags in the MySQL `users` table.
  - *Output:* Updated user registry view.

- **FR18 – Conversation Topic & Prompt Management (CRUD):**
  - *Description:* The system shall allow administrators to create, read, update, and delete conversation topics and their underlying AI system prompt templates.
  - *Input:* Topic title, category, difficulty level, description, and AI prompt instructions.
  - *Processing:* Execute validated CRUD operations on the MySQL `topics` table.
  - *Output:* Immediately updated topic catalog available to learners.

---

## 4. External Interface Requirements

### 4.1 User Interfaces
- Responsive web interface supporting desktop Chromium browsers (Chrome/Edge).
- Key views: Authentication (Login/Register), Topic Selection Dashboard, Active Voice Conversation Room (with microphone status indicator, live transcript, TTS controls, and hint button), Post-Session Feedback Summary, History/Progress Dashboard, and Admin Panel.

### 4.2 Hardware Interfaces
- Standard desktop/laptop microphone for audio input (accessed via browser media permissions).
- Speakers or headphones for TTS audio playback.

### 4.3 Software & API Interfaces
- **Google Gemini API (REST/HTTPS):** Used by the Express.js backend for conversational response generation (FR6), translation/hints (FR8), and structured grammar evaluation (FR10).
- **Web Speech API (`SpeechRecognition` & `speechSynthesis`):** Native browser APIs utilized by the frontend for zero-cost STT (FR5) and TTS (FR7).
- **MySQL Database (via XAMPP):** Relational storage accessed by the backend for persistent data management.

---

## 5. Key Use Case Scenarios

### UC-01: Conduct Real-Time AI Voice Practice Session
- **Primary Actor:** Learner
- **Preconditions:** User is logged in (FR2), has selected a target language (FR3), and granted browser microphone permission.
- **Main Success Scenario:**
  1. Learner selects a conversation topic from the dashboard (FR4).
  2. System initializes the session and plays the AI's opening greeting via TTS (FR7).
  3. Learner clicks the microphone button and speaks a response; Web Speech API transcribes the audio into text (FR5).
  4. Backend sends the text to Google Gemini API and receives a contextual reply plus grammar analysis (FR6, FR10).
  5. System displays and speaks the AI's reply (FR7).
  6. Learner ends the session and reviews the post-session grammar feedback report (FR12).
- **Alternative Flow:** If microphone permission is denied or unavailable, the learner uses the text input box (FR15) to continue the dialogue.

### UC-02: Manage Conversation Topics (Admin)
- **Primary Actor:** Administrator
- **Preconditions:** User is authenticated with `Admin` privileges (FR16).
- **Main Success Scenario:**
  1. Administrator navigates to the Admin Panel.
  2. Administrator selects "Add New Topic" and enters title, difficulty, and AI prompt guidelines (FR18).
  3. System validates and saves the topic to MySQL.
  4. The new topic becomes immediately selectable by learners.

---

## 6. Traceability Matrix (FR to Sprint 2 Artifacts)

| Requirement ID | Requirement Title | Priority | Target Sprint / Status | Related Backlog Epic |
| :--- | :--- | :---: | :---: | :--- |
| **FR1** | User Registration (SHA-256) | High | Sprint 2 (In Progress) | Epic 1: User Auth & Profile |
| **FR2** | User Login & JWT Session | High | Sprint 2 (In Progress) | Epic 1: User Auth & Profile |
| **FR3** | Target Language & Level Preferences | Medium | Sprint 2 (Planned) | Epic 1: User Auth & Profile |
| **FR4** | Topic & Scenario Selection | High | Sprint 2 (In Progress) | Epic 2: Voice Conversation |
| **FR5** | Real-Time STT (Web Speech API) | High | Sprint 2 (In Progress) | Epic 2: Voice Conversation |
| **FR6** | Contextual AI Dialogue (Gemini API) | High | Sprint 2 (In Progress) | Epic 2: Voice Conversation |
| **FR7** | Vocal AI Response (TTS) | High | Sprint 2 (In Progress) | Epic 2: Voice Conversation |
| **FR8** | On-Demand Translation & Hints | Medium | Sprint 3 (Backlog) | Epic 2: Voice Conversation |
| **FR9** | Phoneme Pronunciation Evaluation | N/A | **Out of Scope** | Excluded (Budget Constraint) |
| **FR10** | Grammar & Vocabulary Error Detection | High | Sprint 2 (Planned) | Epic 3: Feedback & Analytics |
| **FR11** | Acoustic Fluency Scoring | N/A | **Out of Scope** | Excluded (Budget Constraint) |
| **FR12** | Post-Session Feedback Summary | High | Sprint 2 (Planned) | Epic 3: Feedback & Analytics |
| **FR13** | Conversation Transcript History | Medium | Sprint 3 (Backlog) | Epic 3: Feedback & Analytics |
| **FR14** | Learning Progress Dashboard | Low | Sprint 3 (Backlog) | Epic 3: Feedback & Analytics |
| **FR15** | Manual Text Input Fallback | Medium | Sprint 2 (Planned) | Epic 2: Voice Conversation |
| **FR16** | Role-Based Access Control (RBAC) | High | Sprint 2 (Planned) | Epic 4: Admin Management |
| **FR17** | User Account Administration | Low | Sprint 3 (Backlog) | Epic 4: Admin Management |
| **FR18** | Topic Management (CRUD) | Medium | Sprint 3 (Backlog) | Epic 4: Admin Management |
