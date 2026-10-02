# VibeSnap • Modern Social-Media Platform

A complete, responsive, full-stack social-media web application inspired by Instagram, created with original branding, clean modular architecture, and a dedicated integration placeholder for a multilingual code-mixed hate-speech detection model.

---

## ✨ Features

- **Authentication & Sessions**:
  - Sign Up with profile picture upload, username validation, email check, and password hashing (bcrypt).
  - Login via username or email with persistent JWT session.
  - One-click **Quick Demo Login** (`Alex Rivera` / `password123`).
  - Account settings, password change, and privacy toggle (Public vs. Private account).

- **Home Feed & Stories**:
  - Horizontal scrolling stories tray with gradient active rings.
  - Fullscreen Story Viewer with timed progress bars, next/prev slide navigation, pause on hold, like, and reply message input.
  - Responsive post feed with like button (animated double-tap heart pop), comment counter, share link copy, and save post toggle.
  - Right sidebar with user switch profile and "Suggested for you" accounts.

- **Post Creation**:
  - Drag-and-drop or file picker for images and MP4 videos.
  - Real-time media preview.
  - Caption with hashtag suggestions (`#vibesnap`, `#travel`, `#ai`, etc.).
  - Location tagging and audience visibility selector (Public vs. Followers Only).
  - Immediate optimistic appearance in feed upon publishing.

- **Multilingual Hate-Speech Moderation Placeholder**:
  - Dedicated modular service: `server/services/hateSpeechDetectionService.js`.
  - API endpoint: `POST /api/analyze-comment`.
  - Analyzes comments in **Tamil-English**, **Hindi-English**, **Malayalam-English**, and **Kannada-English**.
  - Rejects hate speech with custom warnings displaying detected language and confidence score.
  - Seamlessly accepts safe comments and stores them in the SQLite database.
  - Interactive "Hate Speech Tester" modal accessible from the navigation menu to test sample presets in real time.

- **Explore & Reels**:
  - Explore grid: 3-column discovery layout with hover overlays (likes & comments count) and detailed post modal.
  - Reels section: Fullscreen vertical snap scrolling with video autoplay (`IntersectionObserver`), mute/unmute control, like counter, and slide-up comments drawer.

- **Direct Messages & Notifications**:
  - Real-time chat bubbles, active partner list, user search to start new conversations.
  - Notifications for likes, comments, and new followers with timestamps.

- **Responsive Design & Theming**:
  - Full desktop view (left sidebar + centered feed + right sidebar).
  - Compact tablet layout.
  - Mobile bottom navigation bar and mobile top header.
  - Dynamic Dark & Light theme toggle.

---

## 🛠️ Tech Stack

- **Frontend**: React 19, Vite, Vanilla CSS Design System (Custom tokens, animations, glassmorphism), Lucide SVG icons.
- **Backend**: Node.js v24, Express, Multer (media upload), JSON Web Tokens (JWT), BcryptJS.
- **Database**: SQLite via Node.js built-in `node:sqlite` (Zero external database installation required).

---

## 🚀 Quick Start Guide

### 1. Install Dependencies
In the project root directory, run:
```bash
# Install backend dependencies
npm install

# Install frontend dependencies
npm --prefix client install
```

### 2. Seed Demo Database
Populate realistic users, high-definition posts, active stories, reels, comments, and messages:
```bash
npm run seed
```

**Demo Credentials**:
- **Username**: `demouser`
- **Password**: `password123`

### 3. Validate & Prepare ML Datasets
Validate and combine the code-mixed datasets for Tamil, Hindi, Malayalam, and Kannada:
```bash
# Validate and merge datasets into datasets/combined_dataset.csv
npm run dataset:prepare

# Or run unit tests for all 8 validator checks
npm run dataset:validate
```

### 4. Run the Application
Start both backend (port 5000) and frontend (port 5173) concurrently:
```bash
npm run dev
```

Open your browser at:
👉 **[http://localhost:5173](http://localhost:5173)**

---

## 🧠 Main Project Integration: Hate-Speech Detection

### Integration Architecture
When a user submits a comment:
```
User Enters Comment 
      ↓
Click Post / Comment
      ↓
Send to POST /api/analyze-comment
      ↓
hateSpeechDetectionService (Python Flask / FastAPI or local PyTorch/Transformers)
      ↓
If isHateSpeech === true  → Block comment + display warning alert with confidence & language
If isHateSpeech === false → Publish approved comment to SQLite database
```

### File Location:
- Backend Service: `server/services/hateSpeechDetectionService.js`
- API Route: `server/routes/analyzeComment.js`

### Plugging in your Trained Model:
In `server/services/hateSpeechDetectionService.js`, replace the internal logic with an HTTP call to your Python model endpoint (e.g. FastAPI):

```javascript
async function analyzeComment({ comment, postId, userId, languageHint }) {
  // Call your local FastAPI or Flask ML server:
  const response = await fetch('http://localhost:8000/predict', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ text: comment, language_hint: languageHint })
  });
  
  const result = await response.json();
  return {
    isHateSpeech: result.is_hate,
    confidence: result.confidence,
    language: result.language, // e.g. "Tamil-English", "Hindi-English"
    reason: result.is_hate ? 'Violation detected by multilingual model.' : 'Safe content.'
  };
}
```

---

## 📂 Project Structure

```
final-pp/
├── client/                     # Vite + React Frontend
│   ├── src/
│   │   ├── components/
│   │   │   ├── Auth/           # Login & Signup modal
│   │   │   ├── Explore/        # Discovery grid
│   │   │   ├── Feed/           # Stories tray, viewer, post cards, right sidebar
│   │   │   ├── HateSpeech/     # Live ML tester modal
│   │   │   ├── Messages/       # Direct messages & chat threads
│   │   │   ├── Modals/         # Create post modal, post detail modal
│   │   │   ├── Navigation/     # Desktop sidebar & mobile navigation
│   │   │   ├── Notifications/  # Notifications view
│   │   │   ├── Profile/        # Profile page, stats, posts grid
│   │   │   └── Settings/       # Settings, password, privacy
│   │   ├── context/            # AuthContext, ToastContext
│   │   ├── services/           # Centralized API client (api.js)
│   │   ├── styles/             # Vanilla CSS design tokens & utilities
│   │   ├── App.jsx             # Main application controller
│   │   └── index.css           # Pure Vanilla CSS design system
│   ├── index.html              # SEO metadata & Google Fonts
│   └── vite.config.js          # Vite config with backend proxy
├── data/
│   └── vibesnap.sqlite         # SQLite relational database
├── server/                     # Express REST API
│   ├── database/
│   │   └── db.js               # SQLite database & relational schema
│   ├── middleware/
│   │   ├── auth.js             # JWT verification middleware
│   │   └── upload.js           # Multer media storage middleware
│   ├── routes/
│   │   ├── analyzeComment.js   # POST /api/analyze-comment (ML placeholder)
│   │   ├── auth.js             # Authentication routes
│   │   ├── posts.js            # Posts feed, create, like, save, comments
│   │   ├── stories.js          # Stories routes
│   │   ├── reels.js            # Reels vertical video routes
│   │   ├── users.js            # User profile, follows, saved posts
│   │   ├── search.js           # Users, posts, and hashtag search
│   │   ├── messages.js         # Direct messages routes
│   │   ├── notifications.js    # Notifications routes
│   │   └── settings.js         # Settings routes
│   ├── services/
│   │   └── hateSpeechDetectionService.js # Hate speech detection service
│   ├── seed.js                 # Realistic seed dataset
│   └── server.js               # Express application entrypoint
└── package.json                # Root scripts and dependencies
```
