# MediGuide AI — Student Developer Implementation Plan

> This plan is written like a mentor sitting next to you, explaining every decision.
> If something feels confusing, it's the plan's fault — not yours.

---

## What Are We Building?

MediGuide AI is a web app where a user types symptoms like *"I have a headache and feel dizzy"*, and the system:

1. Converts that into medical terminology (*Cephalalgia, Vertigo*)
2. Suggests which hospital department to visit (*Neurology*)
3. Estimates how urgent it is (*Moderate*)
4. Helps find nearby hospitals
5. Shows estimated treatment costs

It is **NOT** a diagnosis tool. It's a navigation assistant — like Google Maps for healthcare.

---

## The Simplest Architecture That Works

```
┌────────────────┐        HTTP        ┌────────────────┐       SQL        ┌────────────┐
│                │   (JSON over REST)  │                │   (JDBC/JPA)     │            │
│  React App     │◄──────────────────►│  Spring Boot   │◄───────────────►│ PostgreSQL │
│  (Browser)     │                    │  (Single JAR)  │                  │            │
│                │                    │                │                  │            │
└────────────────┘                    │   Calls ───────┼──► Gemini API   └────────────┘
                                      │   (HTTP)       │    (Google AI)
                                      └────────────────┘
```

**That's it.** Three things:
- A React app that runs in the browser
- A Spring Boot server that handles everything
- A PostgreSQL database that stores everything

The AI part? Spring Boot calls the Gemini API directly over HTTP. No Python. No FastAPI. No microservices. Just an HTTP call, like calling any other API.

### Why NOT a separate Python AI service?

| Separate Python service | Direct API call from Spring Boot |
|---|---|
| You maintain 2 servers | You maintain 1 server |
| You learn Python + FastAPI + spaCy | You learn Spring Boot deeply |
| You need Docker to run both | You run `mvn spring-boot:run` |
| You handle inter-service communication | You make one HTTP call |
| You debug across 2 languages | You debug in one language |

**The Gemini API already does** symptom extraction, medical terminology mapping, severity estimation, and report simplification. Writing a custom NLP pipeline with spaCy to do what Gemini already does is reinventing the wheel for a learning project.

If you later discover that the Gemini API doesn't meet your needs, *then* you add a Python service. Not before.

---

## Technology Stack — Every Choice Justified

### Frontend

| Technology | Why You Need It | What Problem It Solves | Simpler Alternative | Why This Is Still Reasonable |
|---|---|---|---|---|
| **React** | Component-based UI | Breaks your UI into reusable pieces (SymptomForm, HospitalCard). Without it, you'd write spaghetti HTML/JS. | Vanilla JS + HTML | React's component model teaches you how modern UIs work. It's the most employable frontend skill. |
| **Vite** | Dev server + bundler | Instant hot-reload while coding. Bundles your app for production. | Create React App | CRA is deprecated. Vite is faster and simpler. It's the standard now. |
| **Tailwind CSS** | Styling | Write styles directly in your JSX (`className="bg-blue-500 p-4"`). No separate CSS files to manage. | Plain CSS | Tailwind is faster for solo developers. You can always learn plain CSS separately. |
| **Axios** | HTTP client | Makes API calls to your backend. Handles JSON parsing, error status codes, request/response interceptors. | `fetch()` API | Axios gives you interceptors (auto-attach JWT token to every request) which would take ~30 lines to do with fetch. Worth it. |
| **React Router** | Page navigation | Lets you have `/login`, `/symptoms`, `/hospitals` as separate "pages" without actual page reloads. | None | You can't build a multi-page SPA without a router. |

**Technologies NOT used and why:**

| Skipped | Why |
|---|---|
| React Query | You'll manage loading/error states with `useState`. It's more code, but you'll *understand* what's happening. React Query hides too much magic for a learning project. |
| Zustand/Redux | You'll use React's built-in `useState` + `useContext`. Global state management libraries solve problems you don't have yet (dozens of components sharing complex state). |
| TypeScript | Adds type safety but also adds compilation complexity, generic syntax, and interface ceremonies. Learn JavaScript deeply first. Add TypeScript to your next project. |

### Backend

| Technology | Why You Need It | What Problem It Solves | Simpler Alternative | Why This Is Still Reasonable |
|---|---|---|---|---|
| **Spring Boot** | Web server + framework | Handles HTTP requests, connects to database, manages security. One `@RestController` annotation = a working API endpoint. | Express.js (Node) | Spring Boot teaches enterprise Java patterns that are highly employable. It's more verbose but more structured. |
| **Spring Security** | Authentication/authorization | Protects your APIs. Ensures only logged-in users can access `/api/symptoms`. Ensures only admins can access `/api/admin/*`. | Manual JWT checking in every controller | Spring Security centralizes auth logic. Without it, you'd copy-paste token validation into every endpoint. |
| **Spring Data JPA** | Database access | Write `findByEmail(String email)` and Spring auto-generates the SQL query. No manual JDBC code. | Raw JDBC | JPA eliminates boilerplate. You write entity classes, Spring writes the SQL. For a learning project, this lets you focus on business logic. |
| **JWT (JSON Web Tokens)** | Stateless authentication | User logs in → gets a token → sends token with every request. Server validates token without storing sessions. | Session cookies | JWT teaches you how modern API auth works. Sessions are simpler but JWT is what the industry uses for REST APIs. |
| **PostgreSQL** | Database | Stores users, hospitals, symptoms, costs. Relational database with strong data integrity. | H2 (in-memory) or SQLite | PostgreSQL is what you'll use in production. H2 is for testing. Learn the real thing. |

**Technologies NOT used and why:**

