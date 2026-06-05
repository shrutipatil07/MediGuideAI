# MediGuide AI — Complete Product Walkthrough

> This document shows you the **finished product** before you write a single line of code.
> Read through it like you're a user opening the app for the first time.

---

## The Complete User Journey (Map)

```
First-time user                              Returning user
     │                                            │
     ▼                                            ▼
┌──────────┐    ┌──────────────┐    ┌─────────┐  ┌───────────┐
│ Landing  │───▶│  Register    │───▶│  Login  │──▶│ Dashboard │
│  Page    │    │  Page        │    │  Page   │  │           │
└──────────┘    └──────────────┘    └─────────┘  └─────┬─────┘
                                                       │
                          ┌────────────────────────────┼────────────────────┐
                          │                            │                    │
                          ▼                            ▼                    ▼
                   ┌─────────────┐            ┌──────────────┐    ┌──────────────┐
                   │  Symptom    │            │  Hospital    │    │    Cost      │
                   │  Analysis   │            │  Finder      │    │  Estimator   │
                   └──────┬──────┘            └──────┬───────┘    └──────────────┘
                          │                          │
                          │    "Find Hospitals"      │    "Compare"
                          ├─────────────────────────▶│
                          │                          ▼
                          │                   ┌──────────────┐
                          │                   │  Hospital    │
                          │                   │  Comparison  │
                          │                   └──────────────┘
                          │
                          ▼
                   ┌─────────────┐
                   │   User      │
                   │  History    │
                   └─────────────┘

Admin user (separate flow):
┌───────────┐    ┌──────────────────┐    ┌──────────────────┐
│  Admin    │───▶│  Hospital        │───▶│  Cost            │
│ Dashboard │    │  Management      │    │  Management      │
└───────────┘    └──────────────────┘    └──────────────────┘
```

---

## Screen 1: Landing Page

![Landing Page — what a first-time visitor sees](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\landing_page_1780682207481.png)

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 MediGuide AI                        [Login]  [Register]        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│              Your AI-Powered Healthcare Navigator                   │
│                                                                     │
│     Describe your symptoms in everyday language.                    │
│     Get medical guidance in seconds.                                │
│                                                                     │
│     ┌─────────────────────────────────────────────────┐             │
│     │  Describe your symptoms...                      │  [Analyze] │
│     └─────────────────────────────────────────────────┘             │
│                                                                     │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐       │
│  │  🧠 Symptom     │ │  🏥 Find        │ │  💰 Cost        │       │
│  │    Analysis     │ │    Hospitals    │ │    Estimator   │       │
│  │                 │ │                 │ │                 │       │
│  │ Convert your    │ │ Find hospitals  │ │ Get estimated   │       │
│  │ symptoms to     │ │ near you with   │ │ treatment costs │       │
│  │ medical terms   │ │ the right dept  │ │ before visiting │       │
│  └─────────────────┘ └─────────────────┘ └─────────────────┘       │
│                                                                     │
│              ⚠️ MediGuide AI is not a diagnosis tool.               │
│              Always consult a qualified medical professional.       │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│  © 2026 MediGuide AI · About · Privacy · Contact                   │
└─────────────────────────────────────────────────────────────────────┘
```

### User journey
1. User opens the website for the first time
2. Sees a clean hero section explaining what the app does
3. Can type symptoms directly into the search bar (redirects to login if not authenticated)
4. Can click feature cards to learn more
5. Clicks **Register** or **Login** to get started

### What happens behind the scenes
**Nothing.** This is a static page. No API calls. No database queries. Pure React rendering.

### Files involved
| File | Role |
|---|---|
| `HomePage.jsx` | Renders the landing page layout, hero section, and feature cards |
| `Navbar.jsx` | Shows Login/Register buttons (or Dashboard if logged in) |

### Why this page exists
First impressions matter. The user needs to immediately understand: *what does this app do?* The hero text + 3 feature cards answer that in 5 seconds. The disclaimer at the bottom is critical — this is healthcare, not a toy.

---

## Screen 2: Registration Page

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 MediGuide AI                                       [Login]     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│                    ┌──────────────────────────┐                     │
│                    │  Create Your Account     │                     │
│                    │                          │                     │
│                    │  Full Name               │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ Shruti Sharma        ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  Email                   │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ shruti@example.com   ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  Phone (optional)        │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ 9876543210           ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  Password                │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ ••••••••             ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  Confirm Password        │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ ••••••••             ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  [  Create Account  ]    │                     │
│                    │                          │                     │
│                    │  Already have an account?│                     │
│                    │  Login here              │                     │
│                    └──────────────────────────┘                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### User inputs
| Field | Required | Validation |
|---|---|---|
| Full Name | ✅ | Min 2 characters |
| Email | ✅ | Valid email format, must be unique |
| Phone | ❌ | 10-digit number |
| Password | ✅ | Min 8 characters |
| Confirm Password | ✅ | Must match password |

### What happens when "Create Account" is clicked

```
1. Frontend validates all fields (instant, before any API call)
   - Are required fields filled?
   - Is email format valid?
   - Do passwords match?
   - If any fail → show red error text below the field

2. If all valid → loading spinner appears on button

3. Frontend calls: POST /api/auth/register
   Body: { "fullName": "Shruti Sharma", "email": "shruti@example.com",
           "phone": "9876543210", "password": "securepass123" }

4. Backend receives request:
   a. Check if email already exists → if yes, return 409 "Email already registered"
   b. Hash the password with BCrypt (never store plain text)
   c. Create User record with role = "USER"
   d. Save to database
   e. Generate JWT token
   f. Return: { "token": "eyJ...", "email": "shruti@example.com",
                "fullName": "Shruti Sharma", "role": "USER" }

