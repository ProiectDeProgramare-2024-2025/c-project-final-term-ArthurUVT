# Sports Activity Tracking System

A C application that allows users to record and track various sports activities and view statistics about their performance.

## Features

- Record different types of sports activities:
  - Running
  - Walking
  - Swimming
  - Cardio
  - Strength

- For each activity, record:
  - Date
  - Duration in minutes
  - Distance in kilometers (for running, walking, swimming)
  - Repetitions (for strength exercises)

- View statistics for each activity type:
  - Number of activities performed
  - Average duration
  - Average distance (where applicable)

- Interactive menu system with submenus

## How to Compile

```bash
gcc -o sports_tracker sports_tracker.c
```

## How to Run

```bash
./sports_tracker
```

## Usage Instructions

1. **Main Menu**:
   - Option 1: Add a new activity
   - Option 2: View all recorded activities
   - Option 3: View statistics
   - Option 0: Exit the application

2. **Adding Activities**:
   - Select the type of activity you want to add
   - Enter the requested information (date, duration, distance, etc.)
   - The application will automatically update statistics

3. **Viewing Activities**:
   - Shows a list of all recorded activities with details

4. **Viewing Statistics**:
   - Shows statistics for each type of activity

## Notes

- The application stores data in memory only. All data will be lost when the application is closed.
- The application can store up to 100 activities. 