| Skipped | Why |
|---|---|
| Flyway/Liquibase | Database migration tools. Useful for teams, overkill for solo dev. You'll manage your schema with a single `schema.sql` file that Spring Boot auto-runs on startup. |
| MapStruct | Auto-generates Entity↔DTO mapping code. You'll write mapping methods by hand — it's ~5 lines per class and you'll understand exactly what's happening. |
| Docker | Container orchestration. You'll run PostgreSQL locally (installer or pgAdmin) and Spring Boot via `mvn spring-boot:run`. Docker adds a learning curve that's unrelated to your app. |
| Caffeine/Redis | Caching. Your app has one user (you) during development. Caching solves performance problems you don't have. |

### AI

| Technology | Why | Alternative | Why This Choice |
|---|---|---|---|
| **Gemini API** (Google AI) | Symptom analysis, medical terminology, severity estimation | OpenAI API | Both work. Gemini has a generous free tier. Use whichever you prefer. The code is nearly identical — one HTTP POST call. |

You'll call Gemini like this (pseudocode):
```
POST https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent

Body: {
  "prompt": "Extract medical symptoms from this text: 'I have a bad headache and feel dizzy'.
             Return JSON with: original_term, medical_term, department, severity"
}

Response: {
  "symptoms": [
    {"original": "bad headache", "medical_term": "Cephalalgia", "department": "Neurology", "severity": "moderate"},
    {"original": "feel dizzy", "medical_term": "Vertigo", "department": "ENT/Neurology", "severity": "mild"}
  ]
}
```

That's it. No NLP pipeline. No model training. No Python. One HTTP call.

---

## Project Folder Structure

```
d:\Mediguide AI\
│
├── backend\                        # Spring Boot application
│   ├── src\
│   │   ├── main\
│   │   │   ├── java\com\mediguide\
│   │   │   │   ├── MediGuideApplication.java
│   │   │   │   ├── config\
│   │   │   │   ├── security\
│   │   │   │   ├── auth\
│   │   │   │   ├── symptom\
│   │   │   │   ├── hospital\
│   │   │   │   ├── cost\
│   │   │   │   └── admin\
│   │   │   └── resources\
│   │   │       ├── application.properties
│   │   │       └── schema.sql
│   │   └── test\
│   └── pom.xml
│
├── frontend\                       # React + Vite application
│   ├── src\
│   │   ├── components\
│   │   ├── pages\
│   │   ├── services\
│   │   ├── context\
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
│
├── .gitignore
└── README.md
```

**Why this structure?**
- `backend/` and `frontend/` are two separate projects in one repository (a "monorepo"). You open each in your IDE separately.
- No `docker/`, no `database/`, no `ai-service/` folders. The database schema lives inside the backend. The AI calls live inside the backend. Everything is in one place.

---

## Frontend — Every Folder Explained

```
frontend\src\
│
├── components\                # Reusable UI pieces
│   ├── Navbar.jsx             # Navigation bar shown on every page
│   ├── Footer.jsx             # Footer shown on every page
│   ├── LoadingSpinner.jsx     # Loading indicator (reused everywhere)
│   ├── ErrorMessage.jsx       # Error display (reused everywhere)
│   └── ProtectedRoute.jsx     # Wrapper that redirects to /login if not authenticated
│
├── pages\                     # One file per "screen" in your app
│   ├── HomePage.jsx           # Landing page
│   ├── LoginPage.jsx          # Login form
│   ├── RegisterPage.jsx       # Registration form
│   ├── SymptomPage.jsx        # Symptom input + results
│   ├── HospitalPage.jsx       # Hospital finder + list
│   ├── HospitalDetailPage.jsx # Single hospital details
│   ├── CostPage.jsx           # Cost estimation
│   ├── ProfilePage.jsx        # User profile
│   └── AdminDashboard.jsx     # Admin panel
│
├── services\                  # API call functions (Axios)
│   ├── api.js                 # Axios instance with base URL + JWT interceptor
│   ├── authService.js         # login(), register(), getProfile()
│   ├── symptomService.js      # analyzeSymptoms(), getHistory()
│   ├── hospitalService.js     # searchHospitals(), getHospital()
│   └── costService.js         # estimateCost()
│
├── context\                   # React Context for auth state
│   └── AuthContext.jsx        # Provides { user, token, login, logout } to all components
│
├── App.jsx                    # Root component — sets up routes
├── main.jsx                   # Entry point — renders <App /> into the DOM
└── index.css                  # Tailwind CSS imports
```

### Why each folder exists

**`components/`** — These are pieces you reuse. The Navbar appears on every page. The LoadingSpinner appears whenever data is loading. If you find yourself copy-pasting JSX, it should be a component here.

**`pages/`** — Each file = one URL in your app. `LoginPage.jsx` is what the user sees at `/login`. `SymptomPage.jsx` is what they see at `/symptoms`. One page per route. Simple.

**`services/`** — This is where your API calls live. **Pages don't call Axios directly.** Instead, `SymptomPage.jsx` calls `symptomService.analyzeSymptoms(text)`, which internally calls `axios.post("/api/symptoms/analyze", { text })`. This separation means if your API URL changes, you update one file — not every page.

**`context/`** — React Context solves one problem: "How does the Navbar know if the user is logged in?" Without Context, you'd pass `user` as a prop through 5 layers of components. With Context, any component can call `useAuth()` and get the current user.

### Why NOT more folders?

You don't need `hooks/`, `utils/`, `store/`, `layouts/`, `features/` yet. Those emerge when your app grows. Start with the simplest structure. If `pages/` gets too big, then reorganize.

---

## Backend — Every Folder and File Explained

