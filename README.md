# Sports Activity Tracking System - C Project

## 1. Introduction

This C application provides a system for users to record, view, and analyze their sports activities. It allows tracking various types of activities (Running, Walking, Swimming, Cardio, Strength) along with relevant metrics like duration, distance, and repetitions. The application features an interactive menu, data persistence between sessions, input validation, and color-coded output for enhanced user experience.

This document provides a detailed overview of the application's features, technical implementation, and usage instructions for evaluation purposes.

## 2. Core Features

*   **Activity Recording:**
    *   Users can add new records for five distinct activity types: Running, Walking, Swimming, Cardio, and Strength.
    *   Each record stores:
        *   **Type:** The kind of activity performed.
        *   **Date:** The date the activity took place (YYYY-MM-DD format, defaults to the current date).
        *   **Duration:** The time spent on the activity in minutes (must be > 0).
        *   **Distance:** Kilometers covered (for Running, Walking, Swimming; must be >= 0).
        *   **Repetitions:** Number of repetitions performed (for Strength; must be >= 0).
*   **Activity Viewing:**
    *   Users can view a formatted list of all recorded activities, displaying all stored details (ID, Type, Date, Duration, Distance, Reps).
    *   Irrelevant fields for specific activities (e.g., Distance for Strength) are marked as "N/A".
*   **Statistics Calculation & Display:**
    *   The application automatically calculates and updates statistics as activities are added or loaded.
    *   Users can view aggregate statistics for each activity type, including:
        *   **Count:** Total number of times the activity was performed.
        *   **Average Duration:** Average time spent per session of that activity type.
        *   **Average Distance:** Average distance covered per session (only for Running, Walking, Swimming).
*   **Interactive Menu System:**
    *   A clear, console-based menu guides the user through the application's functionalities.
    *   The main menu provides access to adding activities, viewing activities, viewing statistics, or exiting.
    *   Selecting "Add Activity" leads to a submenu for choosing the specific activity type.
    *   The screen is cleared between menu transitions for better readability.
    *   Users can navigate back from submenus to the main menu.
*   **Data Persistence:**
    *   Activity data is saved to a file (`activities.csv`) when the application exits.
    *   Data is automatically loaded from the file when the application starts, allowing user data to persist across multiple runs.
*   **Input Validation:**
    *   User input is validated to ensure data integrity and prevent crashes.
    *   Prompts clearly specify the expected input format or constraints (e.g., range, units).
    *   Invalid inputs (incorrect types, out-of-range values) trigger user-friendly error messages, and the user is re-prompted.
*   **Color-Coded Output:**
    *   ANSI color codes are used selectively to enhance readability and user experience.
    *   Key elements like menu titles, activity types, and error messages are colored without affecting the entire line.

## 3. Technical Implementation Details

### 3.1. Data Structures

*   **`ActivityType` (enum):** Defines symbolic constants (RUNNING, WALKING, etc.) for the different activity types, mapping them to underlying integer values (0-4).
    ```c
    typedef enum {
        RUNNING = 0,
        WALKING = 1,
        SWIMMING = 2,
        CARDIO = 3,
        STRENGTH = 4
    } ActivityType;
    ```
*   **`Activity` (struct):** Groups all data related to a single activity record.
    ```c
    typedef struct {
        ActivityType type;
        char date[11];   // Format: YYYY-MM-DD
        float duration;  // in minutes
        float distance;  // in kilometers
        int repetitions; // for strength activities
    } Activity;
    ```
*   **Global Arrays:**
    *   `Activity activities[MAX_ACTIVITIES];`: A fixed-size array holding all loaded/added `Activity` structs. `MAX_ACTIVITIES` is defined as 100.
    *   `int activityCount;`: Tracks the current number of activities stored in the `activities` array.
    *   `int activityTypeCounts[MAX_ACTIVITY_TYPES];`: Stores the total count for each activity type.
    *   `float activityTypeTotalDurations[MAX_ACTIVITY_TYPES];`: Stores the sum of durations for each activity type.
    *   `float activityTypeTotalDistances[MAX_ACTIVITY_TYPES];`: Stores the sum of distances for each relevant activity type.

### 3.2. Key Functions