5. Frontend receives response:
   a. Store token in localStorage
   b. Set user in AuthContext
   c. Redirect to /dashboard
   d. Navbar updates: [Login][Register] → "Hi, Shruti 👋" [Logout]
```

### Error scenarios the user might see
| Situation | What appears |
|---|---|
| Email already registered | 🔴 "An account with this email already exists. Login instead?" |
| Password too short | 🔴 "Password must be at least 8 characters" |
| Passwords don't match | 🔴 "Passwords do not match" |
| Server error | 🔴 "Something went wrong. Please try again." |

### Database table: `users`
```
INSERT INTO users (full_name, email, phone, password, role, is_active, created_at)
VALUES ('Shruti Sharma', 'shruti@example.com', '9876543210',
        '$2a$10$xK8f...hashedpassword', 'USER', true, NOW());
```

### API
```
POST /api/auth/register

Request:  { fullName, email, phone, password }
Success:  200 { token, email, fullName, role }
Error:    409 { message: "Email already registered" }
Error:    400 { message: "Validation failed", errors: [...] }
```

---

## Screen 3: Login Page

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 MediGuide AI                                    [Register]     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│                    ┌──────────────────────────┐                     │
│                    │  Welcome Back            │                     │
│                    │                          │                     │
│                    │  Email                   │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ shruti@example.com   ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  Password                │                     │
│                    │  ┌──────────────────────┐│                     │
│                    │  │ ••••••••             ││                     │
│                    │  └──────────────────────┘│                     │
│                    │                          │                     │
│                    │  [      Login       ]    │                     │
│                    │                          │                     │
│                    │  Don't have an account?  │                     │
│                    │  Register here           │                     │
│                    └──────────────────────────┘                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### What happens when "Login" is clicked

```
1. Frontend calls: POST /api/auth/login
   Body: { "email": "shruti@example.com", "password": "securepass123" }

2. Backend:
   a. Find user by email → if not found, return 401 "Invalid credentials"
   b. Compare submitted password with stored BCrypt hash
   c. If mismatch → return 401 "Invalid credentials"
      (NEVER say "wrong password" — that tells attackers the email exists)
   d. Generate JWT token containing: { sub: "shruti@example.com", role: "USER", exp: 24h }
   e. Return: { token, email, fullName, role }

3. Frontend:
   a. Store token in localStorage
   b. Set user in AuthContext
   c. Redirect to /dashboard
```

### Error scenarios
| Situation | What appears |
|---|---|
| Wrong email or password | 🔴 "Invalid email or password" (always the same message) |
| Account deactivated | 🔴 "Your account has been deactivated. Contact support." |

### API
```
POST /api/auth/login

Request:  { email, password }
Success:  200 { token, email, fullName, role }
Error:    401 { message: "Invalid email or password" }
```

---

## Screen 4: User Dashboard

![User Dashboard — what a logged-in user sees](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\user_dashboard_1780682246083.png)

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 MediGuide AI     [Dashboard] [Symptoms] [Hospitals] [Costs]    │
│                                              Hi, Shruti 👋 [Logout]│
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Welcome back, Shruti!                                              │
│                                                                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                │
│  │     5        │ │     3        │ │     2        │                │
│  │  Symptom     │ │  Hospital    │ │   Cost       │                │
│  │  Analyses    │ │  Searches    │ │  Estimates   │                │
│  └──────────────┘ └──────────────┘ └──────────────┘                │
│                                                                     │
│  Quick Actions                                                      │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐          │
│  │ 🧠 Analyze     │ │ 🏥 Find        │ │ 💰 Estimate    │          │
│  │   Symptoms     │ │   Hospitals    │ │    Costs       │          │
│  └────────────────┘ └────────────────┘ └────────────────┘          │
│                                                                     │
│  Recent Activity                                                    │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  📋 Jun 5 — Symptom Analysis                               │   │
│  │     "headaches and blurry vision" → Severity: Moderate      │   │
│  │                                                             │   │
│  │  🏥 Jun 4 — Hospital Search                                │   │
│  │     Searched Neurology hospitals in Mumbai                  │   │
│  │                                                             │   │
│  │  💰 Jun 3 — Cost Estimate                                  │   │
│  │     Neurology Consultation: ₹800 — ₹2,000                  │   │
│  │                                                             │   │
│  │  [View Full History →]                                      │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### What happens when this page loads

```
1. React checks AuthContext → is user logged in?
   - No → redirect to /login
   - Yes → continue

2. Frontend calls: GET /api/auth/me
   Headers: { Authorization: "Bearer eyJ..." }
   → Gets current user profile (in case anything changed)

3. Frontend calls: GET /api/symptoms/history?limit=5
   → Gets last 5 symptom analyses for the stats and recent activity

4. Dashboard renders with:
   - Greeting with user's name
   - Stats counts (calculated from history data)
   - Quick action buttons (just links to other pages)
   - Recent activity list