```
backend\src\main\java\com\mediguide\
│
├── MediGuideApplication.java          # Spring Boot entry point
│
├── config\                            # Application-wide settings
│   ├── CorsConfig.java               # Allows React (port 5173) to call backend (port 8080)
│   └── WebClientConfig.java          # Configures the HTTP client for Gemini API calls
│
├── security\                          # Authentication infrastructure
│   ├── SecurityConfig.java           # Which URLs need login, which are public
│   ├── JwtUtil.java                  # Generate JWT, validate JWT, extract username from JWT
│   ├── JwtFilter.java               # Intercepts every request, checks for valid JWT
│   └── CustomUserDetailsService.java # Loads user from database for Spring Security
│
├── auth\                              # Registration + Login feature
│   ├── AuthController.java           # POST /api/auth/register, POST /api/auth/login
│   ├── AuthService.java              # Business logic: validate, hash password, generate token
│   ├── User.java                     # JPA Entity: id, email, password, name, role
│   ├── UserRepository.java           # Spring Data: findByEmail()
│   ├── RegisterRequest.java          # DTO: { email, password, fullName }
│   ├── LoginRequest.java             # DTO: { email, password }
│   └── AuthResponse.java             # DTO: { token, email, role }
│
├── symptom\                           # Symptom analysis feature
│   ├── SymptomController.java        # POST /api/symptoms/analyze, GET /api/symptoms/history
│   ├── SymptomService.java           # Calls Gemini API, saves result to database
│   ├── GeminiService.java            # Makes HTTP call to Gemini API, parses response
│   ├── SymptomRecord.java            # JPA Entity: id, userId, rawInput, result, createdAt
│   ├── SymptomRecordRepository.java  # Spring Data: findByUserId()
│   ├── SymptomRequest.java           # DTO: { text: "I have a headache" }
│   └── SymptomResponse.java          # DTO: { symptoms: [...], severity, departments }
│
├── hospital\                          # Hospital finder feature
│   ├── HospitalController.java       # GET /api/hospitals, GET /api/hospitals/{id}
│   ├── HospitalService.java          # Search, filter, find nearby
│   ├── Hospital.java                 # JPA Entity: id, name, city, latitude, longitude, departments
│   ├── HospitalRepository.java       # Spring Data: findByCity(), custom query for nearby
│   └── HospitalResponse.java         # DTO: hospital data for frontend
│
├── cost\                              # Cost estimation feature
│   ├── CostController.java           # POST /api/costs/estimate
│   ├── CostService.java              # Looks up pricing, calculates estimate
│   ├── TreatmentCost.java            # JPA Entity: id, treatment, department, minCost, maxCost
│   ├── TreatmentCostRepository.java  # Spring Data: findByTreatmentAndCity()
│   ├── CostRequest.java              # DTO: { treatment, city, hospitalType }
│   └── CostResponse.java             # DTO: { estimatedMin, estimatedMax, breakdown }
│
└── admin\                             # Admin-only features
    ├── AdminController.java           # GET /api/admin/users, PUT /api/admin/hospitals
    └── AdminService.java              # Admin business logic
```

### Why each file exists — The Auth module as an example

Let's walk through the **auth module** file by file, because the pattern repeats for every module:

---

#### `AuthController.java` — The front door

```
Why it exists:  Receives HTTP requests and returns HTTP responses.
                It's the ONLY class that knows about HTTP.

What it does:   Maps URLs to methods.
                POST /api/auth/register → calls authService.register()
                POST /api/auth/login → calls authService.login()

Who calls it:   The React frontend, via Axios.

What it returns: JSON response — either { token, email, role } or an error message.

Rule:           Controllers do NOT contain business logic.
                They receive, delegate, and respond. That's it.
```

#### `AuthService.java` — The brain

```
Why it exists:  Contains ALL the business logic for authentication.
                Validates input, hashes passwords, generates tokens.

What it does:   register(): Check if email already exists → hash password → save user → return token
                login(): Find user by email → verify password → generate JWT → return token

Who calls it:   AuthController.java

What it returns: AuthResponse (token + user info) or throws an exception.

Rule:           Services do NOT know about HTTP (no @RequestMapping).
                Services do NOT write SQL (they call repositories).
                Services contain ONLY business rules.
```

#### `User.java` — The data shape

```
Why it exists:  Tells JPA what the "users" table looks like.
                Each field = one column in the database.

What it does:   Maps Java object ↔ database row.
                @Entity annotation tells JPA "this is a table".
                @Id tells JPA "this is the primary key".

Who calls it:   JPA uses it internally. Your code creates User objects and passes them to UserRepository.

What it returns: Nothing — it's a data class (fields + getters/setters).
```

#### `UserRepository.java` — The database door

```
Why it exists:  Provides database operations without writing SQL.
                You declare a method name, Spring writes the query.

What it does:   extends JpaRepository<User, Long> gives you:
                  save(), findById(), findAll(), deleteById() — for free
                You add: findByEmail(String email) — Spring generates
                  SELECT * FROM users WHERE email = ?

Who calls it:   AuthService.java

What it returns: Optional<User>, List<User>, etc.

Rule:           Repositories contain ZERO business logic.
                They ONLY talk to the database.
```

#### `RegisterRequest.java` / `LoginRequest.java` — What the frontend sends

```
Why they exist: Define the EXACT shape of JSON the frontend must send.
                RegisterRequest: { "email": "...", "password": "...", "fullName": "..." }
                LoginRequest: { "email": "...", "password": "..." }

Why not use User.java directly?
                Because User.java has fields the frontend should NEVER see or set:
                id (auto-generated), role (server-controlled), password hash (never sent back).
                DTOs (Data Transfer Objects) are the public contract between frontend and backend.

Who calls it:   Spring automatically converts incoming JSON into these objects.

What it returns: Nothing — they're just data holders.
```

#### `AuthResponse.java` — What the backend sends back

```
Why it exists:  Defines the EXACT shape of JSON the backend returns after login/register.
                { "token": "eyJ...", "email": "user@example.com", "role": "USER" }

Why not return User.java?
                Because User.java contains the password hash.
                You NEVER send passwords back to the frontend.

Who calls it:   AuthService creates it, AuthController returns it.
```

### This pattern repeats for EVERY module

```
Module = Controller + Service + Entity + Repository + Request DTO + Response DTO

Controller  →  receives HTTP request
Service     →  does the thinking
Repository  →  talks to the database
Entity      →  defines the table shape
Request DTO →  defines what the frontend sends
Response DTO → defines what the backend returns
```

