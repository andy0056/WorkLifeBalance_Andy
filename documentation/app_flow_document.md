# App Flow Document for WorkLifeBalance_Andy

## Onboarding and Sign-In/Sign-Up

When a user installs and launches WorkLifeBalance_Andy for the first time, the application opens directly to the main interface without requiring any sign-in or account creation. There is no login or sign-up process because all data is stored locally on the user’s machine. On first launch, the app checks for existing settings and data files in the user’s application directory. If none are found, it creates default preference and history files automatically. There is no password recovery or social login to consider, and there is no separate landing page beyond the initial desktop or Start Menu icon click.

## Main Dashboard or Home Page

After launching the application, the user sees the primary dashboard. At the center of the screen is a large circular timer display showing minutes and seconds. Directly below the timer are two prominent buttons labeled “Start Work” and “Start Rest.” At the top right corner of the window is a gear icon that opens the Settings page. Along the left edge of the window is a narrow sidebar that contains two icons: one for viewing the session history (DataSheet) and one for returning to the main timer dashboard. The header bar displays the name of the current mode, and the footer area shows whether auto-cycle is on or off. From this dashboard, the user can immediately begin or switch a session, pause or stop an active session, or navigate to other sections of the application using the sidebar or gear icon.

## Detailed Feature Flows and Page Transitions

When the user clicks the “Start Work” button, the timer begins counting down from the preset work duration. The timer digits update every second, and the work mode icon appears next to the countdown. If the user clicks “Pause,” the timer freezes until they click “Resume.” Clicking “Stop” ends the session immediately, plays the termination sound if configured, and resets the timer to the default work duration. When the work timer reaches zero, the finish sound plays and, if auto-cycle is enabled, the application automatically switches to the rest mode without user intervention. Otherwise, the timer stops waiting for the user to start the next session.

Selecting the “Start Rest” button works in the same way, but the timer counts down from the preset rest duration and displays the rest mode icon. The user can pause, resume, or stop the rest session in exactly the same manner as the work session. At the end of a rest session, the finish sound plays and the app either waits for user input or transitions automatically to the work session based on the auto-cycle setting.

Clicking the DataSheet icon in the sidebar transitions the user to the historical data page. This page lists each completed session by date, type, and duration in a table format. The user can scroll through past sessions or use pagination controls at the bottom of the table to move between pages of history. A back arrow at the top left of the DataSheet page returns the user to the main dashboard.

Within Settings, accessed by clicking the gear icon, the user can change work and rest durations, toggle auto-cycle on or off, select which notification sounds play for session completion or errors, and choose between light or dark themes. If the user edits an invalid value, such as a negative duration, an inline error message appears next to the invalid field and the save button is disabled until a valid number is entered. After all settings are valid, clicking the Save button writes the new preferences to a local file and displays a confirmation message. A return button at the top left of the Settings page brings the user back to the main dashboard.

Advanced users can manage custom activities by opening the Settings page and switching to the Custom Activities tab. There, they can add a new activity type by providing a name and default duration. Editing or deleting a custom activity updates the list of available modes in the main dashboard immediately after saving. Upon returning to the dashboard, the new activity appears as an option in the start menu that appears when the user clicks the timer display.

## Settings and Account Management

Although there are no user accounts, the Settings page serves as the central location for personalizing all aspects of the timer. The user can adjust the duration for work, rest, and any custom activities, toggle sounds on or off individually, and enable or disable the auto-cycle feature. These preferences are stored in a JSON or SQLite file in the user’s home directory. When the app launches, it reads these preferences and applies them to the dashboard so that the first session uses the updated settings. Exiting Settings always returns the user to the main dashboard through the back arrow or the Save and Return button.

## Error States and Alternate Paths

If the user enters an invalid value in Settings, such as letters instead of numbers for a duration, the field outline turns red and an error message appears beneath it. The user cannot save until the input is corrected. During a session, if the application loses access to its data file or detects a corrupted preferences file, it displays an alert dialog explaining the issue and offers two options: restore default settings or retry reading the file. Choosing to restore defaults resets all preferences to the original values and recreates a fresh data file.

If the audio files are missing or cannot be played, the application falls back to a visual flash on the timer display. A notification banner appears briefly to inform the user that audio playback failed. Once the banner disappears, the user may continue timing sessions normally.

If the application experiences an unexpected crash or forced termination, upon relaunch it runs a quick recovery routine. This routine checks if a timer was running at the time of the crash and, if so, it offers to resume the timer with the remaining time or start a fresh session. The user selects an option from a dialog before returning to the main dashboard.

## Conclusion and Overall App Journey

From the moment the user opens WorkLifeBalance_Andy, they are presented with a simple dashboard that lets them start a work or rest session at the click of a button. They can pause or stop a session at any time and receive audio or visual cues at transitions. The sidebar enables navigating to a history view where past sessions are logged, and the gear icon reveals a settings area where durations, sounds, themes, and custom activities can be adjusted. Error handling is built in for invalid input, missing data, or audio failures, and a crash-recovery path ensures minimal disruption to the user’s work-life flow. Overall, users can sign in simply by launching the app, manage their sessions without complexity, and rely on persistent settings and history to maintain and reflect on their productivity habits.