```

### Database tables involved
- `users` — to get user profile
- `symptom_records` — to count analyses and show recent ones

### APIs
```
GET /api/auth/me              →  { id, email, fullName, role }
GET /api/symptoms/history     →  [ { id, rawInput, severity, createdAt }, ... ]
```

### Why this page exists
The dashboard is the user's "home base" after login. Without it, users land on... what? A blank page? The dashboard gives them:
- A sense of progress (stats)
- Quick access to all features (action buttons)
- Context of what they've done recently (activity feed)

---

## Screen 5: Symptom Analysis ⭐ (Core Feature)

![Symptom Analysis — the core feature of MediGuide AI](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\symptom_analysis_1780682219471.png)

### What the user sees — BEFORE analysis

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 MediGuide AI     [Dashboard] [Symptoms] [Hospitals] [Costs]    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  🧠 Symptom Analysis                                                │
│                                                                     │
│  Describe your symptoms in your own words.                          │
│  Our AI will identify medical terms and suggest departments.        │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  I've been having really bad headaches for the past         │   │
│  │  week, especially in the morning. My eyes get blurry        │   │
│  │  sometimes and I feel dizzy when I stand up.                │   │
│  │                                                             │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [  🔍 Analyze Symptoms  ]                                         │
│                                                                     │
│  ⚠️ This is not a medical diagnosis. Always consult a doctor.       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### What the user sees — AFTER analysis

```
┌─────────────────────────────────────────────────────────────────────┐
│  🧠 Symptom Analysis Results                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Overall Severity: [🟠 MODERATE]                                    │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Symptom 1                                                  │   │
│  │  ─────────                                                  │   │
│  │  Your words:     "really bad headaches"                     │   │
│  │  Medical term:   Cephalalgia (Chronic Headache)             │   │
│  │  Department:     🏥 Neurology                               │   │
│  │  Severity:       [🟠 Moderate]                              │   │
│  │  Description:    Recurring head pain, potentially caused    │   │
│  │                  by tension, migraine, or underlying        │   │
│  │                  conditions. Morning headaches may indicate │   │
│  │                  sleep or blood pressure issues.            │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Symptom 2                                                  │   │
│  │  ─────────                                                  │   │
│  │  Your words:     "eyes get blurry"                          │   │
│  │  Medical term:   Blurred Vision (Amblyopia)                 │   │
│  │  Department:     🏥 Ophthalmology                           │   │
│  │  Severity:       [🟠 Moderate]                              │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Symptom 3                                                  │   │
│  │  ─────────                                                  │   │
│  │  Your words:     "feel dizzy when I stand up"               │   │
│  │  Medical term:   Orthostatic Hypotension / Vertigo          │   │
│  │  Department:     🏥 General Medicine                        │   │
│  │  Severity:       [🟡 Mild]                                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  📋 Recommended Departments                                        │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  1. Neurology  ·  2. Ophthalmology  ·  3. General Medicine │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [🏥 Find Hospitals with Neurology]   [💰 Estimate Cost]          │
│  [📋 Analyze New Symptoms]                                         │
│                                                                     │
│  ⚠️ This analysis is AI-generated and may not be accurate.          │
│  Please consult a qualified healthcare professional for diagnosis. │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Severity color coding
| Level | Color | Badge | Meaning |
|---|---|---|---|
| Low | 🟢 Green | `LOW` | Minor issue, can wait |
| Mild | 🟡 Yellow | `MILD` | Monitor, see doctor if persistent |
| Moderate | 🟠 Orange | `MODERATE` | Should see a doctor soon |
| High | 🔴 Red | `HIGH` | See a doctor promptly |
| Emergency | 🔴⚡ Red pulsing | `EMERGENCY` | Seek immediate medical attention |

### The complete flow — step by step

```
Step 1: User types symptoms in the text area
        "I've been having really bad headaches for the past week,
         especially in the morning. My eyes get blurry sometimes
         and I feel dizzy when I stand up."

Step 2: User clicks "Analyze Symptoms"

Step 3: Button shows loading spinner, text area becomes read-only

Step 4: Frontend calls:
        POST /api/symptoms/analyze
        Headers: { Authorization: "Bearer eyJ..." }
        Body: { "text": "I've been having really bad headaches..." }

Step 5: Backend (SymptomController) receives the request
        → Extracts authenticated user from JWT
        → Passes text to SymptomService

Step 6: SymptomService calls GeminiService.analyzeSymptoms(text)

Step 7: GeminiService builds this prompt and sends to Gemini API:
        ┌──────────────────────────────────────────────────────────┐
        │  You are a medical terminology assistant.                │
        │  You are NOT providing diagnosis.                        │
        │  You are translating everyday language into medical      │
        │  terminology for healthcare navigation.                  │
        │                                                          │
        │  Analyze this patient description:                       │
        │  "I've been having really bad headaches for the past     │
        │   week, especially in the morning. My eyes get blurry    │
        │   sometimes and I feel dizzy when I stand up."           │
        │                                                          │
        │  Return a JSON object with:                              │
        │  {                                                       │
        │    "symptoms": [                                         │
        │      {                                                   │
        │        "originalText": "the exact words used",           │
        │        "medicalTerm": "proper medical terminology",      │
        │        "department": "recommended hospital department",  │
        │        "severity": "low|mild|moderate|high|emergency",   │
        │        "description": "brief layman explanation"         │
        │      }                                                   │
        │    ],                                                    │
        │    "overallSeverity": "low|mild|moderate|high|emergency",│
        │    "recommendedDepartments": ["Dept1", "Dept2"],         │
        │    "disclaimer": "This is not a medical diagnosis..."    │
        │  }                                                       │
        │                                                          │
        │  Return ONLY valid JSON. No markdown, no explanation.    │
        └──────────────────────────────────────────────────────────┘

Step 8: Gemini API responds with JSON

Step 9: GeminiService parses the JSON response

Step 10: SymptomService saves to database:
         INSERT INTO symptom_records (user_id, raw_input, ai_response, severity)
         VALUES (1, 'I've been having...', '{"symptoms":[...]}', 'moderate')

Step 11: Controller returns SymptomResponse to frontend

Step 12: Frontend hides loading spinner, renders results cards
```

### What if something goes wrong?