Once you understand this for `auth/`, you understand it for `symptom/`, `hospital/`, `cost/`, and `admin/`.

---

### Backend Config Files

#### `config/CorsConfig.java`

```
Why it exists:  Your React app runs on localhost:5173.
                Your Spring Boot runs on localhost:8080.
                Browsers BLOCK requests between different ports (CORS policy).
                This config tells Spring Boot: "Allow requests from localhost:5173".

Without this:   Every API call from React will fail with a CORS error.
```

#### `config/WebClientConfig.java`

```
Why it exists:  Creates a reusable HTTP client bean for calling external APIs (Gemini).
                Instead of creating a new HTTP connection every time, Spring reuses this one.

Alternative:    You COULD use RestTemplate instead. Both work.
                WebClient is the newer, recommended approach in Spring.
                But RestTemplate is simpler if you prefer it — it's a matter of comfort.
```

### Backend Security Files

#### `security/SecurityConfig.java`

```
Why it exists:  Tells Spring Security the RULES:
                - /api/auth/** → public (anyone can register/login)
                - /api/admin/** → only ADMINISTRATOR role
                - everything else → must be logged in (valid JWT)

This is WHERE you define your security policy.
```

#### `security/JwtUtil.java`

```
Why it exists:  Three functions:
                1. generateToken(username) → creates a JWT string
                2. validateToken(token) → checks if it's expired/tampered
                3. extractUsername(token) → gets the username from the token

Uses io.jsonwebtoken (jjwt) library. You configure:
                - Secret key (a long random string only your server knows)
                - Expiration time (e.g., 24 hours)
```

#### `security/JwtFilter.java`

```
Why it exists:  Runs BEFORE every request reaches your controller.

What it does:   1. Checks if the request has an "Authorization: Bearer <token>" header
                2. If yes → validates the token → loads the user → sets them as "logged in"
                3. If no → does nothing (request continues, but will be blocked if the
                   endpoint requires auth)

This is HOW authentication happens on every request.
```

#### `security/CustomUserDetailsService.java`

```
Why it exists:  Spring Security needs a way to load user data from YOUR database.
                This class implements UserDetailsService and tells Spring:
                "To find a user, call userRepository.findByEmail()"

Spring Security calls this automatically. You never call it directly.
```

---

## Database — Simple Schema

You'll use Spring Boot's `schema.sql` feature. Put a file at `src/main/resources/schema.sql` and Spring runs it on startup.

```sql
-- ============================================
-- USERS TABLE
-- ============================================
-- Stores everyone who registers.
-- The 'role' column is either 'USER' or 'ADMIN'.
-- Password is stored as a BCrypt hash, NEVER plain text.

CREATE TABLE IF NOT EXISTS users (
    id          BIGSERIAL PRIMARY KEY,
    email       VARCHAR(255) NOT NULL UNIQUE,
    password    VARCHAR(255) NOT NULL,
    full_name   VARCHAR(255) NOT NULL,
    phone       VARCHAR(20),
    role        VARCHAR(20)  NOT NULL DEFAULT 'USER',
    is_active   BOOLEAN      NOT NULL DEFAULT TRUE,
    created_at  TIMESTAMP    NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMP    NOT NULL DEFAULT NOW()
);

-- ============================================
-- SYMPTOM RECORDS TABLE
-- ============================================
-- Every time a user submits symptoms, we save the input + AI response.
-- 'raw_input' = what the user typed ("I have a bad headache")
-- 'ai_response' = the full JSON from Gemini API
-- 'severity' = extracted severity level (low/moderate/high/emergency)

CREATE TABLE IF NOT EXISTS symptom_records (
    id           BIGSERIAL PRIMARY KEY,
    user_id      BIGINT       NOT NULL REFERENCES users(id),
    raw_input    TEXT         NOT NULL,
    ai_response  TEXT         NOT NULL,
    severity     VARCHAR(50),
    created_at   TIMESTAMP    NOT NULL DEFAULT NOW()
);

-- ============================================
-- HOSPITALS TABLE
-- ============================================
-- Hospitals are added by administrators.
-- latitude/longitude enable "nearby" searches.
-- departments is a comma-separated string (simple)
--   e.g., "Cardiology,Neurology,Orthopedics"

CREATE TABLE IF NOT EXISTS hospitals (
    id           BIGSERIAL PRIMARY KEY,
    name         VARCHAR(255) NOT NULL,
    address      TEXT         NOT NULL,
    city         VARCHAR(100) NOT NULL,
    state        VARCHAR(100) NOT NULL,
    pincode      VARCHAR(10),
    phone        VARCHAR(20),
    email        VARCHAR(255),
    type         VARCHAR(50)  NOT NULL DEFAULT 'private',
    departments  TEXT,
    latitude     DOUBLE PRECISION,
    longitude    DOUBLE PRECISION,
    rating       DOUBLE PRECISION DEFAULT 0.0,
    is_active    BOOLEAN      NOT NULL DEFAULT TRUE,
    created_at   TIMESTAMP    NOT NULL DEFAULT NOW(),
    updated_at   TIMESTAMP    NOT NULL DEFAULT NOW()
);

-- ============================================
-- TREATMENT COSTS TABLE
-- ============================================
-- Reference data: what does a treatment typically cost?
-- Managed by administrators.

CREATE TABLE IF NOT EXISTS treatment_costs (
    id           BIGSERIAL PRIMARY KEY,
    treatment    VARCHAR(255) NOT NULL,
    department   VARCHAR(100) NOT NULL,
    min_cost     DECIMAL(10,2) NOT NULL,
    max_cost     DECIMAL(10,2) NOT NULL,
    city         VARCHAR(100),
    hospital_type VARCHAR(50),
    created_at   TIMESTAMP    NOT NULL DEFAULT NOW(),
    updated_at   TIMESTAMP    NOT NULL DEFAULT NOW()
);
```

