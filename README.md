# Habit_Tracker_Project

### Object Oriented and Functional Programming with Python

### DLBDSOOFPP01 | IU Internationale Hochschule


## Table of Contents
-----------------------

- [Project Overview](#Project-Overview)

- [Project Structure](#Project-Structure)
  
- [Requirements](#Requirements)

- [Installation](#Installation)

- [Code Implementation](#Code-Implementation)
  
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
├── habit.py: Habit class definition
├── database.py: Database initialization and persistence functions
├── analytics.py: Analytics functions for streaks and habit insights
├── cli.py: Command-line interface logic
├── main.py: Entry point to run the CLI
├── tests/
   └── test_habit_tracker.py: Unit tests for Habit class


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


### Code Implementation

1. habit.py: Defines the Habit class with attributes (name, periodicity, completions) and methods (complete_task(), streak()).

2. cli.py: Provides the command-line interface for interacting with habits (list, add, complete, streaks).

3. main.py: Entry point that runs the CLI.

4. analytics.py: Contains pure functions for analyzing habits (listing, filtering, longest streaks).

5: database.py: Handles SQLite database initialization and persistence.

6. test_habit_tracker.py: Unit tests for verifying habit creation, completion, streaks, and error handling.
   

Each file’s code were included below for reference:

**habit.py**


```

from datetime import datetime
from typing import List, Optional

class Habit:
    """
    Represents a habit with a name, periodicity, creation date,
    and a list of completion timestamps.
    """

    def __init__(self, name: str, periodicity: str, created_at: Optional[datetime] = None):
        # Validate periodicity to avoid invalid habits
        if periodicity not in ("daily", "weekly"):
            raise ValueError("Periodicity must be 'daily' or 'weekly'.")

        self.name = name  # Name of the habit
        self.periodicity = periodicity  # daily or weekly
        self.created_at = created_at or datetime.now()  # When the habit was created
        self.completions: List[datetime] = []  # List of completion timestamps

    def complete_task(self) -> None:
        """Record a completion timestamp for the habit."""
        self.completions.append(datetime.now())

    def streak(self) -> int:
        """Calculate the current streak based on periodicity."""
        if not self.completions:
            return 0

        # Ensure completions are sorted chronologically
        self.completions.sort()

        streak = 1
        for prev, curr in zip(self.completions, self.completions[1:]):
            delta = (curr - prev).days

            if self.periodicity == "daily" and delta == 1:
                streak += 1
            elif self.periodicity == "weekly" and delta == 7:
                streak += 1
            else:
                streak = 1  # Reset streak if broken

        return streak

```

**cli.py**


```

# cli.py
# Command-line interface for interacting with the habit tracker

import argparse
from habit import Habit
from database import (
    init_db, save_habit, get_all_habits,
    complete_habit
)
from analytics import list_habits, longest_streak, longest_streak_per_habit

def run_cli():
    """Run the command-line interface."""
    parser = argparse.ArgumentParser(description="Habit Tracker CLI")

    # Make 'action' optional with nargs='?' and set default to 'list'
    parser.add_argument(
        "action",
        choices=["list", "add", "complete", "streaks"],
        nargs="?",
        default="list",
        help="Action to perform (default: list)"
    )

    parser.add_argument("--name", help="Name of the habit")
    parser.add_argument("--periodicity", choices=["daily", "weekly"], help="Habit frequency")
    args = parser.parse_args()

    # Initialize database
    init_db()
    habits = get_all_habits()

    if args.action == "list":
        print("\n========== HABITS ==========")
        for h in habits:
            print(f"- {h.name} ({h.periodicity}) | Streak: {h.streak()}")
        print("============================\n")

    elif args.action == "add":
        if args.name and args.periodicity:
            save_habit(Habit(args.name, args.periodicity))
            print(f"Habit '{args.name}' added.")
        else:
            print("Provide --name and --periodicity.")

    elif args.action == "complete":
        if args.name:
            complete_habit(args.name)
        else:
            print("Provide --name.")

    elif args.action == "streaks":
        if args.name:
            for h in habits:
                if h.name == args.name:
                    print(f"Streak for '{args.name}': {h.streak()}")
                    break
            else:.
                print(f"Habit '{args.name}' not found.")
        else:
            print("Longest streak:", longest_streak(habits))
            print("Streaks per habit:", longest_streak_per_habit(habits))


```

**main.py**

```

# main.py

from cli import run_cli

if __name__ == "__main__":
    run_cli()
          
```

**analytics.py**

```

def list_habits(habits):
    """Return a list of habit names."""
    return [h.name for h in habits]

def habits_by_periodicity(habits, periodicity):
    """Return habits filtered by periodicity."""
    return [h for h in habits if h.periodicity == periodicity]

def longest_streak(habits):
    """Return the longest streak among all habits."""
    return max((h.streak() for h in habits), default=0)

def longest_streak_per_habit(habits):
    """Return streaks for each habit individually."""
    return {h.name: h.streak() for h in habits}



```

**database.py**

```
import sqlite3
from datetime import datetime, timedelta
from typing import List
from habit import Habit

DB_FILE = "habits.db"  # SQLite database file name

def init_db() -> None:
    """Create required tables if they do not exist."""
    with sqlite3.connect(DB_FILE) as conn:
        cursor = conn.cursor()

        # Create habits table
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS habits (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT UNIQUE NOT NULL,
                periodicity TEXT NOT NULL,
                created_at TEXT NOT NULL
            )
        """)

        # Create completions table
        cursor.execute("""
            CREATE TABLE IF NOT EXISTS completions (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                habit_id INTEGER NOT NULL,
                completed_at TEXT NOT NULL,
                FOREIGN KEY(habit_id) REFERENCES habits(id)
            )
        """)

def save_habit(habit: Habit) -> None:
    """Insert a new habit into the database."""
    with sqlite3.connect(DB_FILE) as conn:
        cursor = conn.cursor()
        cursor.execute("""
            INSERT OR IGNORE INTO habits (name, periodicity, created_at)
            VALUES (?, ?, ?)
        """, (habit.name, habit.periodicity, habit.created_at.isoformat()))

def get_completions(habit_id: int) -> List[datetime]:
    """Return all completion timestamps for a habit."""
    with sqlite3.connect(DB_FILE) as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT completed_at FROM completions WHERE habit_id=?", (habit_id,))
        return [datetime.fromisoformat(row[0]) for row in cursor.fetchall()]

def get_all_habits() -> List[Habit]:
    """Return all habits with their completion history."""
    with sqlite3.connect(DB_FILE) as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT id, name, periodicity, created_at FROM habits")
        rows = cursor.fetchall()

    habits = []
    for habit_id, name, periodicity, created_at in rows:
        habit = Habit(name, periodicity, datetime.fromisoformat(created_at))
        habit.completions = sorted(get_completions(habit_id))
        habits.append(habit)

    return habits

def complete_habit(habit_name: str, date: datetime = None) -> None:
    """Mark a habit as completed (optionally backdated)."""
    with sqlite3.connect(DB_FILE) as conn:
        cursor = conn.cursor()

        cursor.execute("SELECT id FROM habits WHERE name=?", (habit_name,))
        row = cursor.fetchone()

        if not row:
            print(f"Habit '{habit_name}' not found.")
            return

        habit_id = row[0]
        timestamp = (date or datetime.now()).date().isoformat()

        # Prevent duplicate completion for same day
        cursor.execute("SELECT 1 FROM completions WHERE habit_id=? AND completed_at=?", (habit_id, timestamp))
        if cursor.fetchone():
            print(f"Habit '{habit_name}' already completed on {timestamp}.")
            return

        cursor.execute("INSERT INTO completions (habit_id, completed_at) VALUES (?, ?)", (habit_id, timestamp))

def populate_default_habits() -> None:
    """Insert 5 predefined habits and 4 weeks of example data."""
    if get_all_habits():  # Only populate if DB is empty
        return

    defaults = [
        Habit("Brush teeth", "daily"),
        Habit("Workout", "daily"),
        Habit("Read a book", "daily"),
        Habit("Clean house", "weekly"),
        Habit("Call family", "weekly")
    ]

    for h in defaults:
        save_habit(h)

    today = datetime.now()

    # Add 28 days of daily completions
    for i in range(28):
        day = today - timedelta(days=28 - i)
        complete_habit("Brush teeth", day)
        complete_habit("Workout", day)
        complete_habit("Read a book", day)

    # Add 4 weeks of weekly completions
    for i in range(4):
        week = today - timedelta(weeks=4 - i)
        complete_habit("Clean house", week)
        complete_habit("Call family", week)



```


**test_habit_tracker.py**

```
import unittest
from datetime import datetime
from habit import Habit

class TestHabitTracker(unittest.TestCase):
    """Unit tests for the Habit class."""

    def test_habit_creation(self):
        # Test that a habit is created with correct attributes
        h = Habit("Test Habit", "daily")
        self.assertEqual(h.name, "Test Habit")
        self.assertEqual(h.periodicity, "daily")

    def test_habit_completion(self):
        # Test that completing a habit adds a timestamp
        h = Habit("Test Habit", "daily")
        h.complete_task()
        self.assertEqual(len(h.completions), 1)

    def test_daily_streak(self):
        # Test streak calculation for daily habits
        h = Habit("Daily Habit", "daily")
        h.completions = [
            datetime(2024, 1, 1),
            datetime(2024, 1, 2),
            datetime(2024, 1, 3)
        ]
        self.assertEqual(h.streak(), 3)

    def test_weekly_streak(self):
        # Test streak calculation for weekly habits
        h = Habit("Weekly Habit", "weekly")
        h.completions = [
            datetime(2024, 1, 1),
            datetime(2024, 1, 8),
            datetime(2024, 1, 15)
        ]
        self.assertEqual(h.streak(), 3)

    def test_invalid_periodicity(self):
        # Test that invalid periodicity raises an error
        with self.assertRaises(ValueError):
            Habit("Bad Habit", "monthly")

if __name__ == "__main__":
    unittest.main()



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

list_habits(habits): Returns names of all habits.

habits_by_periodicity(habits, periodicity): Filters habits by frequency.

longest_streak(habits): Returns the longest streak across all habits.

longest_streak_per_habit(habits): Returns streaks per habit in dictionary form.


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