| Problem | What user sees |
|---|---|
| Empty text submitted | 🔴 "Please describe your symptoms" (frontend validation, no API call) |
| Gemini API is down | 🔴 "Our analysis service is temporarily unavailable. Please try again in a few minutes." |
| Gemini returns unparseable text | 🔴 "We couldn't process your symptoms. Try describing them differently." |
| User's JWT is expired | Redirected to login page |

### Database table: `symptom_records`
```sql
-- After the analysis, a new row is inserted:
SELECT * FROM symptom_records WHERE user_id = 1 ORDER BY created_at DESC;

 id | user_id |            raw_input               |     ai_response      | severity  |     created_at
----+---------+------------------------------------+----------------------+-----------+--------------------
  5 |       1 | I've been having really bad head... | {"symptoms":[...]}   | moderate  | 2026-06-05 22:30:00
  4 |       1 | My stomach hurts after eating       | {"symptoms":[...]}   | mild      | 2026-06-04 18:15:00
```

### API
```
POST /api/symptoms/analyze

Request:   { "text": "I've been having really bad headaches..." }
Headers:   Authorization: Bearer <token>

Success Response (200):
{
  "symptoms": [
    {
      "originalText": "really bad headaches",
      "medicalTerm": "Cephalalgia (Chronic Headache)",
      "department": "Neurology",
      "severity": "moderate",
      "description": "Recurring head pain, potentially caused by..."
    },
    {
      "originalText": "eyes get blurry",
      "medicalTerm": "Blurred Vision",
      "department": "Ophthalmology",
      "severity": "moderate",
      "description": "Decreased visual acuity..."
    },
    {
      "originalText": "feel dizzy when I stand up",
      "medicalTerm": "Orthostatic Hypotension",
      "department": "General Medicine",
      "severity": "mild",
      "description": "Drop in blood pressure when standing..."
    }
  ],
  "overallSeverity": "moderate",
  "recommendedDepartments": ["Neurology", "Ophthalmology", "General Medicine"],
  "disclaimer": "This is not a medical diagnosis. Please consult a healthcare professional."
}
```

### The action buttons at the bottom

**"Find Hospitals with Neurology"** → navigates to `/hospitals?department=Neurology`
The department is pre-filled from the analysis. This is the key user flow — symptoms lead to hospitals.

**"Estimate Cost"** → navigates to `/costs?department=Neurology`
Department is pre-filled again. Seamless navigation.

---

## Screen 6: Hospital Finder

![Hospital Finder — search results for hospitals](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\hospital_finder_1780682233078.png)

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 Find Hospitals                                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  [Search]   │
│  │ City         │  │ Department   │  │ Type         │             │
│  │ Mumbai    ▼  │  │ Neurology ▼  │  │ All       ▼  │             │
│  └──────────────┘  └──────────────┘  └──────────────┘             │
│                                                                     │
│  Showing 12 hospitals in Mumbai with Neurology                      │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🏥 City General Hospital                         ★ 4.2    │   │
│  │  📍 123 Main Street, Andheri West, Mumbai                  │   │
│  │  🏷️ Government                                             │   │
│  │  📋 Neurology · General Medicine · Cardiology · Orthopedics│   │
│  │  📞 022-12345678                                           │   │
│  │                                                             │   │
│  │  [View Details]  [☐ Compare]                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🏥 Apollo Hospital, Mumbai                       ★ 4.5    │   │
│  │  📍 Navi Mumbai, Maharashtra                               │   │
│  │  🏷️ Private                                                │   │
│  │  📋 Neurology · Oncology · Cardiology · Pediatrics         │   │
│  │  📞 022-87654321                                           │   │
│  │                                                             │   │
│  │  [View Details]  [☐ Compare]                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🏥 Lilavati Hospital                             ★ 4.3    │   │
│  │  📍 Bandra West, Mumbai                                    │   │
│  │  🏷️ Private                                                │   │
│  │  📋 Neurology · ENT · Dermatology · Surgery                │   │
│  │  📞 022-26568000                                           │   │
│  │                                                             │   │
│  │  [View Details]  [☐ Compare]                               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [Compare Selected (2)]                ← appears when 2+ selected  │
│                                                                     │
│  [← Previous]  Page 1 of 3  [Next →]                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### User inputs
| Field | Type | Options |
|---|---|---|
| City | Dropdown | Mumbai, Delhi, Bangalore, Chennai, Hyderabad, Pune, Kolkata... |
| Department | Dropdown (optional) | Neurology, Cardiology, Orthopedics, General Medicine... (pre-filled if coming from symptom analysis) |
| Type | Dropdown (optional) | All, Government, Private, Trust |

### What happens when "Search" is clicked

```
1. Frontend calls:
   GET /api/hospitals?city=Mumbai&department=Neurology&type=all&page=0&size=10

2. Backend (HospitalController):
   → Calls hospitalService.search(city, department, type, page, size)

3. HospitalService:
   → Queries HospitalRepository
   → If department is provided: filters hospitals whose 'departments' column
     contains the search term (SQL LIKE '%Neurology%')
   → Sorts by rating descending
   → Returns paginated result

4. Database query (simplified):
   SELECT * FROM hospitals
   WHERE city = 'Mumbai'
     AND departments ILIKE '%Neurology%'
     AND is_active = true
   ORDER BY rating DESC
   LIMIT 10 OFFSET 0;

5. Frontend receives list, renders hospital cards
```

