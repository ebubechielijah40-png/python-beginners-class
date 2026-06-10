# 🎓 SQLite3 Database Guide for Beginners

Learn how to create and manage databases using Python's `sqlite3` module! This guide includes everything you need to get started, with exercises to practice.

---

## 📋 Table of Contents

1. [Setup Instructions](#setup-instructions)
2. [Core Concepts](#core-concepts)
3. [Code Examples](#code-examples)
4. [Exercises](#exercises)
5. [Troubleshooting](#troubleshooting)

---

## 🔧 Setup Instructions

### Step 1: Install Python
Make sure Python 3.x is installed on your computer.
- Download from: https://www.python.org/downloads/
- During installation, **check the box that says "Add Python to PATH"**

### Step 2: Create a Project Folder
Open your terminal/command prompt and run:
```bash
mkdir my_database_project
cd my_database_project
```

### Step 3: Create a Python File
Create a new file called `database.py` in your project folder.

### Step 4: Open in VS Code
```bash
code .
```

### Step 5: Verify sqlite3 is Available
`sqlite3` comes built-in with Python, so no installation needed! But let's verify it works. Create a test file `test_sqlite.py`:

```python
import sqlite3

try:
    conn = sqlite3.connect(":memory:")
    print("✅ sqlite3 is working correctly!")
    conn.close()
except Exception as e:
    print(f"❌ Error: {e}")
```

Run it in VS Code terminal:
```bash
python test_sqlite.py
```

If you see "✅ sqlite3 is working correctly!" — you're ready to go!

---

## 🧠 Core Concepts

### What is a Database?
A **database** is like an organized notebook where the computer stores information in a structured way.

### Key Terms:

| Term | Explanation | Real-Life Example |
|------|-------------|-------------------|
| **Database** | A file that stores organized data | Your address book |
| **Table** | A chart with rows and columns | A list of contacts with columns for Name, Phone, Email |
| **Row** | One complete record | One person's information |
| **Column** | A type of information | Name column, Email column, etc. |
| **Primary Key** | A unique ID for each row | Student ID number |
| **Cursor** | The tool to execute commands | Your pencil for writing |
| **Commit** | Saving changes permanently | Saving your notebook |

---

## 💻 Code Examples

### Example 1: Creating Your First Database

Create a file called `example1_create_database.py`:

```python
"""
Example 1: Creating a Database and Table
This example shows how to create a database file and a table.
"""

import sqlite3
import os

# Step 1: Define the database file name
DB_FILE = "students.db"

# Step 2: Remove old database if it exists (for a fresh start)
if os.path.exists(DB_FILE):
    os.remove(DB_FILE)
    print(f"🗑️  Removed old database: {DB_FILE}")

# Step 3: Connect to the database
# If the file doesn't exist, SQLite creates it automatically
try:
    conn = sqlite3.connect(DB_FILE)
    print(f"✅ Connected to database: {DB_FILE}")
except sqlite3.Error as e:
    print(f"❌ Error connecting: {e}")
    exit()

# Step 4: Create a cursor (your "pencil" for writing SQL)
cursor = conn.cursor()

# Step 5: Create a table
try:
    create_table_query = """
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        age INTEGER NOT NULL,
        grade TEXT NOT NULL
    );
    """
    cursor.execute(create_table_query)
    conn.commit()
    print("✅ Table 'students' created successfully!")
    
except sqlite3.Error as e:
    print(f"❌ Error creating table: {e}")

# Step 6: Close the connection
conn.close()
print("✅ Connection closed. Database is ready!")
```

**Run it:**
```bash
python example1_create_database.py
```

You should see a file called `students.db` appear in your folder! 🎉

---

### Example 2: Adding Data (INSERT)

Create a file called `example2_insert_data.py`:

```python
"""
Example 2: Inserting Data into a Database
This example shows how to add records to your table.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("📝 Adding students to the database...\n")
    
    # Method 1: Insert a single record
    cursor.execute(
        "INSERT INTO students (name, age, grade) VALUES (?, ?, ?)",
        ("Alice Johnson", 14, "A")
    )
    print("✅ Added: Alice Johnson")
    
    # Method 2: Insert multiple records at once
    students = [
        ("Bob Smith", 15, "B"),
        ("Charlie Brown", 14, "A"),
        ("Diana Prince", 16, "A+"),
        ("Ethan Hunt", 15, "C")
    ]
    
    cursor.executemany(
        "INSERT INTO students (name, age, grade) VALUES (?, ?, ?)",
        students
    )
    print("✅ Added: Bob, Charlie, Diana, and Ethan")
    
    # IMPORTANT: Save the changes!
    conn.commit()
    print("\n✅ All changes saved to database!")
    
except sqlite3.Error as e:
    print(f"❌ Error inserting data: {e}")
finally:
    conn.close()
    print("✅ Connection closed.")
```

**Run it:**
```bash
python example2_insert_data.py
```

---

### Example 3: Reading Data (SELECT)

Create a file called `example3_read_data.py`:

```python
"""
Example 3: Reading Data from Database
This example shows different ways to retrieve data.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # --- Method A: Get ALL records ---
    print("📖 METHOD A: Fetch All Records\n")
    cursor.execute("SELECT * FROM students")
    all_students = cursor.fetchall()
    
    print(f"Total students: {len(all_students)}\n")
    for student in all_students:
        print(f"  ID: {student[0]}")
        print(f"  Name: {student[1]}")
        print(f"  Age: {student[2]}")
        print(f"  Grade: {student[3]}")
        print("  " + "-" * 30)
    
    # --- Method B: Get ONE specific record ---
    print("\n📖 METHOD B: Find One Student by Name\n")
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Alice Johnson",))
    alice = cursor.fetchone()
    
    if alice:
        print(f"Found: {alice[1]} (Age: {alice[2]}, Grade: {alice[3]})")
    else:
        print("Student not found!")
    
    # --- Method C: Get records matching a condition ---
    print("\n📖 METHOD C: Find All Grade A Students\n")
    cursor.execute("SELECT name, grade FROM students WHERE grade = ?", ("A",))
    grade_a_students = cursor.fetchall()
    
    print(f"Students with Grade A:")
    for student in grade_a_students:
        print(f"  - {student[0]}: {student[1]}")
    
    # --- Method D: Count records ---
    print("\n📖 METHOD D: Count Records\n")
    cursor.execute("SELECT COUNT(*) FROM students")
    count = cursor.fetchone()[0]
    print(f"Total number of students: {count}")
    
except sqlite3.Error as e:
    print(f"❌ Error reading data: {e}")
finally:
    conn.close()
    print("\n✅ Connection closed.")
```

**Run it:**
```bash
python example3_read_data.py
```

---

### Example 4: Updating Data (UPDATE)

Create a file called `example4_update_data.py`:

```python
"""
Example 4: Updating Existing Records
This example shows how to modify data in your database.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("✏️  Updating student records...\n")
    
    # Before update: Show Charlie's current grade
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Charlie Brown",))
    before = cursor.fetchone()
    print(f"Before: {before[1]} has grade {before[3]}")
    
    # Update Charlie's grade
    cursor.execute(
        "UPDATE students SET grade = ? WHERE name = ?",
        ("A+", "Charlie Brown")
    )
    conn.commit()
    
    # After update: Show Charlie's new grade
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Charlie Brown",))
    after = cursor.fetchone()
    print(f"After:  {after[1]} has grade {after[3]}")
    
    print("\n✅ Update successful!")
    
except sqlite3.Error as e:
    print(f"❌ Error updating data: {e}")
finally:
    conn.close()
```

**Run it:**
```bash
python example4_update_data.py
```

---

### Example 5: Deleting Data (DELETE)

Create a file called `example5_delete_data.py`:

```python
"""
Example 5: Deleting Records
This example shows how to remove data from your database.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("🗑️  Deleting student record...\n")
    
    # Before deletion: Count students
    cursor.execute("SELECT COUNT(*) FROM students")
    before_count = cursor.fetchone()[0]
    print(f"Students before deletion: {before_count}")
    
    # Delete Ethan's record
    cursor.execute("DELETE FROM students WHERE name = ?", ("Ethan Hunt",))
    conn.commit()
    print("✅ Deleted: Ethan Hunt")
    
    # After deletion: Count students
    cursor.execute("SELECT COUNT(*) FROM students")
    after_count = cursor.fetchone()[0]
    print(f"Students after deletion: {after_count}")
    
except sqlite3.Error as e:
    print(f"❌ Error deleting data: {e}")
finally:
    conn.close()
```

**Run it:**
```bash
python example5_delete_data.py
```

---

## 🎯 Exercises

### Exercise 1: Create Your Own Database - Library Books

**Objective:** Create a database to store information about books in a library.

**What you need to do:**
1. Create a new file called `exercise1_library.py`
2. Create a database called `library.db`
3. Create a table called `books` with these columns:
   - `id` (INTEGER PRIMARY KEY)
   - `title` (TEXT, required)
   - `author` (TEXT, required)
   - `year_published` (INTEGER)
   - `copies_available` (INTEGER, required)

**Starter code:**
```python
import sqlite3
import os

DB_FILE = "library.db"

# Remove old database for fresh start
if os.path.exists(DB_FILE):
    os.remove(DB_FILE)

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # CREATE TABLE HERE
    
    conn.commit()
    print("✅ Library database created!")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

**Solution:** See `solutions/exercise1_solution.py`

---

### Exercise 2: Add Books to Your Library

**Objective:** Insert book records into your library database.

**What you need to do:**
1. Create a file called `exercise2_add_books.py`
2. Connect to `library.db`
3. Add at least 5 books using INSERT:
   - "The Great Gatsby" by F. Scott Fitzgerald (1925, 3 copies)
   - "To Kill a Mockingbird" by Harper Lee (1960, 2 copies)
   - And 3 more books of your choice!

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("📚 Adding books to the library...\n")
    
    books = [
        # Add your books here!
    ]
    
    cursor.executemany(
        "INSERT INTO books (title, author, year_published, copies_available) VALUES (?, ?, ?, ?)",
        books
    )
    
    conn.commit()
    print("✅ Books added successfully!")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

**Hint:** Use a tuple for each book like: `("Book Title", "Author Name", 2020, 5)`

---

### Exercise 3: Find Books by Author

**Objective:** Read data and search for books by a specific author.

**What you need to do:**
1. Create a file called `exercise3_search_books.py`
2. Write a program that:
   - Asks the user: "Which author do you want to find?"
   - Searches the database for books by that author
   - Displays all matching books

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # Get author name from user
    author = input("Which author do you want to find? ")
    
    # Search database here
    
    print("\n📚 Books found:")
    # Display results here
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 4: Update Book Copies

**Objective:** Update the number of available copies when someone borrows a book.

**What you need to do:**
1. Create a file called `exercise4_borrow_book.py`
2. Write a program that:
   - Asks the user: "Which book do you want to borrow?"
   - Finds the book and reduces `copies_available` by 1
   - Shows the updated number of copies

**Challenge:** Make sure you can't borrow a book if there are 0 copies left!

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    book_title = input("Which book do you want to borrow? ")
    
    # Check if book exists and has copies available
    # Update the database
    # Show confirmation
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 5: Remove Old Books

**Objective:** Delete books from the database.

**What you need to do:**
1. Create a file called `exercise5_remove_book.py`
2. Write a program that:
   - Asks the user: "Which book should be removed?"
   - Deletes that book from the database
   - Confirms the deletion and shows remaining books count

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    book_to_remove = input("Which book should be removed? ")
    
    # Delete the book
    # Show confirmation
    # Count remaining books
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

## 🎓 Complete Working Example

Here's a full program that combines everything:

Create `complete_example.py`:

```python
"""
Complete SQLite3 Example: Student Management System
This program demonstrates all CRUD operations (Create, Read, Update, Delete)
"""

import sqlite3
import os

DB_FILE = "student_management.db"

def create_database():
    """Create the database and table"""
    if os.path.exists(DB_FILE):
        os.remove(DB_FILE)
    
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("""
    CREATE TABLE students (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        email TEXT NOT NULL UNIQUE,
        grade TEXT NOT NULL,
        attendance INTEGER DEFAULT 0
    )
    """)
    
    conn.commit()
    conn.close()
    print("✅ Database created!")

def add_student(name, email, grade):
    """Add a new student"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    try:
        cursor.execute(
            "INSERT INTO students (name, email, grade) VALUES (?, ?, ?)",
            (name, email, grade)
        )
        conn.commit()
        print(f"✅ Added student: {name}")
    except sqlite3.IntegrityError:
        print(f"❌ Student with email {email} already exists!")
    finally:
        conn.close()

def view_all_students():
    """Display all students"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("SELECT * FROM students")
    students = cursor.fetchall()
    
    print("\n📖 All Students:")
    print("-" * 60)
    for student in students:
        print(f"ID: {student[0]}, Name: {student[1]}, Email: {student[2]}, Grade: {student[3]}")
    print("-" * 60)
    
    conn.close()

def update_student_grade(student_id, new_grade):
    """Update a student's grade"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("UPDATE students SET grade = ? WHERE id = ?", (new_grade, student_id))
    conn.commit()
    print(f"✅ Updated student {student_id} to grade {new_grade}")
    
    conn.close()

def delete_student(student_id):
    """Delete a student"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("DELETE FROM students WHERE id = ?", (student_id,))
    conn.commit()
    print(f"✅ Deleted student {student_id}")
    
    conn.close()

# Run the program
if __name__ == "__main__":
    print("🎓 Student Management System\n")
    
    create_database()
    
    # Add some students
    add_student("Alice", "alice@school.com", "A")
    add_student("Bob", "bob@school.com", "B")
    add_student("Charlie", "charlie@school.com", "A")
    
    # View all students
    view_all_students()
    
    # Update a grade
    update_student_grade(1, "A+")
    
    # View again
    view_all_students()
    
    # Delete a student
    delete_student(3)
    
    # View final result
    view_all_students()
```

**Run it:**
```bash
python complete_example.py
```

---

## 🐛 Troubleshooting

### Problem 1: "ModuleNotFoundError: No module named 'sqlite3'"
**Solution:** `sqlite3` comes with Python by default. Make sure Python is properly installed and you're using Python 3.x.

### Problem 2: "database is locked"
**Solution:** Make sure all previous connections are closed before running your script. Check for any open database connections.

### Problem 3: "UNIQUE constraint failed"
**Solution:** You're trying to insert a duplicate value in a column marked as UNIQUE. Check your data first!

```python
# Good practice: Check before inserting
cursor.execute("SELECT * FROM students WHERE email = ?", (email,))
if cursor.fetchone() is None:
    # Safe to insert
    cursor.execute("INSERT INTO students VALUES (...)")
```

### Problem 4: "no such table"
**Solution:** The table doesn't exist. Make sure you created it first!

### Problem 5: "syntax error"
**Solution:** Check your SQL syntax. Common mistakes:
- Missing commas between columns
- Misspelled keywords (SELECT, INSERT, UPDATE, DELETE)
- Missing parentheses

---

## 📚 Quick Reference

### Connection
```python
conn = sqlite3.connect("database.db")  # Create/connect
cursor = conn.cursor()                  # Create cursor
conn.commit()                           # Save changes
conn.close()                            # Close connection
```

### Create Table
```python
cursor.execute("""
CREATE TABLE table_name (
    id INTEGER PRIMARY KEY,
    column1 TEXT NOT NULL,
    column2 INTEGER
)
""")
```

### Insert
```python
cursor.execute("INSERT INTO table_name (col1, col2) VALUES (?, ?)", (value1, value2))
cursor.executemany("INSERT INTO table_name VALUES (?, ?)", list_of_tuples)
```

### Select
```python
cursor.execute("SELECT * FROM table_name")
all_rows = cursor.fetchall()
one_row = cursor.fetchone()
```

### Update
```python
cursor.execute("UPDATE table_name SET column = ? WHERE id = ?", (new_value, id))
```

### Delete
```python
cursor.execute("DELETE FROM table_name WHERE id = ?", (id,))
```

---

## 🎉 Next Steps

After completing these exercises, you can:
1. Add more complex features (like user authentication)
2. Learn about SQL joins (combining multiple tables)
3. Explore databases with GUI tools like DB Browser for SQLite
4. Learn about database design best practices
5. Move to more advanced databases like PostgreSQL or MySQL

---

**Happy coding! 🚀**
