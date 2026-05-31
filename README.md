# Habit_Tracker_Project

### Object Oriented and Functional Programming with Python

### DLBDSOOFPP01 | IU Internationale Hochschule


## Table of Contents
-----------------------

- [Project Overview](#Project-Overview)

- [Project Structure](#Project-Structure)
  
- [Requirements](#Requirements)

- [Installation](#Installation)
  
- [How to Run the App](#How-To-Run-The-App)

- [How to Use Each Command](#How-To-Use-Each-Command)

- [Predefined Habits](#Predefined-Habits)
  
- [Analytics Module](#analytics-module)
  
- [How to Run Tests](#how-to-run-tests)
  
- [Technologies Used](#technologies-used)
  
- [OOP Design](#oop-design)
  
- [Functional Programming Design](#functional-programming-design)
  
- [Database Structure](#database-structure)



### Project Overview


The Habit Tracker is a Python-based command-line application designed to help users build and maintain habits. It allows users to add habits, mark them as complete, track streaks, and analyze progress. The project emphasizes Object-Oriented Programming (OOP) and Functional Programming (FP) principles while persisting data in a lightweight database.

### Project Structure

```

Habit_Tracker_Project/
│
├── habit.py          # Habit class definition
├── database.py       # Database initialization and persistence functions
├── analytics.py      # Analytics functions for streaks and habit insights
├── cli.py            # Command-line interface logic
├── main.py           # Entry point to run the CLI
├── tests/
│   └── test_habit.py # Unit tests for Habit class
└── README.md         # Project documentation

```

### Requirements

Python 3.9+

SQLite (bundled with Python)

Libraries:

argparse (standard library)

unittest (standard library)

datetime (standard library)


### Installation

1. Clone the repository:
   ```
git clone https://github.com/Luphen1/Habit_Tracker_Project.git
cd Habit_Tracker_Project

   ```
2. Ensure Python 3.9+ is installed:

```
python --version

```

### How to Run the App

Run the CLI via:

```
python main.py

```

By default, it lists all habits. You can specify actions with arguments.

### How to Use Each Command

```
List habits:

```
bash
python main.py list

```

Displays all habits with their streaks.

Add a habit:

bash
python main.py add --name "Exercise" --periodicity daily
Adds a new habit with the given name and frequency.

Complete a habit:

bash
python main.py complete --name "Exercise"
Marks the habit as completed for the current date.

View streaks:

bash
python main.py streaks
Shows the longest streak overall and streaks per habit.

bash
python main.py streaks --name "Exercise"
Shows the streak for a specific habit.

```


### Predefined Habits
The system allows users to define habits with daily or weekly periodicity. Any other frequency (e.g., monthly) raises a ValueError.


### Analytics Module

list_habits(habits) → Returns names of all habits.

habits_by_periodicity(habits, periodicity) → Filters habits by frequency.

longest_streak(habits) → Returns the longest streak across all habits.

longest_streak_per_habit(habits) → Returns streaks per habit in dictionary form.


### How to Run Tests

Unit tests are located in tests/test_habit.py. Run them with:

```
python -m unittest tests/test_habit.py
```

Test include:

Habit creation
Task completion
Daily streak calculation
Weekly streak calculation
Invalid periodicity handling


### Technologies Used
Habit creation

Task completion

Daily streak calculation

Weekly streak calculation

Invalid periodicity handling

### OOP Design

Habit Class:

Attributes: name, periodicity, completions

Methods:

complete_task(): Marks a habit as complete

streak(): Calculates streak based on periodicity

Encapsulation ensures habits are self-contained objects with their own logic.


### Functional Programming Design

Analytics functions (list_habits, longest_streak, etc.) are pure functions:

They take input (habit list) and return output without side effects.

This separation makes them reusable and testable.


### Database Structure

SQLite database schema:

```
CREATE TABLE habits (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    periodicity TEXT NOT NULL,
    created_at TEXT NOT NULL
);

CREATE TABLE completions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    habit_id INTEGER NOT NULL,
    completed_at TEXT NOT NULL,
    FOREIGN KEY (habit_id) REFERENCES habits(id)
);

```
habits: Stores habit metadata.

completions: Stores timestamps of completed habits.




