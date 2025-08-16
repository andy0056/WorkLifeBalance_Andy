# Project Requirements Document: WorkLifeBalance_Andy

## 1. Project Overview

WorkLifeBalance_Andy is a desktop application designed to help individuals manage their work and rest intervals with clarity and ease. At its core, the app functions as a configurable timer that alternates between "Work" and "Rest" periods, giving users visual and audio cues to maintain a healthy rhythm throughout their day. By tracking sessions and storing historical data, it empowers users to reflect on their habits and adjust for an improved work–life balance.

This tool is being built to solve the common problem of losing track of time—either overworking without breaks or taking breaks that stretch too long. Key objectives include: accurate, reliable timing; simple controls for starting, pausing, and stopping sessions; audible alerts to signal transitions; and a settings interface for personalization. Success will be measured by user engagement (daily usage), accuracy of timing within 1–2 seconds over long sessions, and positive feedback on usability and usefulness.

## 2. In-Scope vs. Out-of-Scope

### In-Scope (Version 1.0)
- Core time-tracking engine with start, pause, resume, and stop functions
- Two default activity modes: “Work” and “Rest”
- Custom activities: users can add, rename, or remove labels
- Visual user interface using provided icons (Work.png, Rest.png, Settings.png, DataSheet.png)
- Audio notification system (Finish.mp3, Error.mp3, Termination.mp3)
- Settings panel for interval durations, sound preferences, and basic theme
- Local data persistence (JSON file or embedded SQLite) for session history and user preferences
- Data view (DataSheet) showing past sessions in tabular form
- Event-driven architecture connecting timer core, UI layer, notification system, and settings manager

### Out-of-Scope (Phase 2+)
- Cloud synchronization or multi-device data sharing
- User accounts or sign-in functionality
- Advanced analytics or charting beyond a simple table view
- Mobile or web versions—desktop only in Version 1.0
- AI-driven suggestions or scheduling optimizations
- Plugin or extension architecture for third-party add-ons

## 3. User Flow

When a new user launches WorkLifeBalance_Andy for the first time, they are greeted by a clean landing screen featuring two large buttons—"Start Work" and "Start Rest"—alongside a sidebar icon for the DataSheet view and a gear icon for Settings. The user clicks “Start Work,” and the timer begins counting down from the default work interval (e.g., 25 minutes). During the session, the main screen shows a live countdown, the active mode icon (Work.png), and controls to pause or stop.

Upon timer completion, the app plays the designated sound (Finish.mp3) and automatically switches into the next mode (Rest) if auto-cycle is enabled, or it stops waiting for user input otherwise. The user can navigate to the DataSheet view to see a history of completed sessions or open Settings to adjust work/rest durations, toggle auto-cycle, choose notification sounds, or manage custom activities. All changes are saved immediately, so the next session reflects the updated preferences.

## 4. Core Features

- **Time-Tracking Engine**: start, pause, resume, stop; accurate to within 1–2 seconds over long durations
- **Mode Management**: two default modes (Work/Rest) and support for custom activities
- **Intuitive GUI**: clear icons, live countdown display, sidebar navigation (DataSheet, Settings)
- **Audio Notifications**: plays distinct MP3 files on session end, errors, or forced termination
- **Settings Manager**: edit default durations, auto-cycle toggle, sound on/off, theme choice
- **Data Persistence**: local storage of user preferences and session history in JSON or SQLite
- **Historical Data View**: tabular display (DataSheet) of past sessions with date, duration, and type
- **Event-Driven Architecture**: timer emits events; UI and notification modules subscribe and react

## 5. Tech Stack & Tools

- **Frontend**: Electron (desktop shell), React (UI layer), TypeScript (type safety)
- **Backend/Logic**: Node.js runtime for timer core, file I/O, and settings management
- **Data Storage**: Embedded SQLite (via better-sqlite3) or JSON file fallback
- **Audio Playback**: Howler.js or native HTMLAudioElement for MP3 support
- **Build & Packaging**: Electron Builder for Windows, macOS, and Linux executables
- **IDE & Plugins**: Visual Studio Code with ESLint, Prettier, and EditorConfig
- **Testing**: Jest for unit tests, Spectron for end-to-end Electron tests

## 6. Non-Functional Requirements

- **Performance**: App launch under 2 seconds; UI button response under 100ms; CPU usage < 5% during idle
- **Reliability**: Timer drift < 2 seconds per hour; auto-recover from crashes or unexpected shutdowns
- **Security**: Local data stored in user’s AppData/Home directory with appropriate file permissions
- **Usability**: Clear iconography; keyboard shortcuts for start/pause/stop; consistent look and feel across platforms
- **Accessibility**: Text labels on buttons; support for high-contrast themes; logical tab order

## 7. Constraints & Assumptions

- Users run the application on modern Windows, macOS, or Linux desktops
- Internet connection is not required for core functionality
- No external APIs or cloud services in Version 1.0
- Audio files are shipped with the application—no dynamic downloads
- Electron and Node.js versions must be long-term support (LTS)

## 8. Known Issues & Potential Pitfalls

- **Timer Accuracy Drift**: JavaScript timers (`setTimeout`) can drift under heavy load. Mitigation: use `performance.now()` or native Node.js timers for better precision.
- **Cross-Platform Audio Behavior**: MP3 playback may vary by OS. Mitigation: test Howler.js on each target platform and include fallback codecs if needed.
- **Data File Corruption**: Abrupt shutdown during write can corrupt JSON/SQLite. Mitigation: write temp files and rename on success, employ SQLite transactions.
- **Large Session History**: Thousands of entries could slow the DataSheet view. Mitigation: implement pagination or lazy loading.
- **Packaging Size**: Electron apps tend to be large (~50–80MB). Mitigation: prune unused node modules and enable electron-builder's compression.

---
This document provides a comprehensive, unambiguous reference for any AI or developer to generate detailed technical specifications, UI/UX designs, backend structures, and step-by-step implementation guides for WorkLifeBalance_Andy.