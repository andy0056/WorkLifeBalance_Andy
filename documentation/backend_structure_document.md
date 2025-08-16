# Backend Structure Document for WorkLifeBalance_Andy

This document explains how the backend of WorkLifeBalance_Andy is organized, hosted, and maintained. It uses everyday language so that anyone—technical or not—can understand the setup without confusion.

## 1. Backend Architecture

- **Platform:** Node.js runtime embedded inside an Electron desktop application. This means our backend logic runs on your computer without needing an external server.
- **Design Pattern:** Event-driven and modular:
  - **Event Emitter:** The timer core emits events (e.g., sessionStarted, sessionEnded), and other modules (UI updater, notification player, data saver) subscribe to those events.
  - **Separation of Concerns:** We split logic into clear modules:
    - Timer Core (manages work/rest countdowns)
    - Settings Manager (loads/saves preferences)
    - Data Manager (reads/writes session history)
    - Notification System (plays sounds or shows visual alerts)
- **Scalability & Maintainability:** Even though this is a local desktop app, the modular approach:
  - Lets us add new activities or features without touching unrelated parts.
  - Enables unit testing of each module in isolation.
- **Performance:** Node.js handles file I/O and timing efficiently, while Electron’s renderer process updates the screen. Timers use high-precision APIs (`performance.now()`) to keep drift under 1–2 seconds per hour.

## 2. Database Management

- **Primary Storage:** Embedded SQLite database (accessed via the `better-sqlite3` library).
  - Supports reliable transactions and recovery in case of a crash.
  - Needs no separate installation—just a single file in the user’s AppData or Home folder.
- **Fallback Storage:** JSON files.
  - If the SQLite file becomes corrupted or if the user prefers a lighter approach, the app switches to a plain JSON file for both settings and session history.
- **Data Access:** All reads and writes go through the Data Manager module:
  - Hides low-level file and database operations from other parts of the app.
  - Ensures consistent data validation and error handling.
- **Best Practices:** 
  - Database writes use transactions or temporary files to avoid corruption.
  - Read/write operations are throttled and batched to minimize disk usage.

## 3. Database Schema

Below is a human-readable description followed by the SQL definitions for our SQLite tables.

Activities Table (stores all available activity types, including Work, Rest, and any custom ones)
- **Columns:**
  - `id` – unique number for each activity type
  - `name` – label of the activity (e.g., Work, Rest, Gym)
  - `defaultDuration` – default time in seconds

Sessions Table (logs each completed or paused session)
- **Columns:**
  - `id` – unique session identifier
  - `activityId` – links to the activity table
  - `startTime` – timestamp when the session began
  - `endTime` – timestamp when the session ended
  - `duration` – actual elapsed time in seconds

Settings Table (stores user preferences)
- **Columns:**
  - `key` – setting name (e.g., workDuration, autoCycle)
  - `value` – stored as text, number, or JSON depending on the setting

SQL Definitions:

```sql
CREATE TABLE Activities (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL UNIQUE,
  defaultDuration INTEGER NOT NULL  -- in seconds
);

CREATE TABLE Sessions (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  activityId INTEGER NOT NULL,
  startTime TEXT NOT NULL,         -- ISO 8601 timestamp
  endTime TEXT NOT NULL,           -- ISO 8601 timestamp
  duration INTEGER NOT NULL,       -- in seconds
  FOREIGN KEY (activityId) REFERENCES Activities(id)
);

CREATE TABLE Settings (
  key TEXT PRIMARY KEY,
  value TEXT NOT NULL              -- JSON string or plain text
);
```

## 4. API Design and Endpoints

Although this is a desktop app without a web server, we expose a set of internal APIs via Electron’s IPC (inter-process communication). This lets the front-end code (renderer) ask the backend (main process) to perform actions in a clear, REST-like way:

- **Settings APIs**
  - `getSettings()` – returns all user preferences
  - `updateSetting(key, value)` – changes one preference