### Why BIGSERIAL instead of UUID?

| UUID | BIGSERIAL (1, 2, 3, 4...) |
|---|---|
| 36-character string: `550e8400-e29b-41d4-a716-446655440000` | Simple number: `42` |
| Needed for distributed systems (multiple databases generating IDs independently) | Works perfectly when you have one database |
| Harder to debug: "find the record with ID 550e8400..." | Easy to debug: "find record 42" |

You have one database. Use numbers. If you ever need UUIDs, it's a 10-minute change.

### Why `schema.sql` instead of Flyway?

| Flyway | schema.sql |
|---|---|
| Version-controlled migrations (V1__, V2__, V3__) | One file, Spring runs it on startup |
| Essential for teams (multiple people changing the schema simultaneously) | Perfect for solo development |
| Tracks which migrations have already run | Uses `CREATE TABLE IF NOT EXISTS` to be idempotent |

When you're the only developer, `schema.sql` is fine. If you ever join a team or deploy to production, learn Flyway then.

### `application.properties` configuration

```properties
# Database connection
spring.datasource.url=jdbc:postgresql://localhost:5432/mediguide
spring.datasource.username=postgres
spring.datasource.password=your_password

# Tell JPA to use PostgreSQL dialect
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect

# Don't auto-generate tables from entities — we use schema.sql
spring.jpa.hibernate.ddl-auto=none

# Run schema.sql on startup
spring.sql.init.mode=always

# Gemini API
gemini.api.key=your_gemini_api_key
gemini.api.url=https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent

# JWT
jwt.secret=your-secret-key-at-least-32-characters-long-change-this
jwt.expiration=86400000

# Server port
server.port=8080
```

---

## Request Flow — How Everything Connects

### Example: User analyzes symptoms

```
Step 1: User types "I have a bad headache and feel dizzy" on SymptomPage.jsx

Step 2: SymptomPage calls symptomService.analyzeSymptoms("I have a bad headache and feel dizzy")

Step 3: symptomService.js does:
        axios.post("/api/symptoms/analyze", { text: "I have a bad headache..." })
        (Axios interceptor auto-attaches: Authorization: Bearer eyJ...)

Step 4: Spring Security's JwtFilter intercepts the request:
        - Reads the Bearer token
        - Validates it (not expired, not tampered)
        - Loads the user from database
        - Sets the user as authenticated
        - Passes request to the controller

Step 5: SymptomController.analyzeSymptoms() receives the request:
        - Extracts the text from SymptomRequest DTO
        - Gets the authenticated user's ID from SecurityContext
        - Calls symptomService.analyze(userId, text)

Step 6: SymptomService.analyze() does the real work:
        a) Calls geminiService.analyzeSymptoms(text)
        b) GeminiService sends HTTP POST to Gemini API with a prompt like:
           "Extract medical symptoms from: 'I have a bad headache and feel dizzy'.
            Return JSON with original_term, medical_term, department, severity."
        c) Gemini API returns JSON response
        d) SymptomService parses the response
        e) Saves a SymptomRecord to the database (user_id, raw_input, ai_response)
        f) Returns SymptomResponse to the controller

Step 7: Controller returns JSON to the frontend:
        {
          "symptoms": [
            { "original": "bad headache", "medicalTerm": "Cephalalgia",
              "department": "Neurology", "severity": "moderate" }
          ],
          "overallSeverity": "moderate",
          "recommendedDepartments": ["Neurology", "General Medicine"]
        }

Step 8: SymptomPage.jsx receives the response, updates state, renders results.
```

---

## Module Breakdown — Detailed

### Module 1: Authentication

**Why it exists**: Users need accounts to save their symptom history, cost estimates, and preferences. Admins need to be distinguished from regular users.

**User flow**:
```
New User:   opens app → clicks Register → fills form → submits → gets token → redirected to home
Login:      opens app → clicks Login → fills email+password → submits → gets token → redirected to home
Every page: Navbar shows "Login" or "Hi, {name}" based on auth state
Protected:  visiting /symptoms without login → redirected to /login
```

**Frontend responsibility**: Login form, register form, store JWT token in localStorage, attach token to every API call, redirect unauthenticated users.

**Backend responsibility**: Hash passwords (BCrypt), validate credentials, generate JWT tokens, validate JWT tokens on every request, block unauthorized access.

**Database responsibility**: Store user records (email, hashed password, role).

**APIs**:
```
POST /api/auth/register    →  { email, password, fullName }    →  { token, email, role }
POST /api/auth/login       →  { email, password }              →  { token, email, role }
GET  /api/auth/me          →  (JWT in header)                  →  { id, email, fullName, role }
```

**Smallest working version**: User can register, login, and see their profile. Protected routes redirect to login.

---

### Module 2: Symptom Analysis

**Why it exists**: This is the core feature. Users describe symptoms in everyday language, and the system translates them into medical terminology with department recommendations.

**User flow**:
```
User goes to /symptoms
→ Types "I've been having bad headaches for a week and my eyes are blurry"
→ Clicks "Analyze"
→ Sees loading spinner
→ Sees results:
   ┌──────────────────────────────────────────────────────┐
   │  Your Symptom Analysis                               │
   │                                                      │
   │  🔴 Overall Severity: Moderate                       │
   │                                                      │
   │  Symptom: "bad headaches"                            │
   │  Medical Term: Cephalalgia                           │
   │  Department: Neurology                               │
   │  Severity: Moderate                                  │
   │                                                      │
   │  Symptom: "eyes are blurry"                          │
   │  Medical Term: Blurred Vision (Amblyopia)            │
   │  Department: Ophthalmology                           │
   │  Severity: Moderate                                  │
   │                                                      │
   │  📋 Recommended: Visit Neurology or Ophthalmology    │
   │                                                      │
   │  [Find Hospitals →]  [Estimate Cost →]               │
   └──────────────────────────────────────────────────────┘
→ Can click "Find Hospitals" to go to hospital finder with department pre-filled
→ Can view past analyses in history tab
```