### Hospital Detail Page (clicking "View Details")

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 City General Hospital                                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ★ 4.2 Rating  ·  🏷️ Government  ·  📍 Mumbai                     │
│                                                                     │
│  📍 Address                                                         │
│  123 Main Street, Andheri West, Mumbai, Maharashtra — 400058        │
│                                                                     │
│  📞 Contact                                                         │
│  Phone: 022-12345678                                                │
│  Email: info@citygeneralhospital.org                                │
│                                                                     │
│  📋 Departments                                                     │
│  ┌────────────┐ ┌──────────────────┐ ┌────────────┐ ┌───────────┐  │
│  │ Neurology  │ │ General Medicine │ │ Cardiology │ │Orthopedics│  │
│  └────────────┘ └──────────────────┘ └────────────┘ └───────────┘  │
│                                                                     │
│  [💰 Estimate Treatment Cost]     [← Back to Results]              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### API
```
GET /api/hospitals?city=Mumbai&department=Neurology&page=0&size=10

Success Response (200):
{
  "content": [
    {
      "id": 1,
      "name": "City General Hospital",
      "address": "123 Main Street, Andheri West, Mumbai",
      "city": "Mumbai",
      "state": "Maharashtra",
      "type": "government",
      "departments": "Neurology,General Medicine,Cardiology,Orthopedics",
      "phone": "022-12345678",
      "rating": 4.2
    },
    ...
  ],
  "page": 0,
  "size": 10,
  "totalElements": 12,
  "totalPages": 2
}

GET /api/hospitals/{id}        →  { full hospital details }
```

---

## Screen 7: Hospital Comparison

![Hospital Comparison — side-by-side comparison](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\hospital_comparison_1780682293675.png)

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 Compare Hospitals                                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│            City General Hospital  │  Apollo Hospital Mumbai         │
│  ─────────────────────────────────┼──────────────────────────       │
│  Rating     ★ 4.2                 │  ★ 4.5                          │
│  Type       🏷️ Government         │  🏷️ Private                     │
│  City       Mumbai                │  Mumbai                         │
│  Phone      022-12345678          │  022-87654321                    │
│                                   │                                  │
│  Departments:                     │  Departments:                    │
│    ✅ Neurology                   │    ✅ Neurology                  │
│    ✅ General Medicine            │    ❌ General Medicine           │
│    ✅ Cardiology                  │    ✅ Cardiology                 │
│    ✅ Orthopedics                 │    ❌ Orthopedics                │
│    ❌ Oncology                    │    ✅ Oncology                   │
│    ❌ Pediatrics                  │    ✅ Pediatrics                 │
│                                   │                                  │
│  Avg Cost   ₹500 — ₹1,000        │  ₹1,500 — ₹3,000               │
│  ─────────────────────────────────┼──────────────────────────       │
│                                                                     │
│  ✅ = Has your recommended department (Neurology)                   │
│                                                                     │
│  [← Back to Results]                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### How comparison works

```
1. On the Hospital Finder page, user checks the "Compare" checkbox on 2 hospitals
2. "Compare Selected (2)" button appears at the bottom
3. User clicks it

4. Frontend already HAS the data for both hospitals (from the search results)
   → No new API call needed!
   → Just renders both hospitals side-by-side

5. If department was pre-filled from symptom analysis:
   → Each hospital's departments are compared against the recommended one
   → ✅ = hospital has this department
   → ❌ = hospital doesn't

This is purely a FRONTEND feature. No new backend API needed.
The comparison page just re-displays data the frontend already fetched.
```

### Why limit to 2 hospitals?
Comparing 2 is easy to read. Comparing 3+ becomes a cluttered table. Keep it simple. If users want to compare different pairs, they go back and select again.

---

## Screen 8: Cost Estimator

![Cost Estimator — treatment cost estimation](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\cost_estimator_1780682271042.png)

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  💰 Treatment Cost Estimator                                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌────────────────────────────┐  ┌────────────────────────────────┐│
│  │                            │  │                                ││
│  │  Department                │  │  (Results appear here          ││
│  │  ┌──────────────────────┐  │  │   after clicking Estimate)    ││
│  │  │ Neurology          ▼ │  │  │                                ││
│  │  └──────────────────────┘  │  │                                ││
│  │                            │  │                                ││
│  │  Treatment Type            │  │                                ││
│  │  ┌──────────────────────┐  │  │                                ││
│  │  │ Consultation       ▼ │  │  │                                ││
│  │  └──────────────────────┘  │  │                                ││
│  │                            │  │                                ││
│  │  City                      │  │                                ││
│  │  ┌──────────────────────┐  │  │                                ││
│  │  │ Mumbai             ▼ │  │  │                                ││
│  │  └──────────────────────┘  │  │                                ││
│  │                            │  │                                ││
│  │  Hospital Type             │  │                                ││
│  │  ┌──────────────────────┐  │  │                                ││
│  │  │ Private            ▼ │  │  │                                ││
│  │  └──────────────────────┘  │  │                                ││
│  │                            │  │                                ││
│  │  [  💰 Estimate Cost  ]   │  │                                ││
│  │                            │  │                                ││
│  └────────────────────────────┘  └────────────────────────────────┘│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### After clicking "Estimate Cost"

```
  ┌────────────────────────────────┐
  │  💰 Estimated Cost             │
  │                                │
  │  Department:  Neurology        │
  │  Treatment:   Consultation     │
  │  City:        Mumbai           │
  │  Type:        Private          │
  │                                │
  │  ┌──────────────────────────┐  │
  │  │   ₹800  —  ₹2,000       │  │
  │  │                          │  │
  │  │   Average: ₹1,200       │  │
  │  └──────────────────────────┘  │
  │                                │
  │  Government hospitals in       │
  │  Mumbai typically charge:      │
  │  ₹100 — ₹500                  │
  │                                │
  │  ⚠️ These are estimates based  │
  │  on average costs. Actual      │
  │  costs may vary by hospital.   │
  │                                │
  │  [🏥 Find Neurology Hospitals] │
  └────────────────────────────────┘
```

