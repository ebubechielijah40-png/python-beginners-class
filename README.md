# 🎓 SQLite3 Database Guide for Beginners

Learn how to create and manage databases using Python's `sqlite3` module! This guide teaches you both the **what** (syntax) and the **why** (semantics) behind every line of code.

---

## 📋 Table of Contents

1. [Setup Instructions](#setup-instructions)
2. [Understanding Databases](#understanding-databases)
3. [Core Concepts](#core-concepts)
4. [Code Examples with Detailed Explanations](#code-examples-with-detailed-explanations)
5. [Exercises](#exercises)
6. [Troubleshooting](#troubleshooting)

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

### Step 3: Open in VS Code
```bash
code .
```

### Step 4: Verify sqlite3 is Available
`sqlite3` comes built-in with Python, so no installation needed! Create a test file `test_sqlite.py`:

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

---

## 🧠 Understanding Databases

### What is a Database?

A **database** is an organized collection of data stored in a computer. Think of it like a filing cabinet:
- The **cabinet** = the database file (`.db`)
- **Drawers** = tables (collections of related data)
- **Files in drawers** = rows (individual records)
- **Information in files** = columns (specific data types)

### Why Use Databases?

Instead of storing data in random text files or lists in memory, databases allow you to:
1. **Organize** data in a structured way
2. **Search** through large amounts of data quickly
3. **Update** information without losing other data
4. **Protect** data with constraints and relationships
5. **Scale** to millions of records efficiently

---

## 🧠 Core Concepts

### 1. **Database Connection**
A connection is your **gateway** to the database. It's like opening a door to your filing cabinet.

```python
conn = sqlite3.connect("students.db")
```
- **Why:** Before you can read, write, or modify data, you need to establish a connection
- **What happens:** If the file doesn't exist, SQLite creates it; if it does exist, it opens it
- **Semantics:** This is similar to opening a file on your computer - you establish access

### 2. **Cursor**
A cursor is your **tool** for executing SQL commands. It's like a pencil for writing in your notebook.

```python
cursor = conn.cursor()
```
- **Why:** You can't execute SQL directly; you need an intermediary object
- **What happens:** A cursor object is created that can send commands to the database
- **Semantics:** It acts as a "middleman" between Python and SQLite

### 3. **SQL (Structured Query Language)**
SQL is the **language** used to communicate with databases. It's specifically designed for data operations.

### 4. **Tables**
Tables are **structured collections** of data organized in rows and columns.

```python
CREATE TABLE students (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER NOT NULL,
    grade TEXT NOT NULL
);
```
- **Why:** Organizing data in a table format makes it easy to query and manage
- **Semantics:** Each column defines what TYPE of data is stored, and each row is one complete record

### 5. **CRUD Operations**
- **C**reate: INSERT new data
- **R**ead: SELECT existing data
- **U**pdate: UPDATE existing data
- **D**elete: DELETE existing data

### 6. **Commit**
Saving changes permanently to the database file.

```python
conn.commit()
```
- **Why:** Without committing, changes exist only in memory; if the program crashes, they're lost
- **Semantics:** It's like pressing "Save" in a word processor - without it, your changes are temporary

---

## 💻 Code Examples with Detailed Explanations

### Example 1: Creating Your First Database

Create a file called `example1_create_database.py`:

```python
"""
Example 1: Creating a Database and Table
This example shows the fundamental process of database creation.
"""

import sqlite3
import os

# ===== STEP 1: Define Database File =====
DB_FILE = "students.db"

# Why: We use a variable so we can reference the same filename throughout the program
# Semantics: This is the location where SQLite will create/store the database file

# ===== STEP 2: Remove Old Database (Optional but Recommended) =====
if os.path.exists(DB_FILE):
    os.remove(DB_FILE)
    print(f"🗑️  Removed old database: {DB_FILE}")

# Why: Starting fresh ensures we're not confused by old test data
# Semantics: Checks if file exists, then deletes it to start with a clean slate

# ===== STEP 3: Connect to the Database =====
try:
    conn = sqlite3.connect(DB_FILE)
    print(f"✅ Connected to database: {DB_FILE}")
except sqlite3.Error as e:
    print(f"❌ Error connecting: {e}")
    exit()

# Why: The try-except block handles errors gracefully
# Semantics: If connection fails, we print the error and stop execution
# - sqlite3.connect() opens/creates the database file
# - The 'conn' object represents our connection to the database

# ===== STEP 4: Create a Cursor =====
cursor = conn.cursor()

# Why: The cursor is our tool to execute SQL commands
# Semantics: Think of it as the "pen" that writes SQL commands to the database

# ===== STEP 5: Create a Table =====
try:
    create_table_query = """
    CREATE TABLE IF NOT EXISTS students (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        age INTEGER NOT NULL,
        grade TEXT NOT NULL
    );
    """
    
    # Breaking down the SQL statement:
    # CREATE TABLE - Tell SQLite to create a new table
    # IF NOT EXISTS - Safety check: only create if it doesn't already exist
    # students - The name of the table
    # (columns) - Inside parentheses, we define each column:
    #   - id INTEGER PRIMARY KEY: A unique number for each student (PRIMARY KEY makes it unique)
    #   - name TEXT NOT NULL: Text data that MUST have a value (can't be empty)
    #   - age INTEGER NOT NULL: Whole number that MUST have a value
    #   - grade TEXT NOT NULL: Text data that MUST have a value
    
    cursor.execute(create_table_query)
    
    # Why cursor.execute(): Sends the SQL command to the database
    # Semantics: The cursor actually runs the command against the database
    
    conn.commit()
    
    # Why commit: Writes the table creation to the database file permanently
    # Semantics: Without commit(), the table only exists in memory temporarily
    
    print("✅ Table 'students' created successfully!")
    
except sqlite3.Error as e:
    print(f"❌ Error creating table: {e}")

# ===== STEP 6: Close the Connection =====
conn.close()
print("✅ Connection closed. Database is ready!")

# Why close: Releases resources and ensures data is flushed to disk
# Semantics: Similar to closing a file - prevents memory leaks and data corruption
```

**Key Learning Points:**
- **NOT NULL**: Ensures a column always has a value (prevents empty entries)
- **PRIMARY KEY**: Makes each id unique (no two students can have the same id)
- **IF NOT EXISTS**: Prevents errors if the table already exists
- **Commit vs Execute**: Execute sends the command, commit saves it permanently

**Run it:**
```bash
python example1_create_database.py
```

---

### Example 2: Adding Data (INSERT)

Create a file called `example2_insert_data.py`:

```python
"""
Example 2: Inserting Data into a Database
This example shows how to add new records to your table.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("📝 Adding students to the database...\n")
    
    # ===== METHOD 1: Insert a Single Record =====
    cursor.execute(
        "INSERT INTO students (name, age, grade) VALUES (?, ?, ?)",
        ("Alice Johnson", 14, "A")
    )
    print("✅ Added: Alice Johnson")
    
    # Why this syntax:
    # - INSERT INTO students: Tell SQLite we're adding data to the students table
    # - (name, age, grade): Specify which columns we're filling (we skip 'id' because it auto-increments)
    # - VALUES (?, ?, ?): The ? are placeholders for the actual data
    # - ("Alice Johnson", 14, "A"): The tuple provides the actual values
    
    # Why use placeholders (?):
    # Security: Prevents "SQL injection" - a way hackers try to break databases
    # Safety: Automatically escapes special characters
    # Semantics: The database engine handles data safely for us
    
    # ===== METHOD 2: Insert Multiple Records at Once =====
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
    
    # Why executemany() instead of execute():
    # - executemany() is optimized for adding multiple rows at once
    # - It's faster than calling execute() five times
    # - Semantics: One bulk operation is more efficient than many individual operations
    
    # ===== IMPORTANT: Commit the Changes =====
    conn.commit()
    print("\n✅ All changes saved to database!")
    
    # Why commit after all inserts:
    # - Groups multiple operations into one transaction
    # - If any insert fails, NONE of them are saved (atomicity)
    # - Semantics: Either all succeed or all fail - no partial updates
    
except sqlite3.Error as e:
    print(f"❌ Error inserting data: {e}")
finally:
    conn.close()
    print("✅ Connection closed.")

# Why use finally:
# - finally block runs regardless of success or error
# - Ensures the connection is ALWAYS closed
# - Prevents resource leaks
```

**Key Learning Points:**
- **?** placeholders protect against SQL injection
- **executemany()** is more efficient than multiple execute() calls
- **Commit after all operations** ensures atomic transactions (all or nothing)
- **Finally block** ensures cleanup happens regardless of errors

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
This example shows different ways to retrieve and display data.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # ===== METHOD A: Fetch ALL Records =====
    print("📖 METHOD A: Fetch All Records\n")
    
    cursor.execute("SELECT * FROM students")
    # Why SELECT *:
    # - SELECT: Retrieve data
    # - *: All columns (shorthand for name, age, grade, id)
    # - Semantics: Tells database to grab everything from the table
    
    all_students = cursor.fetchall()
    # Why fetchall():
    # - Retrieves ALL rows from the last SELECT query
    # - Returns a list of tuples: [(row1), (row2), (row3), ...]
    # - Semantics: Reads all results from the query into memory
    
    print(f"Total students: {len(all_students)}\n")
    for student in all_students:
        # Each student is a tuple: (id, name, age, grade)
        print(f"  ID: {student[0]}")      # Index 0 = id
        print(f"  Name: {student[1]}")    # Index 1 = name
        print(f"  Age: {student[2]}")     # Index 2 = age
        print(f"  Grade: {student[3]}")   # Index 3 = grade
        print("  " + "-" * 30)
    
    # ===== METHOD B: Get ONE Specific Record =====
    print("\n📖 METHOD B: Find One Student by Name\n")
    
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Alice Johnson",))
    # Why WHERE name = ?:
    # - WHERE: Filter results based on a condition
    # - name = ?: Only return rows where the name matches the placeholder
    # - Semantics: Narrows down results to only what we want
    
    alice = cursor.fetchone()
    # Why fetchone():
    # - Retrieves ONLY the first matching row
    # - Returns a single tuple or None if no match found
    # - Semantics: Efficient when you only need one result
    
    if alice:
        print(f"Found: {alice[1]} (Age: {alice[2]}, Grade: {alice[3]})")
    else:
        print("Student not found!")
    
    # ===== METHOD C: Get Records Matching a Condition =====
    print("\n📖 METHOD C: Find All Grade A Students\n")
    
    cursor.execute("SELECT name, grade FROM students WHERE grade = ?", ("A",))
    # Why SELECT name, grade (not *):
    # - We only need specific columns, not all of them
    # - Semantics: Reduces data transferred and processed
    # - More efficient than SELECT * when you only need certain columns
    
    grade_a_students = cursor.fetchall()
    
    print(f"Students with Grade A:")
    for student in grade_a_students:
        print(f"  - {student[0]}: {student[1]}")
    
    # ===== METHOD D: Count Records =====
    print("\n📖 METHOD D: Count Records\n")
    
    cursor.execute("SELECT COUNT(*) FROM students")
    # Why COUNT(*):
    # - Counts the number of rows in the table
    # - Much faster than fetching all rows and using len()
    # - Semantics: Database does the counting, not Python
    
    count = cursor.fetchone()[0]
    # [0] gets the first (and only) value from the tuple returned
    
    print(f"Total number of students: {count}")
    
except sqlite3.Error as e:
    print(f"❌ Error reading data: {e}")
finally:
    conn.close()
    print("\n✅ Connection closed.")
```

**Key Learning Points:**
- **SELECT * vs SELECT columns**: Choose specific columns when possible for efficiency
- **WHERE clause**: Filters results before returning them
- **fetchall()**: Returns all results as a list of tuples
- **fetchone()**: Returns only the first result (more efficient for single lookups)
- **COUNT(*)**: Let the database count, not Python
- **Accessing tuple data**: Use index numbers [0], [1], etc.

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
This example shows how to modify data that already exists in the database.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("✏️  Updating student records...\n")
    
    # ===== STEP 1: Show Data Before Update =====
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Charlie Brown",))
    before = cursor.fetchone()
    print(f"Before: {before[1]} has grade {before[3]}")
    
    # ===== STEP 2: Update the Record =====
    cursor.execute(
        "UPDATE students SET grade = ? WHERE name = ?",
        ("A+", "Charlie Brown")
    )
    # Why this syntax:
    # - UPDATE students: Tell SQLite which table to modify
    # - SET grade = ?: Change the grade column to a new value
    # - WHERE name = ?: Only update rows where name matches
    # - Semantics: This is a targeted update - only affects matching rows
    
    conn.commit()
    # Why commit after update:
    # - Makes the change permanent in the database file
    # - Semantics: Without commit, the change exists only in memory
    
    # ===== STEP 3: Show Data After Update =====
    cursor.execute("SELECT * FROM students WHERE name = ?", ("Charlie Brown",))
    after = cursor.fetchone()
    print(f"After:  {after[1]} has grade {after[3]}")
    
    print("\n✅ Update successful!")
    
except sqlite3.Error as e:
    print(f"❌ Error updating data: {e}")
finally:
    conn.close()
```

**Key Learning Points:**
- **UPDATE ... SET**: Changes existing data
- **WHERE clause is essential**: Without it, you'd update ALL rows!
- **Commit after updates**: Makes changes permanent
- **Show before/after**: Good practice to verify updates worked

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
This example shows how to remove data from the database.
"""

import sqlite3

DB_FILE = "students.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("🗑️  Deleting student record...\n")
    
    # ===== STEP 1: Count Before Deletion =====
    cursor.execute("SELECT COUNT(*) FROM students")
    before_count = cursor.fetchone()[0]
    print(f"Students before deletion: {before_count}")
    
    # ===== STEP 2: Delete a Record =====
    cursor.execute("DELETE FROM students WHERE name = ?", ("Ethan Hunt",))
    # Why this syntax:
    # - DELETE FROM students: Tell SQLite which table to delete from
    # - WHERE name = ?: Only delete rows that match this condition
    # - Semantics: This is permanent - deleted data cannot be recovered
    
    conn.commit()
    # Why commit after delete:
    # - Makes the deletion permanent
    # - Without commit, the deletion is temporary
    
    print("✅ Deleted: Ethan Hunt")
    
    # ===== STEP 3: Count After Deletion =====
    cursor.execute("SELECT COUNT(*) FROM students")
    after_count = cursor.fetchone()[0]
    print(f"Students after deletion: {after_count}")
    
except sqlite3.Error as e:
    print(f"❌ Error deleting data: {e}")
finally:
    conn.close()
```

**Key Learning Points:**
- **DELETE is permanent**: Once you commit, the data is gone
- **WHERE is critical**: Without it, you delete EVERYTHING!
- **Always verify deletions**: Count before/after to confirm
- **Commit makes it permanent**: Change exists in memory until you commit

**Run it:**
```bash
python example5_delete_data.py
```

---

## 🎯 Exercises

### Exercise 1: Create Your Own Database - Library Books

**Objective:** Understand table design by creating a database from scratch.

**What you need to do:**
1. Create a new file called `exercise1_library.py`
2. Create a database called `library.db`
3. Create a table called `books` with these columns:
   - `id` (INTEGER PRIMARY KEY) - Unique identifier for each book
   - `title` (TEXT NOT NULL) - Book title (required)
   - `author` (TEXT NOT NULL) - Author name (required)
   - `year_published` (INTEGER) - Publication year
   - `copies_available` (INTEGER NOT NULL) - How many copies we have (required)

**Why these data types:**
- `INTEGER PRIMARY KEY`: auto-incrementing unique ID
- `TEXT NOT NULL`: Text data that can't be empty
- `INTEGER`: Whole numbers for year and copies
- `NOT NULL`: Ensures critical data is always filled

**Starter code:**
```python
import sqlite3
import os

DB_FILE = "library.db"

# Clean start
if os.path.exists(DB_FILE):
    os.remove(DB_FILE)

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # CREATE TABLE HERE
    # Think about:
    # - What columns do you need?
    # - What data type for each column?
    # - Which columns MUST have values (NOT NULL)?
    
    conn.commit()
    print("✅ Library database created!")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 2: Add Books to Your Library

**Objective:** Practice INSERT operations and understand data validation.

**What you need to do:**
1. Create a file called `exercise2_add_books.py`
2. Connect to `library.db`
3. Add at least 5 books:
   - "The Great Gatsby" by F. Scott Fitzgerald (1925, 3 copies)
   - "To Kill a Mockingbird" by Harper Lee (1960, 2 copies)
   - And 3 more books of your choice!

**Why you're doing this:**
- Practice using `executemany()` for bulk inserts
- Understand how to prepare data before inserting
- Learn to use the `?` placeholder for safety

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    print("📚 Adding books to the library...\n")
    
    # Create a list of book tuples
    # Format: (title, author, year_published, copies_available)
    books = [
        # ("Book Title", "Author Name", year, copies),
    ]
    
    # Use executemany() because we're adding multiple records
    # This is more efficient than calling execute() multiple times
    cursor.executemany(
        "INSERT INTO books (title, author, year_published, copies_available) VALUES (?, ?, ?, ?)",
        books
    )
    
    # Don't forget to commit!
    conn.commit()
    print("✅ Books added successfully!")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 3: Find Books by Author

**Objective:** Master SELECT queries with WHERE clauses.

**What you need to do:**
1. Create a file called `exercise3_search_books.py`
2. Ask the user: "Which author do you want to find?"
3. Search the database for books by that author
4. Display all matching books with their details

**Why this is important:**
- Learn how WHERE filters results
- Understand user input for dynamic queries
- Practice displaying results in a readable format

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # Get input from user
    author = input("Which author do you want to find? ")
    
    # Write a SELECT query with WHERE to find books by this author
    # Remember to use ? placeholder for the author name
    cursor.execute("SELECT * FROM books WHERE author = ?", (author,))
    
    # Fetch all matching books
    books = cursor.fetchall()
    
    if books:
        print(f"\n📚 Books by {author}:")
        for book in books:
            print(f"  Title: {book[1]}")
            print(f"  Year: {book[3]}")
            print(f"  Copies: {book[4]}")
            print("  " + "-" * 40)
    else:
        print(f"No books found by {author}")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 4: Update Book Copies

**Objective:** Practice UPDATE operations and add logic.

**What you need to do:**
1. Create a file called `exercise4_borrow_book.py`
2. Ask the user: "Which book do you want to borrow?"
3. Find the book and reduce `copies_available` by 1
4. Show confirmation with updated number of copies

**Challenge:** Don't let users borrow if copies_available is 0

**Why this is important:**
- Learn UPDATE with WHERE
- Add business logic (can't borrow if no copies)
- Practice error handling

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    book_title = input("Which book do you want to borrow? ")
    
    # Step 1: Check if book exists and has copies
    cursor.execute("SELECT * FROM books WHERE title = ?", (book_title,))
    book = cursor.fetchone()
    
    if book is None:
        print("Book not found!")
    elif book[4] == 0:  # book[4] is copies_available
        print("Sorry, no copies available!")
    else:
        # Step 2: Update the number of copies (decrease by 1)
        # What SQL statement would you use?
        
        conn.commit()
        print(f"✅ You borrowed '{book_title}'!")
        print(f"Copies remaining: {book[4] - 1}")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

### Exercise 5: Remove Old Books

**Objective:** Practice DELETE operations safely.

**What you need to do:**
1. Create a file called `exercise5_remove_book.py`
2. Ask the user: "Which book should be removed?"
3. Delete that book from the database
4. Show confirmation and remaining book count

**Why this is important:**
- Learn DELETE with WHERE
- Practice database cleanup operations
- Understand the finality of DELETE

**Starter code:**
```python
import sqlite3

DB_FILE = "library.db"

try:
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # Count books before deletion
    cursor.execute("SELECT COUNT(*) FROM books")
    before_count = cursor.fetchone()[0]
    
    book_to_remove = input("Which book should be removed? ")
    
    # Step 1: Delete the book
    cursor.execute("DELETE FROM books WHERE title = ?", (book_to_remove,))
    
    # Step 2: Commit the deletion
    
    # Step 3: Count books after deletion
    
    # Step 4: Show results
    print(f"✅ '{book_to_remove}' has been removed!")
    
except sqlite3.Error as e:
    print(f"❌ Error: {e}")
finally:
    conn.close()
```

---

## 🎓 Complete Working Example

Create `complete_example.py` - a full Student Management System:

```python
"""
Complete SQLite3 Example: Student Management System
Demonstrates all CRUD operations (Create, Read, Update, Delete)
"""

import sqlite3
import os

DB_FILE = "student_management.db"

# ===== FUNCTION 1: Create Database =====
def create_database():
    """
    Creates the database and students table.
    
    Why separate functions:
    - Makes code reusable and organized
    - Each function has one responsibility
    - Easier to test and debug
    """
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
    # Why UNIQUE on email:
    # - Prevents duplicate email addresses
    # - Each student has one unique email
    
    # Why DEFAULT 0 on attendance:
    # - New students start with 0 attendance
    # - Saves typing this value every time
    
    conn.commit()
    conn.close()
    print("✅ Database created!")

# ===== FUNCTION 2: Add Student =====
def add_student(name, email, grade):
    """
    Adds a new student to the database.
    
    Parameters:
    - name: Student's name
    - email: Student's email (must be unique)
    - grade: Student's grade
    """
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
        # This error occurs if email already exists (UNIQUE constraint)
        print(f"❌ Error: Email {email} already exists!")
    finally:
        conn.close()

# ===== FUNCTION 3: View All Students =====
def view_all_students():
    """
    Displays all students in a formatted table.
    """
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("SELECT * FROM students")
    students = cursor.fetchall()
    
    print("\n📖 All Students:")
    print("-" * 60)
    for student in students:
        # Tuple structure: (id, name, email, grade, attendance)
        print(f"ID: {student[0]:2} | Name: {student[1]:20} | Grade: {student[3]}")
    print("-" * 60)
    
    conn.close()

# ===== FUNCTION 4: Update Grade =====
def update_student_grade(student_id, new_grade):
    """
    Updates a student's grade by ID.
    """
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("UPDATE students SET grade = ? WHERE id = ?", (new_grade, student_id))
    conn.commit()
    print(f"✅ Updated student {student_id} to grade {new_grade}")
    
    conn.close()

# ===== FUNCTION 5: Delete Student =====
def delete_student(student_id):
    """
    Removes a student from the database.
    """
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute("DELETE FROM students WHERE id = ?", (student_id,))
    conn.commit()
    print(f"✅ Deleted student {student_id}")
    
    conn.close()

# ===== MAIN PROGRAM =====
if __name__ == "__main__":
    print("🎓 Student Management System\n")
    
    # Create fresh database
    create_database()
    
    # Add some students
    add_student("Alice", "alice@school.com", "A")
    add_student("Bob", "bob@school.com", "B")
    add_student("Charlie", "charlie@school.com", "A")
    
    # View all students
    view_all_students()
    
    # Update a grade
    print("\nUpdating Alice's grade...")
    update_student_grade(1, "A+")
    
    # View again
    view_all_students()
    
    # Delete a student
    print("\nDeleting Charlie...")
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
**Why it happens:** Python installation doesn't have sqlite3
**Solution:** Reinstall Python, making sure to check "sqlite3" during installation

### Problem 2: "database is locked"
**Why it happens:** Another process still has the database open
**Solution:** Make sure you're closing connections properly with `conn.close()`

### Problem 3: "UNIQUE constraint failed"
**Why it happens:** Trying to insert a duplicate value in a UNIQUE column
**Solution:** Check if the value already exists before inserting, or update instead of insert

```python
# Good practice: Check before inserting
cursor.execute("SELECT * FROM students WHERE email = ?", (email,))
if cursor.fetchone() is None:
    # Safe to insert - email doesn't exist yet
    cursor.execute("INSERT INTO students ...")
```

### Problem 4: "no such table"
**Why it happens:** Trying to query a table that doesn't exist
**Solution:** Create the table first using CREATE TABLE

### Problem 5: "syntax error"
**Why it happens:** Incorrect SQL syntax
**Common mistakes:**
- Missing commas between column definitions
- Misspelled SQL keywords (SELECT, INSERT, UPDATE, DELETE)
- Missing parentheses around column definitions
- Forgetting semicolon at end of query (though SQLite often doesn't require it)

---

## 📚 Quick Reference

### Connection and Cursor
```python
conn = sqlite3.connect("database.db")  # Create or open database
cursor = conn.cursor()                  # Create a cursor for executing commands
conn.commit()                           # Save all changes permanently
conn.close()                            # Close connection and free resources
```

### Create Table
```python
cursor.execute("""
CREATE TABLE table_name (
    id INTEGER PRIMARY KEY,        -- Unique auto-incrementing ID
    column1 TEXT NOT NULL,         -- Text that can't be empty
    column2 INTEGER,               -- Whole number (can be NULL)
    column3 TEXT UNIQUE,           -- Text that must be unique
    column4 INTEGER DEFAULT 0      -- Number with default value of 0
)
""")
```

### Data Types
- **INTEGER** - Whole numbers (1, 100, -5)
- **TEXT** - Text strings ("Alice", "hello")
- **REAL** - Decimal numbers (3.14, 2.5)
- **BLOB** - Binary data (images, files)
- **NULL** - Empty/no value

### Constraints
- **PRIMARY KEY** - Makes a column unique and indexes it
- **NOT NULL** - Column must always have a value
- **UNIQUE** - Values in column must be different (like email)
- **DEFAULT value** - Sets a default if no value provided
- **CHECK condition** - Validates that data meets a condition

### Insert
```python
# Single row
cursor.execute(
    "INSERT INTO table_name (col1, col2) VALUES (?, ?)", 
    (value1, value2)
)

# Multiple rows
cursor.executemany(
    "INSERT INTO table_name (col1, col2) VALUES (?, ?)", 
    [(val1, val2), (val3, val4)]
)
```

### Select
```python
# All rows and columns
cursor.execute("SELECT * FROM table_name")

# Specific columns
cursor.execute("SELECT col1, col2 FROM table_name")

# With condition
cursor.execute("SELECT * FROM table_name WHERE col1 = ?", (value,))

# Count rows
cursor.execute("SELECT COUNT(*) FROM table_name")

# Get results
all_rows = cursor.fetchall()    # Returns list of tuples
one_row = cursor.fetchone()     # Returns single tuple or None
many_rows = cursor.fetchmany(5) # Returns 5 rows
```

### Update
```python
cursor.execute(
    "UPDATE table_name SET column = ? WHERE id = ?", 
    (new_value, id)
)
```

### Delete
```python
cursor.execute("DELETE FROM table_name WHERE id = ?", (id,))
```

---

## 🎉 Key Takeaways

### Understanding the "Why":

1. **Why databases exist:** Store organized data efficiently and safely
2. **Why SQL:** Designed specifically for data operations
3. **Why cursors:** Needed as an intermediary between Python and SQLite
4. **Why commit:** Makes changes permanent; without it, they're just in memory
5. **Why WHERE clauses:** Filter results before returning them (efficiency)
6. **Why placeholders (?):** Prevent SQL injection attacks
7. **Why PRIMARY KEY:** Ensures each record is unique and can be found quickly
8. **Why NOT NULL:** Guarantees critical columns always have values
9. **Why transactions:** Ensures all-or-nothing consistency
10. **Why close connections:** Prevents resource leaks and data corruption

---

## 🚀 Next Steps

After mastering this guide:
1. Learn about **SQL JOINs** - combining data from multiple tables
2. Explore **indexes** - making searches faster
3. Study **database design** - designing efficient databases
4. Try **data analysis** - using databases with Pandas
5. Learn other databases like **PostgreSQL** or **MySQL**

---

**Happy learning! 🎓✨**