*   **`main()`**: Entry point. Initializes data (loads from file), manages the main menu loop and submenu navigation logic using `do-while` and `switch` statements, and triggers saving data to file before exiting.
*   **Display Functions (`displayMainMenu()`, `displayAddMenu()`):** Print the respective menu options to the console using `printf`, incorporating color codes.
*   **Add Functions (`addRunning()`, `addWalking()`, etc.):** Handle the user interaction for adding a specific activity type. They prompt for input using helper functions, create an `Activity` struct, add it to the global array, and update statistics.
*   **`viewActivities()`**: Iterates through the `activities` array and prints a formatted table of all recorded activities.
*   **`viewStatistics()`**: Iterates through the possible activity types, calculates averages based on the global statistics arrays, and prints a formatted summary table.
*   **`updateStatistics()`**: Updates the global count, total duration, and total distance arrays based on a newly added activity.
*   **File I/O Functions (`loadActivitiesFromFile()`, `saveActivitiesToFile()`):**
    *   `loadActivitiesFromFile()`: Opens `activities.csv` in read mode (`"r"`), uses `fscanf` in a loop to parse each line according to the CSV format, populates the `activities` array, and updates statistics. Handles file-not-found gracefully.
    *   `saveActivitiesToFile()`: Opens `activities.csv` in write mode (`"w"`), iterates through the `activities` array, and uses `fprintf` to write each activity's data back to the file in the defined CSV format.
*   **Helper Functions:**
    *   `clearScreen()`: Clears the terminal screen using `system("cls")` or `system("clear")`.
    *   `getCurrentDate()`: Gets the current system date and formats it as "YYYY-MM-DD".
    *   `waitForEnter()`: Pauses execution until the user presses Enter.
    *   `getActivityTypeName()`: Returns a colored string representation of an `ActivityType`.
    *   **Validation Helpers (`getIntegerInput()`, `getFloatInput()`, `getStringInput()`):** These crucial functions provide robust input handling. They display a prompt, attempt to read input using `scanf` or `fgets`, validate the input type and range, clear the input buffer using `fgets` to prevent errors on subsequent reads, and loop until valid input is received, showing user-friendly error messages. `getStringInput` also handles default values and empty input.

### 3.3. Data Persistence (`activities.csv`)

*   **File Format:** A simple Comma Separated Values (CSV) file. Each line represents one activity:
    `ActivityTypeIndex,Date,Duration,Distance,Repetitions`
    *Example:* `0,2024-05-20,35.5,5.20,0` (A Running activity)
*   **Loading:** Performed by `loadActivitiesFromFile()` at application start.
*   **Saving:** Performed by `saveActivitiesToFile()` just before the application exits (when user chooses option 0 from the main menu).

### 3.4. Input Validation Approach

Input validation is implemented primarily within the `getIntegerInput`, `getFloatInput`, and `getStringInput` helper functions. The core strategy is:
1.  Prompt the user with clear expectations.
2.  Attempt to read the input using `scanf` (for numbers) or `fgets` (for strings).
3.  Check the return value of `scanf` or analyze the string from `fgets`.
4.  **Crucially**, use `fgets` after `scanf` to consume any leftover characters in the input buffer (including the newline or invalid characters).
5.  Validate the data type and range (e.g., duration > 0, distance >= 0).
6.  If validation fails, print a specific, colored error message.
7.  Loop until valid input is successfully read.

### 3.5. Color Implementation

*   ANSI escape codes (e.g., `\033[0;36m` for cyan) are defined as macros (`COLOR_CYAN`, `COLOR_RESET`, etc.).
*   These macros are embedded within `printf` format strings to start and stop coloring specific text segments (titles, activity names, error messages). This avoids coloring entire lines and enhances visual clarity. Compatibility depends on the terminal supporting ANSI codes.

## 4. How to Compile and Run

1.  **Compilation:** Use a C compiler (like GCC) in the terminal:
    ```bash
    gcc -o sports_tracker sports_tracker.c
    ```
    This creates an executable file named `sports_tracker`.

2.  **Running:** Execute the compiled program from the terminal:
    ```bash
    ./sports_tracker
    ```

3.  **Interaction:** Follow the on-screen menu prompts to add, view, or analyze activities. Enter '0' from the main menu to save data and exit.

## 5. Known Limitations

*   **Fixed Storage Size:** The application uses a fixed-size array (`MAX_ACTIVITIES = 100`) and will prevent adding more activities once this limit is reached.
*   **Basic Date Validation:** While date input accepts a default, it only validates that *some* string was entered. It doesn't rigorously check if the entered string is a valid calendar date (e.g., allows "2024-99-99").
*   **File Overwrite on Error:** If an error occurs during the `saveActivitiesToFile` process *after* the file has been opened in "w" mode but *before* all data is written, the original file content might be lost or truncated. More robust saving would involve writing to a temporary file first.
*   **ANSI Color Compatibility:** Colors may not render correctly on terminals that do not support ANSI escape codes.