**Frontend responsibility**: Text input form, display AI results, show severity with color coding, link to hospital finder.

**Backend responsibility**: Receive text → call Gemini API → parse response → save to database → return structured result.

**Database responsibility**: Store every analysis (user_id, raw_input, ai_response, severity, created_at) so users can view history.

**APIs**:
```
POST /api/symptoms/analyze    →  { text: "..." }           →  { symptoms: [...], severity, departments }
GET  /api/symptoms/history    →  (JWT, paginated)           →  [ { id, rawInput, severity, createdAt }, ... ]
GET  /api/symptoms/{id}       →  (JWT)                      →  { full analysis details }
```

**Files**:
| File | Responsibility |
|---|---|
| `SymptomController.java` | Receives HTTP, delegates to service, returns response |
| `SymptomService.java` | Orchestrates: calls GeminiService, saves to DB, builds response |
| `GeminiService.java` | Builds prompt, calls Gemini API via HTTP, parses JSON response |
| `SymptomRecord.java` | JPA entity — maps to `symptom_records` table |
| `SymptomRecordRepository.java` | `findByUserIdOrderByCreatedAtDesc(Long userId)` |
| `SymptomRequest.java` | DTO: `{ text: String }` |
| `SymptomResponse.java` | DTO: `{ symptoms: List, overallSeverity: String, recommendedDepartments: List }` |

**Smallest working version**: User types symptoms, gets back a JSON response from Gemini with medical terms and departments. No history, no severity badges — just the API call working end-to-end.

---

### Module 3: Hospital Finder

**Why it exists**: After symptom analysis tells you "Visit Neurology", you need to find hospitals with a Neurology department nearby.

**User flow**:
```
User goes to /hospitals
→ Enters city name OR allows browser geolocation
→ Optionally filters by department (pre-filled from symptom analysis)
→ Sees list of hospitals:
   ┌──────────────────────────────────────────┐
   │  City General Hospital    ★ 4.2          │
   │  📍 123 Main St, Mumbai                  │
   │  🏥 Government | Departments: Neuro, ENT │
   │  📞 022-12345678                         │
   │  [View Details]  [Estimate Cost]         │
   └──────────────────────────────────────────┘
→ Clicks "View Details" to see full info
```

**Frontend responsibility**: Search form (city, department filters), hospital card list, detail page.

**Backend responsibility**: Query hospitals by city/department, sort by rating, return paginated results.

**Database responsibility**: Store hospital records with city, departments, coordinates, and contact info.

**APIs**:
```
GET /api/hospitals?city=Mumbai&department=Neurology    →  [ { hospital data }, ... ]
GET /api/hospitals/{id}                                →  { full hospital details }
```

**"Nearby" without PostGIS**: For the first version, just filter by city. If you later want distance-based search, use the Haversine formula in a `@Query` annotation — it's one SQL query, no PostGIS needed.

**Smallest working version**: Admin adds hospitals via API. Users search by city. No map, no geolocation — just a search box and a list.

---

### Module 4: Cost Estimation

**Why it exists**: Users want to know "How much will a Neurology consultation cost at a private hospital in Mumbai?"

**User flow**:
```
User goes to /costs
→ Selects treatment type (Consultation, Surgery, Lab Test, etc.)
→ Selects department (pre-filled from symptom analysis)
→ Selects city
→ Selects hospital type (Government / Private)
→ Clicks "Estimate"
→ Sees:
   ┌──────────────────────────────────────────┐
   │  Estimated Cost                          │
   │                                          │
   │  Treatment: Neurology Consultation       │
   │  City: Mumbai                            │
   │  Hospital Type: Private                  │
   │                                          │
   │  Estimated Range: ₹500 — ₹2,000         │
   │                                          │
   │  Note: This is an estimate based on      │
   │  average costs. Actual costs may vary.   │
   └──────────────────────────────────────────┘
```

**Frontend responsibility**: Form with dropdowns, display estimated cost range.

**Backend responsibility**: Look up treatment costs from database, return min/max range.

**Database responsibility**: Store reference pricing data (treatment, department, city, min_cost, max_cost).

**APIs**:
```
POST /api/costs/estimate    →  { treatment, department, city, hospitalType }  →  { min, max, average }
GET  /api/costs/treatments  →  (no params)                                    →  [ "Consultation", "Lab Test", ... ]
```

**Smallest working version**: Admin adds pricing data. User selects options from dropdowns, sees a price range. No fancy calculations — just a database lookup.

---

### Module 5: Admin

**Why it exists**: Someone needs to add hospitals and treatment costs to the database. That's the admin.

**User flow**:
```
Admin logs in (same login page, but role = ADMIN)
→ Sees Admin Dashboard link in Navbar
→ Can:
   - View all users (list)
   - Add/edit hospitals
   - Add/edit treatment costs
   - View system statistics (total users, total analyses)
```

**APIs**:
```
GET    /api/admin/users                    →  [ user list ]
POST   /api/admin/hospitals                →  { hospital data }  →  { created hospital }
PUT    /api/admin/hospitals/{id}           →  { updated data }   →  { updated hospital }
POST   /api/admin/costs                    →  { cost data }      →  { created cost entry }
GET    /api/admin/stats                    →  { totalUsers, totalAnalyses, totalHospitals }
```

**Smallest working version**: Admin can add a hospital and add treatment costs via the API (even without a UI — use Postman). The UI is a nice-to-have that comes after the API works.

---

## Development Roadmap — Step by Step

### Phase 1: Project Setup (Day 1-2)

