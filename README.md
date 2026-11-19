# LUNA — Women's Wellness & Cycle Companion (Native Android)

## 1. Overview
**LUNA** is a private, offline-first native Android application built with **Kotlin, XML layouts, Material Components, Activities + Explicit Intents, RecyclerView + CardView, BottomNavigationView, TabLayout, and SQLite (`SQLiteOpenHelper`)**.

- **7 Feature Screens:** Home, Cycle Calendar, Mood & Wellness, Cycle-Aware Planner, LUNA Care, *Kya Ye Normal Hai?* Checker, and Profile & Settings.
- **5 Bottom Navigation Tabs:** Home, Cycle, Wellness (`Mood | Planner` TabLayout), Learn (hub for LUNA Care & Checker), and Me.
- **100% On-Device Privacy:** All user data stays in local SQLite (`luna_wellness.db`). `android:allowBackup="false"` prevents unintended cloud backups of sensitive health data.

## 2. Database Schema (5 Core Tables)
1. `period_records(id, start_date, end_date, cycle_length, notes, created_at, updated_at)` — `CHECK(end_date IS NULL OR end_date >= start_date)` + repository overlap prevention.
2. `mood_checkins(id, checkin_date UNIQUE, mood, symptoms, energy_level, notes, created_at, updated_at)` — 1 check-in per day, optional Notes integrated inside check-in.
3. `planner_events(id, title, event_date, event_time, description, reminder_enabled, reminder_time, is_completed, created_at, updated_at)`.
4. `health_education(id, category, content_type, title, description, content, created_at)` — Seeded offline across 6 categories (placeholder educational text marked for medical verification).
5. `user_settings(id CHECK(id=1), name, average_cycle_length, average_period_length, reminder_enabled, reminder_preferences, other_preferences)`.

*SharedPreferences* is used **only** for the first-launch `is_onboarding_done` flag.

## 3. Building & Installing the APK on a Real Android Phone
- **Debug APK:** `gradle :app:assembleDebug` → `app/build/outputs/apk/debug/app-debug.apk`
- **Signed Release APK:** Set `KEYSTORE_PATH`, `STORE_PASSWORD`, and `KEY_PASSWORD` environment variables (never hardcoded), then run `gradle :app:assembleRelease` → `app/build/outputs/apk/release/app-release.apk`.
- **Transfer to Phone:**
  1. Connect phone via USB (File Transfer / MTP) or share the `.apk` directly to your phone's `Downloads` folder.
  2. Open the `.apk` file on your Android phone.
  3. If prompted, allow *"Install unknown apps"* for your file manager, then tap **Install**.
  4. Launch **LUNA** from your home screen—works completely offline without a laptop.

## 4. College Viva Questions & Answers
- **Q: Why `SQLiteOpenHelper` instead of Room or Firebase?**
  - **A:** The syllabus focuses on core Android SQLite (`SQLiteOpenHelper`, `ContentValues`, `Cursor`, parameterized queries), and keeping data strictly on-device protects sensitive menstrual health privacy.
- **Q: How do you prevent SQL injection and memory leaks?**
  - **A:** All queries use `?` selection arguments (never string concatenation), and every `Cursor` is automatically closed via Kotlin's `.use {}` block.
- **Q: How are 7 screens organized into 5 bottom tabs without Fragments?**
  - **A:** `BaseActivity` shares the `Toolbar`, Options Menu, and 5-tab `BottomNavigationView` via explicit `Intent`s. `WellnessActivity` hosts Mood and Planner with a `TabLayout` toggling XML containers, and `LearnActivity` is a hub opening `LunaCareActivity` and `SymptomCheckerActivity` with `parentActivityName` Up navigation.
- **Q: How are cycle predictions calculated?**
  - **A:** `CycleCalculator` computes days between successive period start dates, averages up to the last 6 valid cycles (21–45 days), falls back to `user_settings` (default 28 days, labelled *"estimated from your settings"*), and clearly labels every phase and date as an estimate.
