# Frontend Guideline Document

This document lays out the architecture, design principles, and technologies used in the WorkLifeBalance_Andy frontend. It’s written in everyday language so anyone can understand how the UI is built, styled, organized, and tested.

---

## 1. Frontend Architecture

### 1.1 Overview
- The app is a desktop application built with **Electron**, which wraps a web-style interface into a standalone program for Windows, macOS, and Linux.  
- The user interface is built in **React** using **TypeScript** for extra safety and clarity.  
- Under the hood, **Node.js** powers the timer logic, file I/O, and settings management.

### 1.2 How It Supports Scalability, Maintainability & Performance
- **Component-based**: Breaking the UI into small, focused React components (buttons, timer display, sidebar) makes it easy to add, remove, or update features.  
- **TypeScript types**: Catch mistakes before they reach users and make the code easier to understand.  
- **Event-driven core**: The timer emits events (start, tick, finish), and UI, notifications, and storage code subscribe to only the events they need—this separation keeps pieces loosely coupled.  
- **Modular folders**: Organizing code by feature (e.g., `components/`, `contexts/`, `hooks/`, `assets/`) helps new developers find their way around quickly.  

---

## 2. Design Principles

### 2.1 Usability
- Big, obvious buttons for “Start Work,” “Start Rest,” “Pause,” “Stop.”  
- Live countdown display at the center of the screen so users always know where they stand.  
- Clear labels and icons (Work.png, Rest.png) that match their actions.

### 2.2 Accessibility
- Keyboard shortcuts for start/pause/stop.  
- Semantic HTML elements (`<button>`, `<nav>`, `<main>`) for screen readers.  
- High-contrast text and focus states for form fields and buttons.

### 2.3 Responsiveness
- The layout adapts when the user resizes the window: sidebar collapses, content stacks vertically on narrow widths.  
- Flexible CSS grid and flexbox ensure the timer, buttons, and table of history always fit nicely.

---

## 3. Styling and Theming

### 3.1 Styling Approach
- **CSS with BEM naming** (Block__Element--Modifier) keeps class names predictable and avoids conflicts.  
- All styles live in plain `.css` files, imported either globally or scoped via CSS Modules for component isolation.

### 3.2 Theming
- We support **light** and **dark** themes by toggling a `data-theme` attribute on `<html>`.  
- Theme variables (colors, backgrounds) are defined in a central CSS file using CSS custom properties (CSS variables).

### 3.3 Visual Style
- **Flat design** with subtle shadows and smooth transitions.  
- Minimalist icons and plenty of whitespace for a calm, focused look.

### 3.4 Color Palette
- Primary Blue: #2F80ED  
- Secondary Yellow: #F2C94C  
- Success Green: #6FCF97  
- Error Red: #EB5757  
- Dark Gray (text): #333333  
- Light Gray (background): #EEEEEE  
- White: #FFFFFF

### 3.5 Typography
- Font family: **Inter**, sans-serif fallback.  
- Headings: 600 weight, body text: 400 weight.  
- Line height: 1.5 for readability.

---

## 4. Component Structure

### 4.1 Organization
- `src/components/atoms/` – simple elements (Icon, Button, Input).  
- `src/components/molecules/` – small groups of atoms (TimerDisplay with label and icon).  
- `src/components/organisms/` – larger sections (Sidebar, HistoryTable, SettingsForm).  
- `src/pages/` – top-level views (DashboardPage, HistoryPage, SettingsPage).

### 4.2 Reuse and Maintainability
- Each component lives in its own folder with `Component.tsx`, `Component.css`, and `Component.test.tsx`.  
- Clear Prop types define what data a component needs, making reuse straightforward.

---

## 5. State Management

### 5.1 Approach
- We use **React Context** + **useReducer** to handle global state:
  - `TimerContext` tracks current mode, remaining time, and running/paused status.  
  - `SettingsContext` holds user preferences (durations, theme, auto-cycle).  
  - `HistoryContext` (optional) caches past sessions for quick display.

### 5.2 Sharing Across Components
- Any component can read or dispatch timer actions (`START`, `PAUSE`, `STOP`) via context hooks (`useTimer()`).  
- Settings updates happen centrally, and changes propagate immediately throughout the UI.

---

## 6. Routing and Navigation

### 6.1 Library
- **React Router** handles in-app navigation without full page reloads.

### 6.2 Route Structure
- `/` → Dashboard (main timer view)  
- `/history` → HistoryTable view (DataSheet)  
- `/settings` → SettingsForm view

### 6.3 Sidebar Navigation
- A collapsible sidebar with links to each page, using `<NavLink>` for active-link styling.  
- A gear icon in the header also opens the settings page.

---

## 7. Performance Optimization

- **Code Splitting**: Use `React.lazy` and `Suspense` to load History and Settings pages only when the user navigates there.  
- **Asset Optimization**: Compress PNG icons and delay loading audio (`finish.mp3`, etc.) until needed.  
- **Memoization**: Wrap heavy components (HistoryTable rows) in `React.memo` and use `useCallback` for event handlers.  
- **Efficient Rendering**: Avoid unnecessary re-renders by keeping contexts focused and components small.

---

## 8. Testing and Quality Assurance

### 8.1 Unit and Integration Tests
- **Jest** + **React Testing Library** for:
  - Component rendering and user interactions (clicking buttons, typing inputs).  
  - Context reducers (timer logic, settings updates).

### 8.2 End-to-End (E2E) Tests
- **Spectron** for automated tests inside the Electron shell: simulate clicks, verify that pages render, and confirm timer behavior.

### 8.3 Linting & Formatting
- **ESLint** + **Prettier** + **EditorConfig** enforce consistent code style and catch errors early.

### 8.4 CI/CD Checks
- GitHub Actions run linting, unit tests, and build verification on every commit to the main branch.

---

## 9. Conclusion and Overall Frontend Summary

This frontend setup—Electron + React + TypeScript—delivers a clear, maintainable, and high-performance desktop experience. We’ve paired a component-based architecture with simple CSS and theming to ensure consistency and ease of updates. React Context and React Router keep state and navigation straightforward, while code splitting and memoization optimize speed. A robust testing suite and CI/CD pipeline guarantee quality with each release.

Together, these guidelines ensure that any developer (or non-developer) can understand, extend, and maintain the UI for WorkLifeBalance_Andy without ambiguity.