```
What you do:
  1. Create "backend" folder → initialize Spring Boot project (start.spring.io or IntelliJ)
     Dependencies: Spring Web, Spring Security, Spring Data JPA, PostgreSQL Driver, Lombok
  2. Create "frontend" folder → run: npm create vite@latest ./ -- --template react
  3. Install frontend deps: npm install axios react-router-dom
  4. Install Tailwind CSS: follow official Tailwind + Vite guide
  5. Install PostgreSQL on your machine → create database "mediguide"
  6. Configure application.properties with database connection
  7. Create schema.sql with users table
  8. Run backend → verify it starts without errors
  9. Run frontend → verify it shows default Vite page
  10. Create .gitignore, initialize git repo

What you learn:
  - How Spring Boot projects are structured
  - How Vite projects are structured
  - How to connect Spring Boot to PostgreSQL
  - How schema.sql works

Deliverable: Both projects start. Backend connects to database. Frontend shows a page.
```

### Phase 2: User Registration (Day 3-5)

```
What you build:
  Backend:
    1. User.java entity
    2. UserRepository.java
    3. RegisterRequest.java DTO
    4. AuthResponse.java DTO
    5. AuthService.java (register method only)
    6. AuthController.java (register endpoint only)
    7. SecurityConfig.java (permit /api/auth/**)
    8. CorsConfig.java

  Frontend:
    1. RegisterPage.jsx (form with email, password, name)
    2. authService.js (register function)
    3. api.js (Axios instance)
    4. App.jsx (basic routing: / and /register)

What you learn:
  - How @Entity maps to a database table
  - How Spring Data generates queries from method names
  - How BCrypt password hashing works
  - How DTOs separate internal data from API contracts
  - How CORS works and why it's needed
  - How React forms work
  - How Axios makes POST requests

Deliverable: User fills form → data saved to database → success message shown.
             No JWT yet. No login yet. Just registration.

Why registration before login?
  Registration is simpler — it doesn't require JWT. It teaches you the full
  request flow (React → Axios → Controller → Service → Repository → Database)
  without the complexity of authentication. Once you understand this flow,
  adding JWT for login is incremental.
```

### Phase 3: User Login + JWT (Day 6-10)

```
What you build:
  Backend:
    1. JwtUtil.java (generate + validate tokens)
    2. JwtFilter.java (intercept requests, validate token)
    3. CustomUserDetailsService.java
    4. Update SecurityConfig.java (add JWT filter)
    5. LoginRequest.java DTO
    6. AuthService.java (add login method)
    7. AuthController.java (add login + me endpoints)

  Frontend:
    1. LoginPage.jsx
    2. AuthContext.jsx (store user + token, provide login/logout functions)
    3. Update api.js (add JWT interceptor — attach token to every request)
    4. ProtectedRoute.jsx
    5. Navbar.jsx (show login/logout based on auth state)
    6. Update App.jsx (wrap with AuthProvider, add protected routes)
    7. ProfilePage.jsx (show current user info)

What you learn:
  - How JWT tokens work (header.payload.signature)
  - How stateless authentication works (no sessions)
  - How Spring Security filter chain works
  - How Axios interceptors work
  - How React Context provides global state
  - How protected routes redirect unauthenticated users

Deliverable: Full auth flow — register, login, see profile, logout.
             Protected routes redirect to login.
             JWT token persisted in localStorage.
```

### Phase 4: Symptom Analysis (Day 11-18)

```
What you build:
  Backend:
    1. GeminiService.java (HTTP call to Gemini API)
    2. SymptomService.java (orchestrate analysis + save)
    3. SymptomController.java
    4. SymptomRecord.java entity
    5. SymptomRecordRepository.java
    6. SymptomRequest.java + SymptomResponse.java DTOs
    7. WebClientConfig.java (or RestTemplate config)

  Frontend:
    1. SymptomPage.jsx (text input + results display)
    2. symptomService.js (API call)
    3. Add route to App.jsx

What you learn:
  - How to call external APIs from Spring Boot
  - How to design prompts for AI APIs
  - How to parse JSON responses
  - How to save analysis results to database
  - How to display complex data in React
  - How to handle loading and error states

Deliverable: User types symptoms → sees medical terms, departments, severity.
             Results saved to database. History page shows past analyses.

This is the hardest phase because:
  - You're integrating an external API (Gemini) for the first time
  - You need to design good prompts
  - You need to handle API errors gracefully
  - You need to parse unpredictable AI responses

Take your time here. Get the API call working in isolation (Postman or a test)
before integrating it into the full flow.
```

### Phase 5: Hospital Finder (Day 19-24)

```
What you build:
  Backend:
    1. Hospital.java entity
    2. HospitalRepository.java
    3. HospitalService.java (search by city, filter by department)
    4. HospitalController.java
    5. HospitalResponse.java DTO
    6. Add hospitals table to schema.sql
    7. Add data.sql with 20-30 sample hospitals

  Frontend:
    1. HospitalPage.jsx (search + results)
    2. HospitalDetailPage.jsx
    3. hospitalService.js
    4. HospitalCard.jsx component

What you learn:
  - How to implement search with filters
  - How to use @Query for custom JPA queries
  - How to seed sample data
  - How to build a list/detail page pattern
  - How to link modules (symptom → hospital via department)

Deliverable: Users search hospitals by city, filter by department.
             "Find Hospitals" button on symptom results pre-fills the department filter.
```

### Phase 6: Cost Estimation (Day 25-29)

```
What you build:
  Backend:
    1. TreatmentCost.java entity
    2. TreatmentCostRepository.java
    3. CostService.java
    4. CostController.java
    5. CostRequest.java + CostResponse.java DTOs
    6. Seed data for treatment costs

  Frontend:
    1. CostPage.jsx (form + results)
    2. costService.js

What you learn:
  - How to build a lookup/estimation service
  - How to use dropdowns populated from API data
  - How to format currency in the frontend

Deliverable: User selects treatment + city → sees estimated cost range.
```

### Phase 7: Admin Features (Day 30-35)