- **Activity APIs**
  - `listActivities()` – returns an array of all activity types
  - `addActivity(name, defaultDuration)` – creates a new activity
  - `updateActivity(id, name, defaultDuration)` – edits an existing activity
  - `deleteActivity(id)` – removes an activity
- **Session APIs**
  - `startSession(activityId)` – records the start of a new session
  - `endSession(sessionId)` – marks a session as ended and logs its duration
  - `getSessions(page, pageSize)` – returns a paginated list of past sessions
- **Notification API**
  - `playNotification(type)` – plays a sound or flashes the screen depending on availability

Each of these IPC channels behaves like a simple REST endpoint, carries parameters and returns results or error messages, and ensures clear separation between UI code and data/logic code.

## 5. Hosting Solutions

- **Local Desktop Execution:** The entire backend runs on the user’s own machine—no remote servers, no internet required.
- **Packaging:** We use Electron Builder to wrap our Node.js code and assets into a single installable application on Windows, macOS, and Linux.
- **Benefits:**
  - **Reliability:** Runs offline without relying on network availability.
  - **Cost‐Effective:** No hosting fees or cloud infrastructure to maintain.
  - **Privacy:** All data stays on the user’s computer.

## 6. Infrastructure Components

Even in a locally hosted desktop app, there are supporting pieces that keep things running smoothly:

- **IPC (Inter-Process Communication):** Secure channels between the UI and backend logic, ensuring only defined APIs are accessible.
- **In-Memory Cache:** Frequently used data (like settings and activity lists) are cached in memory for instant access, reducing disk reads.
- **File System Watchers:** Monitor the data files for external changes or corruption and trigger recovery routines if needed.
- **Builder & Updater:** Electron Builder not only packages the app but also supports auto-updates, downloading and applying new versions seamlessly.

## 7. Security Measures

- **File Permissions:** Data and settings files are created in the user’s AppData (Windows) or Home folder (macOS/Linux) with default OS protections—no world‐writable or system‐wide access.
- **Atomic Writes:** Settings and history files are written to a temporary file first, then renamed on success, preventing partial writes or corruption.
- **IPC Hardening:** Only whitelisted channels are open between renderer and main processes; remote code execution is disabled.
- **Validation & Sanitization:** All input from the UI (durations, activity names) is validated in the backend before saving to disk or database.
- **No External Network Calls:** Since the app never sends data to outside servers, user data remains private.

## 8. Monitoring and Maintenance

- **Logging:** Backend events, errors, and warnings are logged to a rolling file in the user’s data folder using a lightweight logger (e.g., Winston).
- **Crash Reporting:** Integration with a service like Sentry (optional) can capture unhandled exceptions and help troubleshoot issues in field.
- **Auto-Update Mechanism:** Electron Builder’s auto-update keeps users on the latest version without manual downloads.
- **Testing & CI/CD:** GitHub Actions run unit tests (Jest) on the backend modules and integration tests (Spectron) on packaged builds before any release.
- **Periodic Cleanup:** Old session entries can be pruned or archived automatically to keep the database size manageable.

## 9. Conclusion and Overall Backend Summary

WorkLifeBalance_Andy’s backend is a fully local, Node.js‐based system embedded in Electron. It uses an event-driven, modular design that separates timing logic, data storage, settings management, and notifications. Data is reliably stored in an embedded SQLite database (with a JSON fallback), and all communication between the user interface and backend occurs through carefully controlled IPC channels.

This setup ensures:
- **Performance:** High-precision timers and in-memory caching keep the UI responsive.
- **Reliability:** Atomic writes, database transactions, and crash-recovery routines protect user data.
- **Security & Privacy:** No external servers means all information stays on the user’s device under OS‐managed permissions.
- **Maintainability:** Modular code, clear IPC-based APIs, and automated testing/CI keep the project easy to extend and update.

Overall, the backend aligns perfectly with our goal: to provide a dependable, private, and easy‐to‐maintain foundation for a desktop timer app that helps users balance work and rest.