# SleepLift (iOS)

A SwiftUI iOS app for logging sleep, caffeine, workouts, and nutrition—then transforming daily inputs into simple, actionable insights. Includes offline-first local persistence and CSV export designed for downstream analytics and data pipelines.

---

## Why this project
SleepLift consolidates a small set of high-impact variables into a fast, consistent daily workflow:
- sleep duration and subjective quality
- caffeine dose and timing (and whether it’s too late in the day)
- workout duration and effort (RPE)
- daily calories and protein

The goal is frictionless tracking plus practical feedback—especially around caffeine timing relative to a target bedtime.

---

## Features

### Quick logging
- **Sleep:** date, bedtime, wake time, minutes to fall asleep, awakenings, quality (1–5), notes
- **Caffeine:** date/time, source, dose (mg)
- **Workout:** date/time, type, duration, RPE, notes
- **Nutrition:** date, calories, protein, optional notes

### Caffeine cutoff (half-life model)
SleepLift estimates **remaining caffeine at bedtime** using exponential decay (half-life) and computes the **latest time you can take a new dose** while staying under a configurable “allowed mg at bedtime” threshold.

- Remaining caffeine after `t` hours:
  - `remaining = dose * 0.5^(t / halfLifeHours)`
- Total remaining at bedtime is the sum of remaining caffeine across all doses logged earlier that day.
- Latest allowable time solves:
  - `dose * 0.5^(dt / halfLife) <= allowed`
  - `dt >= halfLife * log2(dose / allowed)`

This powers the “Caffeine cutoff” card on the main screen.

### Insights + history
- **Insights:** 7-day summary plus a daily-grain “feature row” view (behavior snapshot by day)
- **History:** scrollable timelines of logged entries by category

### CSV export
- Export CSV snapshots from the app for analysis, dashboards, and pipelines
- Designed to integrate with a companion ELT project (see Related project)

---

## Screenshots

**Overview**  
![Overview](docs/images/overview.png)

**Log Sleep**  
![Sleep](docs/images/sleep.png)

**Log Caffeine**  
![Caffeine](docs/images/caffeine.png)

**Log Workout**  
![Workout](docs/images/workout.png)

**Log Nutrition**  
![Nutrition](docs/images/nutrition.png)

**Insights**  
![Insights](docs/images/insights.png)

**History**  
![History](docs/images/history.png)

**Settings + Export**  
![Settings](docs/images/iOS_categories.png)  
![CSV Export](docs/images/iOS_csv.png)

---

## Tech stack
- **Language:** Swift
- **UI:** SwiftUI
- **Architecture:** lightweight MVVM-style separation (Views + model types + utility engines)
- **Persistence:** local JSON file storage using `Codable` (offline-first)
- **Algorithms:** caffeine half-life decay + “latest safe dose time” calculation

---

## Data persistence (local JSON)
App state is stored locally as JSON using a small file-backed store:
- **Atomic writes** for safer persistence
- **Graceful recovery**: loads fall back to an empty state if the file is missing or corrupted

No external services are required to use the app.

---

## Project structure (high level)
- `Views/` — SwiftUI screens (logging, insights, history, settings)
- `Models/` — data types (sleep entries, caffeine entries, workouts, nutrition)
- `Store/` — JSON persistence layer (`Codable` encode/decode)
- `Utils/` — caffeine calculation engine (half-life logic), formatting, helpers
- `docs/images/` — README screenshots

---

## Running locally
1. Open the project in Xcode
2. Select an iPhone simulator (or a connected device)
3. Build & Run

**Requirements**
- Recent Xcode
- iOS simulator or iOS device

---

## Privacy
All data is stored locally on-device (JSON) unless a CSV export is manually generated. No network calls are required for core functionality.

---

## Future improvements
- Customizable caffeine half-life and bedtime defaults via a user profile
- Optional reminders and streak tracking
- Deeper analytics (e.g., trends/correlations across sleep, caffeine timing, and training)
- More export options (date ranges, per-category export)

---

## Related project (data engineering)
CSV exports from this app are designed to feed a companion ELT pipeline + dashboard:

- **SleepLift-DE** — batch ELT pipeline (Python, DuckDB) with tests and CI  
  https://github.com/jeffreylin0217/sleeplift-de