```
What you build:
  Backend:
    1. AdminController.java
    2. AdminService.java
    3. Update SecurityConfig (restrict /api/admin/** to ADMIN role)
    4. @PreAuthorize("hasRole('ADMIN')") annotations

  Frontend:
    1. AdminDashboard.jsx (stats + management panels)
    2. AdminRoute.jsx (role-based guard)
    3. Forms for adding hospitals and costs

What you learn:
  - How role-based access control works
  - How @PreAuthorize works in Spring Security
  - How to build admin interfaces
  - How to protect frontend routes by role

Deliverable: Admin can manage hospitals, costs, and view user statistics.
```

### Module Dependency Diagram

```
Phase 1: Setup
    │
    ▼
Phase 2: Registration  ←── Must exist before login (need users in DB)
    │
    ▼
Phase 3: Login + JWT   ←── Must exist before any protected feature
    │
    ├──────────────────────┐
    ▼                      ▼
Phase 4: Symptoms     Phase 5: Hospitals   ←── Can be built in parallel
    │                      │                    but hospitals can use
    │                      │                    department from symptoms
    ├──────────────────────┘
    ▼
Phase 6: Costs         ←── Uses treatment types + cities from hospitals
    │
    ▼
Phase 7: Admin         ←── Manages data for hospitals + costs
```

---

## Maven Dependencies (pom.xml)

```xml
<dependencies>
    <!-- Web: Lets you create REST APIs with @RestController -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Security: Authentication and authorization -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <!-- JPA: Database access without writing SQL -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Validation: @NotBlank, @Email annotations on DTOs -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- PostgreSQL driver: Connects Java to PostgreSQL -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- JWT: Create and validate JSON Web Tokens -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.12.6</version>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.12.6</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId>
        <version>0.12.6</version>
        <scope>runtime</scope>
    </dependency>

    <!-- Lombok: Reduces boilerplate (auto-generates getters/setters/constructors) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>

    <!-- WebClient: For calling Gemini API -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>

    <!-- Testing -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Why each dependency?

| Dependency | Without it... | Lines of code it saves |
|---|---|---|
| `spring-boot-starter-web` | You can't create API endpoints | You'd need to manually configure a servlet container |
| `spring-boot-starter-security` | You'd manually check tokens in every controller method | ~100 lines of duplicated auth code |
| `spring-boot-starter-data-jpa` | You'd write raw JDBC: `connection.prepareStatement("SELECT * FROM users WHERE email = ?")` | ~20 lines per query |
| `spring-boot-starter-validation` | You'd write `if (email == null) throw ...` for every field | ~5 lines per field |
| `postgresql` | Can't connect to PostgreSQL | N/A — it's a driver |
| `jjwt` | You'd manually encode/decode Base64 JSON + HMAC signatures | ~50 lines of crypto code |
| `lombok` | You'd write getters, setters, constructors for every class | ~15 lines per entity |
| `spring-boot-starter-webflux` | You'd manually use `HttpURLConnection` to call Gemini | ~30 lines of connection management |

### Why Lombok?

Without Lombok, a simple User entity looks like this:
```java
public class User {
    private Long id;
    private String email;
    private String password;
    private String fullName;

    public User() {}
    public User(String email, String password, String fullName) {
        this.email = email;
        this.password = password;
        this.fullName = fullName;
    }
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    // ... 10 more methods ...
}
```

With Lombok:
```java
@Data @Entity @NoArgsConstructor @AllArgsConstructor
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String email;
    private String password;
    private String fullName;
}
```

Same result. `@Data` generates all getters, setters, equals, hashCode, and toString. It's not magic — it's a code generator that runs at compile time. You can always remove it and write the methods by hand if you want to understand what it does.

---

## Frontend Dependencies (package.json)

```json
{
  "dependencies": {
    "react": "^19.x",
    "react-dom": "^19.x",
    "react-router-dom": "^7.x",
    "axios": "^1.x"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.x",
    "tailwindcss": "^4.x",
    "vite": "^6.x"
  }
}
```

**That's 4 runtime dependencies. Four.** React, ReactDOM, React Router, Axios. Everything else is a dev tool.

---

## How To Run The Project

```
Terminal 1 — Backend:
  cd backend
  mvn spring-boot:run

Terminal 2 — Frontend:
  cd frontend
  npm run dev

Browser:
  Open http://localhost:5173
```

No Docker. No docker-compose. No build scripts. Two terminals.

---

## Verification Plan

After each phase, verify with these checks:

| Phase | How to verify |
|---|---|
| Phase 1 (Setup) | Backend starts without errors. Frontend shows Vite default page. Database has `users` table. |
| Phase 2 (Register) | POST to `/api/auth/register` with Postman → user appears in database. React form submits successfully. |
| Phase 3 (Login) | POST to `/api/auth/login` → get JWT token. Use token to access GET `/api/auth/me`. Without token → 401 error. |
| Phase 4 (Symptoms) | POST to `/api/symptoms/analyze` with `{ "text": "headache and fever" }` → get medical terms back. Record saved in database. |
| Phase 5 (Hospitals) | GET `/api/hospitals?city=Mumbai` → returns hospital list. Frontend shows cards. |
| Phase 6 (Costs) | POST `/api/costs/estimate` → returns price range. Frontend shows results. |
| Phase 7 (Admin) | Admin user can add hospitals. Regular user gets 403 on admin endpoints. |

---

## Open Questions for You

> [!IMPORTANT]
> **Gemini API vs OpenAI API**: Both work identically for this project. Gemini has a free tier (good for learning). OpenAI has better documentation. Which do you prefer?

> [!NOTE]
> **WebClient vs RestTemplate for Gemini calls**: RestTemplate is older and simpler (blocking calls). WebClient is newer (non-blocking). For a learning project, RestTemplate is easier to understand. Do you have a preference?

> [!NOTE]
> **Hospital data**: You'll need sample hospital data for Phase 5. Options: (1) manually create 20-30 entries in a `data.sql` file, or (2) use a public dataset. I'd suggest option 1 for simplicity. Thoughts?
