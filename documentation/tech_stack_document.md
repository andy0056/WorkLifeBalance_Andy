# Tech Stack Document for WorkLifeBalance_Andy

This document explains, in everyday terms, the technology choices made for the WorkLifeBalance_Andy application. It’s written so anyone—technical or not—can understand why each tool was selected and how it fits into the overall project.

## 1. Frontend Technologies
These are the tools that build what you see and interact with:

- **Electron**
  - Wraps our web-based interface into a desktop application you can install on Windows, macOS, or Linux.
  - Lets us use familiar web languages (HTML, CSS, JavaScript) to create a native-like desktop experience.

- **React**
  - Breaks the interface into reusable pieces (components) such as the timer display, buttons, and side menu.
  - Makes it easy to update the screen when the timer counts down or when you switch between "Work" and "Rest."

- **TypeScript**
  - Adds a safety net to our JavaScript code by catching mistakes early (for example, if you accidentally try to do math on text).
  - Improves code clarity so developers know exactly what kind of data each part of the app expects.

- **Styling Tools (CSS/Plain Styles)**
  - Simple, easy-to-maintain CSS files define colors, fonts, and layouts.
  - Keeps the look consistent across all screens (main timer, settings, history).

Together, these choices ensure a snappy, intuitive, and visually consistent interface that anyone can understand and navigate.

## 2. Backend Technologies
These power the logic behind the scenes and manage your data:

- **Node.js**
  - Runs the main application logic—managing the timer, responding to button clicks, and handling file operations.
  - Offers built-in tools for reading and writing files, which we use to store your settings and history.

- **SQLite (via better-sqlite3)**
  - Provides a small, embedded database to store session history and user preferences locally on your computer.
  - Works offline and doesn’t require any setup—just a single file in your AppData or Home folder.

- **JSON File Fallback**
  - If you prefer a lighter setup or if the database file gets corrupted, the app can switch to using a simple JSON file to save your data.
  - Ensures recovery from errors and keeps your history safe.

- **Howler.js (Audio Playback)**
  - Plays the built-in notification sounds (Finish.mp3, Error.mp3, Termination.mp3).
  - Falls back to a quick visual flash if audio can’t play for any reason.

This combination handles everything from precise time tracking to reliable storage, all without an internet connection.

## 3. Infrastructure and Deployment
How we build, test, and deliver the app to you:

- **Version Control (Git & GitHub)**
  - Keeps track of every change in the code so we can review, revert, or collaborate safely.
  - Stores the project in a shared repository for team collaboration.

- **Continuous Integration / Continuous Delivery (CI/CD)**
  - Automated scripts (for example, GitHub Actions) run tests and build the app every time we update the code.
  - Ensures that broken code never reaches a release, and new features can be packaged quickly.

- **Electron Builder**
  - Packages the app into installable files for Windows, macOS, and Linux.
  - Compresses and signs installers so you get a smooth setup experience.

- **Testing Tools (Jest & Spectron)**
  - **Jest** runs quick checks on core logic (timer accuracy, settings save/load).
  - **Spectron** automates real-world tests inside the Electron app—simulating clicks and verifying that screens appear correctly.

These infrastructure choices keep the app reliable, make releases straightforward, and ensure a high-quality experience for users.

## 4. Third-Party Integrations
External libraries and services that extend our app:

- **Howler.js** (Audio)
  - Simplifies playing MP3 files across different operating systems.
  - Automatically handles volume control, playback errors, and browser/OS quirks.

- **better-sqlite3** (Database)
  - A fast, easy-to-use library for working with SQLite inside Node.js.
  - Manages low-level details like locking and transactions for safe data access.

These integrations let us focus on our core features—time tracking and user settings—while relying on proven tools for audio and storage.

## 5. Security and Performance Considerations
How we keep your data safe and the app running smoothly:

- **Local Data Protection**
  - All user data (history and settings) is stored in your own AppData or Home folder—no external servers.
  - Files are written safely: temporary files are used during saves and then renamed only if the write succeeds, preventing corruption.

- **File Permissions**
  - The app requests only the permissions it needs to read and write its own data folder.
  - No broad system access, keeping your computer secure.

- **Timer Accuracy**
  - Uses high-precision timers (performance.now()) rather than less-reliable system clocks.
  - Keeps drift under 1–2 seconds even over hours of use.

- **Resource Management**
  - The app idles at under 5% CPU usage when not timing—so it won’t slow down your computer.
  - Lazy-loads history pages to avoid long waits when you have lots of past sessions.

By focusing on local storage, careful file handling, and optimized timing, we deliver a secure, efficient experience.

## 6. Conclusion and Overall Tech Stack Summary
In building WorkLifeBalance_Andy, we chose a set of technologies that together:

- Offer a **consistent, cross-platform desktop experience** (Electron + React + TypeScript).
- Provide **robust timing and storage** without requiring internet access (Node.js + SQLite/JSON).
- Ensure **clear audio and visual notifications** (Howler.js + built-in fallback).
- Support **reliable builds and quality assurance** (Git/GitHub, CI/CD, Electron Builder, Jest, Spectron).
- Maintain strong **security and performance** through local data handling and high-precision timers.

These choices align perfectly with our goal: a simple, trustworthy, and engaging tool to help you balance work and rest. The stack is straightforward enough for quick updates and robust enough to scale with future enhancements—whether that’s adding new activity types, more analytics, or additional platform support.