### The complete flow

```
1. User selects options from dropdowns
   (Department may be pre-filled if coming from symptom analysis)

2. Clicks "Estimate Cost"

3. Frontend calls:
   POST /api/costs/estimate
   Body: { "department": "Neurology", "treatment": "Consultation",
           "city": "Mumbai", "hospitalType": "private" }

4. Backend (CostController → CostService):
   → Queries TreatmentCostRepository:
     SELECT min_cost, max_cost FROM treatment_costs
     WHERE department = 'Neurology'
       AND treatment = 'Consultation'
       AND city = 'Mumbai'
       AND hospital_type = 'private';
   → If exact match not found, falls back to city-level average
   → If still not found, falls back to national average
   → Calculates average = (min + max) / 2

5. Returns CostResponse to frontend

6. Frontend renders the cost card with range + average
```

### Where does the cost data come from?
Administrators enter it via the Admin Dashboard. It's reference data — average costs for treatments in different cities. Not real-time hospital pricing. The system clearly states these are estimates.

### API
```
POST /api/costs/estimate

Request:  { department, treatment, city, hospitalType }
Response: {
  "department": "Neurology",
  "treatment": "Consultation",
  "city": "Mumbai",
  "hospitalType": "private",
  "minCost": 800,
  "maxCost": 2000,
  "averageCost": 1200,
  "currency": "INR"
}

GET /api/costs/treatments    →  ["Consultation", "Lab Test", "Surgery", "Procedure", "Follow-up"]
GET /api/costs/departments   →  ["Neurology", "Cardiology", "Orthopedics", ...]
```

---

## Screen 9: User History

### What the user sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  📋 Your Health Timeline                                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🧠  June 5, 2026 at 10:30 PM                              │   │
│  │  ──────────────────────────────                              │   │
│  │  Input: "I've been having really bad headaches for the       │   │
│  │          past week and my eyes get blurry"                   │   │
│  │                                                              │   │
│  │  Severity: [🟠 Moderate]                                    │   │
│  │  Departments: Neurology, Ophthalmology                       │   │
│  │                                                              │   │
│  │  [View Full Results]  [🗑️ Delete]                           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🧠  June 4, 2026 at 6:15 PM                               │   │
│  │  ──────────────────────────────                              │   │
│  │  Input: "My stomach hurts after eating and I feel nauseous"  │   │
│  │                                                              │   │
│  │  Severity: [🟡 Mild]                                        │   │
│  │  Departments: Gastroenterology                               │   │
│  │                                                              │   │
│  │  [View Full Results]  [🗑️ Delete]                           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  🧠  June 1, 2026 at 2:45 PM                               │   │
│  │  ──────────────────────────────                              │   │
│  │  Input: "I have a sore throat and mild fever"                │   │
│  │                                                              │   │
│  │  Severity: [🟢 Low]                                         │   │
│  │  Departments: General Medicine, ENT                          │   │
│  │                                                              │   │
│  │  [View Full Results]  [🗑️ Delete]                           │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [← Previous]  Page 1 of 2  [Next →]                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### What happens when this page loads

```
1. Frontend calls:
   GET /api/symptoms/history?page=0&size=10
   Headers: Authorization: Bearer <token>

2. Backend:
   → Extracts user ID from JWT
   → Queries: SELECT * FROM symptom_records
              WHERE user_id = 1
              ORDER BY created_at DESC
              LIMIT 10 OFFSET 0
   → Returns paginated list

3. Frontend renders timeline cards
   → Each card shows: date, input text (truncated), severity badge, departments
   → "View Full Results" opens the complete AI response for that record
   → "Delete" removes the record
```

### APIs
```
GET    /api/symptoms/history?page=0&size=10   →  paginated list of past analyses
GET    /api/symptoms/{id}                     →  full details of one analysis
DELETE /api/symptoms/{id}                     →  delete one record (only own records)
```

### Why save history?
1. Users can track their health patterns over time
2. They can reference past analyses without re-running them
3. It's useful for showing a doctor: "Here's what I've been experiencing"

---

## Screen 10: Admin Dashboard

![Admin Dashboard — system management panel](C:\Users\shrut\.gemini\antigravity\brain\71482d6a-654d-48d8-b848-0ca8a9a84e60\admin_dashboard_1780682282649.png)

### Who can access this?
Only users with `role = "ADMIN"` in the database. Regular users who try to access `/admin` are redirected to the dashboard.

### What the admin sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🔧 Admin Dashboard                                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌────────────┐│
│  │    127       │ │    489       │ │     48       │ │    156     ││
│  │  Total       │ │  Symptom     │ │  Hospitals   │ │   Cost     ││
│  │  Users       │ │  Analyses    │ │              │ │  Entries   ││
│  └──────────────┘ └──────────────┘ └──────────────┘ └────────────┘│
│                                                                     │
│  Management                                                         │
│  ┌────────────────────┐ ┌────────────────────┐ ┌────────────────┐  │
│  │ 🏥 Manage          │ │ 💰 Manage          │ │ 👥 View        │  │
│  │    Hospitals       │ │    Costs           │ │    Users       │  │
│  │                    │ │                    │ │                │  │
│  │ Add, edit, or      │ │ Add or update      │ │ View user      │  │
│  │ deactivate         │ │ treatment cost     │ │ list and       │  │
│  │ hospitals          │ │ reference data     │ │ activity       │  │
│  └────────────────────┘ └────────────────────┘ └────────────────┘  │
│                                                                     │
│  Recent Users                                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Name              Email                    Joined          │   │
│  │  ────              ─────                    ──────          │   │
│  │  Shruti Sharma     shruti@example.com       Jun 5, 2026    │   │
│  │  Raj Patel         raj@example.com          Jun 4, 2026    │   │
│  │  Priya Singh       priya@example.com        Jun 3, 2026    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### What happens when this page loads

