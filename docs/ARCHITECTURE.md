# APG System Architecture

## 1. Architecture Goal

APG must feel fast.

Users will repeatedly change conditions such as budget,
dates, number of rounds, accommodation style, and preferences.

APG should avoid unnecessary external API calls and
recalculate only what actually changed.

---

## 2. High-Level Architecture

User
↓
APG Frontend
↓
APG Search / Optimizer
↓
────────────────────────
Flight Data
Golf Data
Hotel Data
Route Data
────────────────────────
↓
APG Database / Cache
↓
APG Scoring Engine
↓
APG Pick
↓
External Booking

---

## 3. Data Sources

### Flight

Primary candidate:
Amadeus

Purpose:
- Discover direct flights from Korean airports to Japan
- Search flight schedules
- Retrieve flight prices

Supported departure airports:

- ICN
- GMP
- CJJ
- TAE
- PUS

APG does not pre-select Japanese destinations.

The available direct-flight destinations become
candidate destinations automatically.

---

### Golf

Primary source:
Rakuten GORA

Purpose:
- Golf course information
- Available dates
- Tee times
- Prices
- Player conditions
- Additional fees

Booking:
Redirect user to the relevant Rakuten GORA booking page.

---

### Hotel

Purpose:
- Hotel information
- Location
- Price
- Room information
- Photos / reviews

Booking:
Redirect user to an affiliate booking partner.

---

### Route

Purpose:
Calculate:

- Airport → Hotel
- Hotel → Golf Course
- Golf Course → Golf Course
- Golf Course → Airport

Driving time is an important component of APG Score.

---

## 4. Data Pipeline

External APIs
↓
Data Collector
↓
Normalize Data
↓
APG Database
↓
Cache
↓
Optimizer
↓
APG Pick

External data should not be queried from scratch
for every user search.

---

## 5. Cache Strategy

### Direct Flight Routes

Refresh:
Approximately once per week

Reason:
Direct-flight route availability changes relatively slowly.

---

### Flight Schedule

Refresh:
Periodically

Exact interval will be determined after API testing.

---

### Flight Price

Refresh:
Short-term cache

Prices change frequently.

---

### Golf Course Basic Information

Refresh:
Long-term cache

Examples:

- Location
- Course information
- Photos
- Basic facilities
- Review summary

---

### Golf Price / Tee Time

Refresh:
Short-term cache

Availability and prices may change.

---

### Hotel Basic Information

Refresh:
Long-term cache

Examples:

- Location
- Photos
- Facilities
- Review summary

---

### Hotel Price / Availability

Refresh:
Short-term cache

---

### APG Search Results

Repeated searches with equivalent conditions
should reuse previously calculated results when valid.

---

## 6. Search Process

Example input:

Departure:
ICN

Period:
August

Duration:
3 nights / 4 days

Players:
2

Rounds:
3

Target Budget:
KRW 1,200,000 per person

Budget Search Range:
± KRW 300,000

Accommodation:
City Center

Preference:
Balanced

---

### Step 1 — Destination Discovery

Load all Japanese destinations with direct flights
from the selected departure airport.

Example:

ICN
↓
Japan Direct Destinations
↓
FUK
KIX
HIJ
KMJ
OKA
CTS
...

No destination is manually selected by the user.

---

### Step 2 — Flight Candidates

Find viable round-trip flight combinations
for the requested travel period.

---

### Step 3 — Golf Candidates

For each destination:

Find golf courses and tee times compatible
with the flight schedule and number of rounds.

---

### Step 4 — Hotel Candidates

Find hotels compatible with:

- travel dates
- budget
- golf course locations
- accommodation preference

---

### Step 5 — Route Calculation

Calculate estimated driving time between:

Airport
Hotel
Golf Courses

---

### Step 6 — Package Generation

Generate viable combinations:

Flight
+
Hotel
+
Golf
+
Transportation Estimate

---

### Step 7 — Cost Calculation

Calculate estimated total trip cost.

Then calculate:

Total Cost / Number of Travelers

= Estimated Cost Per Person

---

### Step 8 — APG Score

Rank viable plans using factors such as:

- Total cost
- Driving time
- Flight convenience
- Golf course quality
- Accommodation quality
- Budget deviation
- Schedule efficiency

Exact weights will be developed and tested separately.

---

### Step 9 — APG Pick

Return ranked plans.

The highest-ranked plan becomes:

APG Pick

---

## 7. Incremental Recalculation

APG should NOT restart the entire search
when the user changes a simple preference.

Example:

Budget

KRW 1.2M
↓
KRW 1.3M

Do not:

Call every external API again.

Instead:

Reuse existing candidate data
↓
Apply new budget condition
↓
Re-score
↓
Return new APG Pick

---

Another example:

Preference

Lowest Cost
↓
Minimum Driving

Reuse:

Flights
Hotels
Golf Courses
Routes

Then recalculate only the APG Score.

---

## 8. Performance Principle

User actions should feel immediate.

Target:

Cached filtering / ranking:
< 1 second

Normal search:
As fast as practically possible

External API calls should happen only when necessary.

---

## 9. AI Role

AI is NOT responsible for numerical optimization.

Core optimization should be deterministic.

AI may be used for:

- Review summarization
- Recommendation explanation
- Natural-language descriptions

Example:

"APG recommends Hiroshima because this itinerary is
KRW 80,000 below your target budget while reducing
estimated driving time by 47 minutes."

---

## 10. MVP Technical Principle

Keep infrastructure simple and inexpensive.

Initial candidates:

Frontend:
Next.js

Hosting:
Vercel

Database:
Supabase / PostgreSQL

Cache:
Start with database caching

AI:
OpenAI API

Version Control:
GitHub

Do not introduce additional infrastructure
until actual usage requires it.

---

## 11. Core Engineering Principle

Do not calculate what has already been calculated.

Do not fetch what has already been fetched.

Do not make the user wait for data APG could have prepared beforehand.
