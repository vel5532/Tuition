# TEST 11 — TNPSC Exam Preparation Platform

A full-stack AI-powered TNPSC exam prep platform with subscription model, room-based competition, PDF upload, and AI explanations.

---

## 🗂️ Folder Structure

```
test11/
├── public/
│   └── index.html
├── src/
│   ├── contexts/
│   │   └── AuthContext.js          # Auth state + user data
│   ├── services/
│   │   ├── firebase.js             # Firebase init
│   │   ├── auth.js                 # Auth operations
│   │   ├── db.js                   # Firestore CRUD
│   │   ├── ai.js                   # OpenAI integration
│   │   ├── payment.js              # Razorpay integration
│   │   └── pdfExtractor.js         # PDF.js + AI extraction
│   ├── pages/
│   │   ├── AuthPage.js             # Login / Register
│   │   ├── user/
│   │   │   ├── Dashboard.js        # User home
│   │   │   ├── TestScreen.js       # Timed test
│   │   │   ├── ResultScreen.js     # Results + AI explain
│   │   │   ├── Leaderboard.js      # Global rankings
│   │   │   ├── PremiumPage.js      # Subscription upgrade
│   │   │   ├── UploadPDF.js        # PDF upload (premium)
│   │   │   ├── RoomsPage.js        # Create/Join rooms
│   │   │   └── RoomPlay.js         # Live room game
│   │   └── admin/
│   │       ├── AdminPanel.js       # Admin layout
│   │       ├── AdminUsers.js       # User management
│   │       ├── AdminQuestions.js   # Question bank
│   │       ├── AdminPayments.js    # Payment history
│   │       └── AdminLeaderboard.js # Leaderboard control
│   ├── App.js                      # Routes + role-based access
│   └── index.js                    # Entry point
├── firestore.rules                 # Security rules
├── firestore.indexes.json          # DB indexes
├── firebase.json                   # Deploy config
├── .env.example                    # Env template
└── package.json
```

---

## ⚙️ Setup Guide

### Step 1: Firebase Setup

1. Go to https://console.firebase.google.com
2. Create a new project (e.g., `test11-tnpsc`)
3. Enable **Authentication** → Email/Password
4. Create **Firestore Database** (start in production mode)
5. Go to Project Settings → General → Your apps → Add Web App
6. Copy the config values

### Step 2: Make First Admin

After signing up normally, go to Firebase Console → Firestore → `users` collection → find your user → manually set:
```
role: "admin"
```

### Step 3: OpenAI API Key

1. Go to https://platform.openai.com/api-keys
2. Create a key
3. Add to `.env` as `REACT_APP_OPENAI_API_KEY`

### Step 4: Razorpay Setup

1. Go to https://razorpay.com → Sign up
2. Dashboard → API Keys → Generate Key
3. Copy Key ID (starts with `rzp_`) to `.env`
4. For production, also set up webhook for payment verification

### Step 5: Install & Run

```bash
# Clone / extract project
cd test11

# Create env file
cp .env.example .env
# Edit .env with your actual keys

# Install dependencies
npm install

# Start development server
npm start
```

---

## 🚀 Deployment (Firebase Hosting)

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Initialize (select Hosting + Firestore)
firebase init

# Build React app
npm run build

# Deploy Firestore rules + indexes + hosting
firebase deploy

# Or deploy everything at once
firebase deploy --only hosting,firestore
```

---

## 🔥 Firebase Database Structure

```
users/{uid}
  ├── name: string
  ├── email: string
  ├── role: "user" | "admin"
  ├── isPremium: boolean
  ├── subscriptionExpiry: timestamp
  ├── roomCreateLimit: number  (11 per subscription)
  ├── roomJoinLimit: number    (11 per subscription)
  ├── roomCreatedCount: number
  ├── roomJoinedCount: number
  └── status: "active" | "blocked"

questions/{id}
  ├── question: string
  ├── options: string[]
  ├── answer: string
  ├── topic: string
  └── language: string

results/{id}
  ├── userId: string
  ├── score: number
  ├── total: number
  ├── percentage: number
  ├── passed: boolean
  └── topic: string

leaderboard/{userId}
  ├── userId: string
  └── totalScore: number

rooms/{id}
  ├── hostId: string
  ├── code: string  (6 chars)
  ├── users: string[]
  ├── questions: Question[]
  ├── status: "waiting" | "playing" | "done"
  └── liveScores: { [userId]: number }

payments/{id}
  ├── userId: string
  ├── type: "subscription" | "extension"
  ├── amount: number
  ├── razorpayPaymentId: string
  └── status: "completed"
```

---

## 💡 Key Features

| Feature | Details |
|---------|---------|
| Role-based routing | admin → /admin, user → /dashboard |
| Test system | 60s timer per question, auto-submit |
| Pass rule | 80% minimum to pass |
| AI explanations | OpenAI explains right/wrong answers |
| PDF extraction | PDF.js + AI parses MCQ questions |
| Room system | 6-char code, live scores via Firestore |
| Payment | Razorpay UPI, ₹11/month subscription |
| Room limits | 11 creates + 11 joins, pay ₹11 for 11 more |
| Admin panel | Full user/question/payment/leaderboard control |

---

## ⚠️ Production Checklist

- [ ] Replace all placeholder API keys in `.env`
- [ ] Enable Firebase App Check for security
- [ ] Set up Razorpay webhook for server-side payment verification
- [ ] Deploy Firestore security rules: `firebase deploy --only firestore`
- [ ] Enable Firebase Hosting CDN caching
- [ ] Set up Firebase Emulators for local testing
- [ ] Add Google Analytics to Firebase
- [ ] Configure custom domain in Firebase Hosting

---

## 🔐 Security Notes

- **API keys in frontend**: Firebase and Razorpay Key ID are safe to expose (they have their own security layers)
- **OpenAI key**: Consider proxying through a Cloud Function to hide the key
- **Firestore rules**: Already configured to prevent unauthorized access
- **Payment verification**: For production, verify Razorpay signature server-side using a Cloud Function

---

## 📱 Mobile Optimization

The app is mobile-first with:
- Responsive grid layouts
- Touch-friendly button sizes (minimum 44px)
- Viewport meta tag configured
- Smooth scrolling and transitions