```
1. Frontend checks: does the user have role "ADMIN"?
   → No → redirect to /dashboard
   → Yes → proceed

2. Frontend calls: GET /api/admin/stats
   Headers: Authorization: Bearer <admin-token>

3. Backend:
   → @PreAuthorize("hasRole('ADMIN')") — blocks non-admin users
   → Runs:
     SELECT COUNT(*) FROM users;           → 127
     SELECT COUNT(*) FROM symptom_records; → 489
     SELECT COUNT(*) FROM hospitals;       → 48
     SELECT COUNT(*) FROM treatment_costs; → 156
   → Returns stats object

4. Frontend calls: GET /api/admin/users?page=0&size=5
   → Gets recent user list for the table
```

### API
```
GET /api/admin/stats
Response: { totalUsers: 127, totalAnalyses: 489, totalHospitals: 48, totalCostEntries: 156 }

GET /api/admin/users?page=0&size=10
Response: { content: [ { id, fullName, email, role, isActive, createdAt }, ... ], totalPages, ... }
```

---

## Screen 11: Hospital Management (Admin)

### What the admin sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  🏥 Manage Hospitals                              [+ Add Hospital] │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Search: [________________]  City: [All ▼]  Status: [All ▼]       │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  Name                    City      Type        Status  Actions│  │
│  │  ────                    ────      ────        ──────  ──────│  │
│  │  City General Hospital   Mumbai    Government  Active  [✏️][🗑️]│
│  │  Apollo Hospital         Mumbai    Private     Active  [✏️][🗑️]│
│  │  Lilavati Hospital       Mumbai    Private     Active  [✏️][🗑️]│
│  │  Fortis Hospital         Delhi     Private     Active  [✏️][🗑️]│
│  │  AIIMS Delhi             Delhi     Government  Active  [✏️][🗑️]│
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  [← Previous]  Page 1 of 5  [Next →]                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Add Hospital Form (clicking "+ Add Hospital")

```
┌──────────────────────────────────────┐
│  Add New Hospital                    │
│                                      │
│  Hospital Name *                     │
│  ┌──────────────────────────────────┐│
│  │ Fortis Hospital                  ││
│  └──────────────────────────────────┘│
│                                      │
│  Address *                           │
│  ┌──────────────────────────────────┐│
│  │ Mulund Goregaon Link Road       ││
│  └──────────────────────────────────┘│
│                                      │
│  City *              State *         │
│  ┌──────────────┐   ┌──────────────┐│
│  │ Mumbai       │   │ Maharashtra  ││
│  └──────────────┘   └──────────────┘│
│                                      │
│  Pincode            Phone            │
│  ┌──────────────┐   ┌──────────────┐│
│  │ 400078       │   │ 022-25934444 ││
│  └──────────────┘   └──────────────┘│
│                                      │
│  Email                               │
│  ┌──────────────────────────────────┐│
│  │ info@fortis.com                  ││
│  └──────────────────────────────────┘│
│                                      │
│  Type                                │
│  ┌──────────────────────────────────┐│
│  │ Private                       ▼  ││
│  └──────────────────────────────────┘│
│                                      │
│  Departments (comma-separated)       │
│  ┌──────────────────────────────────┐│
│  │ Cardiology,Neurology,Orthopedics││
│  └──────────────────────────────────┘│
│                                      │
│  Latitude           Longitude        │
│  ┌──────────────┐   ┌──────────────┐│
│  │ 19.1724      │   │ 72.9570      ││
│  └──────────────┘   └──────────────┘│
│                                      │
│  [Cancel]            [Save Hospital] │
└──────────────────────────────────────┘
```

### What happens when "Save Hospital" is clicked

```
1. Frontend validates required fields (name, address, city, state, type)

2. Frontend calls:
   POST /api/admin/hospitals
   Body: { name, address, city, state, pincode, phone, email,
           type, departments, latitude, longitude }
   Headers: Authorization: Bearer <admin-token>

3. Backend:
   → @PreAuthorize("hasRole('ADMIN')") — ensures only admins
   → Validates input
   → Saves to hospitals table
   → Returns created hospital with ID

4. Frontend:
   → Shows success toast "Hospital added successfully"
   → Adds new hospital to the list
```

### APIs
```
GET    /api/admin/hospitals?page=0&size=10      →  paginated list
POST   /api/admin/hospitals                      →  create new hospital
PUT    /api/admin/hospitals/{id}                 →  update hospital
DELETE /api/admin/hospitals/{id}                 →  soft-delete (set is_active = false)
```

---

## Screen 12: Cost Management (Admin)

### What the admin sees

```
┌─────────────────────────────────────────────────────────────────────┐
│  💰 Manage Treatment Costs                      [+ Add Cost Entry] │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Filter:  Department [All ▼]   City [All ▼]                       │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │ Treatment       Department   City    Type    Min     Max     │  │
│  │ ─────────       ──────────   ────    ────    ───     ───     │  │
│  │ Consultation    Neurology    Mumbai  Pvt     ₹800    ₹2,000  │  │
│  │ Consultation    Neurology    Mumbai  Govt    ₹100    ₹500    │  │
│  │ Consultation    Neurology    Delhi   Pvt     ₹700    ₹1,800  │  │
│  │ MRI Scan        Neurology    Mumbai  Pvt     ₹3,000  ₹8,000 │  │
│  │ Consultation    Cardiology   Mumbai  Pvt     ₹1,000  ₹3,000 │  │
│  │ ECG Test        Cardiology   Mumbai  Pvt     ₹500    ₹1,500 │  │
│  │ X-Ray           Orthopedics  Mumbai  Pvt     ₹300    ₹1,200 │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Add Cost Entry Form

```
┌──────────────────────────────────────┐
│  Add Treatment Cost                  │
│                                      │
│  Treatment *                         │
│  ┌──────────────────────────────────┐│
│  │ Consultation                  ▼  ││   (Consultation, Lab Test, Surgery,
│  └──────────────────────────────────┘│    MRI Scan, X-Ray, ECG, etc.)
│                                      │
│  Department *                        │
│  ┌──────────────────────────────────┐│
│  │ Neurology                     ▼  ││
│  └──────────────────────────────────┘│
│                                      │
│  City *                              │
│  ┌──────────────────────────────────┐│
│  │ Mumbai                        ▼  ││
│  └──────────────────────────────────┘│
│                                      │
│  Hospital Type *                     │
│  ┌──────────────────────────────────┐│
│  │ Private                       ▼  ││
│  └──────────────────────────────────┘│
│                                      │
│  Minimum Cost (₹) *    Max Cost (₹) │
│  ┌──────────────┐   ┌──────────────┐│
│  │ 800          │   │ 2000         ││
│  └──────────────┘   └──────────────┘│
│                                      │
│  [Cancel]           [Save]           │
└──────────────────────────────────────┘
```

### APIs
```
GET    /api/admin/costs?page=0&size=10&department=Neurology   →  paginated list
POST   /api/admin/costs                                        →  add new cost entry
PUT    /api/admin/costs/{id}                                   →  update cost entry
DELETE /api/admin/costs/{id}                                   →  delete cost entry
```

---

## Summary — Complete API List

Every API your application needs, in one table:

| Method | Endpoint | Auth | Role | Purpose |
|---|---|---|---|---|
| `POST` | `/api/auth/register` | ❌ | Any | Create account |
| `POST` | `/api/auth/login` | ❌ | Any | Login, get JWT |
| `GET` | `/api/auth/me` | ✅ | Any | Get current user profile |
| `POST` | `/api/symptoms/analyze` | ✅ | USER | Analyze symptoms via AI |
| `GET` | `/api/symptoms/history` | ✅ | USER | Get past analyses |
| `GET` | `/api/symptoms/{id}` | ✅ | USER | Get one analysis detail |
| `DELETE` | `/api/symptoms/{id}` | ✅ | USER | Delete one analysis |
| `GET` | `/api/hospitals` | ✅ | Any | Search hospitals |
| `GET` | `/api/hospitals/{id}` | ✅ | Any | Hospital details |
| `POST` | `/api/costs/estimate` | ✅ | Any | Get cost estimate |
| `GET` | `/api/costs/treatments` | ✅ | Any | List treatment types |
| `GET` | `/api/costs/departments` | ✅ | Any | List departments |
| `GET` | `/api/admin/stats` | ✅ | ADMIN | Dashboard statistics |
| `GET` | `/api/admin/users` | ✅ | ADMIN | List all users |
| `POST` | `/api/admin/hospitals` | ✅ | ADMIN | Add hospital |
| `PUT` | `/api/admin/hospitals/{id}` | ✅ | ADMIN | Update hospital |
| `DELETE` | `/api/admin/hospitals/{id}` | ✅ | ADMIN | Deactivate hospital |
| `GET` | `/api/admin/costs` | ✅ | ADMIN | List cost entries |
| `POST` | `/api/admin/costs` | ✅ | ADMIN | Add cost entry |
| `PUT` | `/api/admin/costs/{id}` | ✅ | ADMIN | Update cost entry |
| `DELETE` | `/api/admin/costs/{id}` | ✅ | ADMIN | Delete cost entry |

**Total: 21 APIs.** That's your entire backend. No more, no less.

---

## Summary — Complete Database Tables

| Table | Rows (est.) | Managed by | Used by |
|---|---|---|---|
| `users` | Grows with signups | Registration | Auth, Admin, all features |
| `symptom_records` | Grows with each analysis | Symptom Analysis | History, Dashboard |
| `hospitals` | 50-200 (admin adds) | Admin | Hospital Finder, Comparison |
| `treatment_costs` | 100-500 (admin adds) | Admin | Cost Estimator |

**Total: 4 tables.** That's your entire database.

---

## The Connected User Journey — Everything Together

```
A real user session might look like this:

1. Shruti opens MediGuide AI for the first time
   → Sees Landing Page → clicks Register → creates account
   → Redirected to Dashboard

2. She types her symptoms:
   "I've been having headaches and my vision is blurry"
   → Gets results: Neurology, Ophthalmology, Moderate severity

3. She clicks "Find Hospitals with Neurology"
   → Redirected to Hospital Finder with department = Neurology
   → Enters city: Mumbai
   → Sees 12 hospitals

4. She checks "Compare" on City General Hospital and Apollo Hospital
   → Clicks "Compare Selected"
   → Sees side-by-side comparison
   → Both have Neurology ✅
   → City General is cheaper (Government)

5. She clicks "Estimate Cost" for Neurology Consultation
   → Redirected to Cost Estimator with department = Neurology
   → Selects: Consultation, Mumbai, Government
   → Sees: ₹100 — ₹500 (average ₹250)

6. Next week, she logs back in
   → Dashboard shows her recent analysis
   → She clicks "View Full Results" to review
   → She runs a new analysis for different symptoms

That's the entire product. Simple, useful, learnable.
```
