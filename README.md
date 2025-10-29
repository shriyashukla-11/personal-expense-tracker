# personal-expense-tracker
A simple Flask + SQLite web app to track personal expenses.



import sqlite3
from datetime import datetime

conn = sqlite3.connect("expenses.db")
cursor = conn.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS expenses (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    category TEXT,
    amount REAL NOT NULL,
    date TEXT NOT NULL
)
""")
conn.commit()

def add_expense():
    name = input("Enter expense name: ")
    category = input("Enter category (Food, Travel, etc): ")
    amount = float(input("Enter amount: "))
    date = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    cursor.execute("INSERT INTO expenses (name, category, amount, date) VALUES (?, ?, ?, ?)",
                   (name, category, amount, date))
    conn.commit()
    print("✅ Expense added successfully!\n")


def view_expenses():
    cursor.execute("SELECT * FROM expenses")
    rows = cursor.fetchall()

    print("\n------ All Expenses ------")
    if not rows:
        print("No expenses found.")
    else:
        for row in rows:
            print(f"ID: {row[0]} | {row[1]} | {row[2]} | ₹{row[3]} | {row[4]}")
    print("---------------------------\n")


def total_expenses():
    cursor.execute("SELECT SUM(amount) FROM expenses")
    total = cursor.fetchone()[0]
    print(f"\n💰 Total Spending: ₹{total if total else 0}\n")


def delete_expense():
    view_expenses()
    expense_id = input("Enter the ID of expense to delete: ")
    cursor.execute("DELETE FROM expenses WHERE id=?", (expense_id,))
    conn.commit()
    print("🗑️ Expense deleted successfully!\n")


def search_by_category():
    category = input("Enter category to search: ")
    cursor.execute("SELECT * FROM expenses WHERE category LIKE ?", ('%' + category + '%',))
    rows = cursor.fetchall()

    print(f"\nExpenses in category '{category}':")
    for row in rows:
        print(f"ID: {row[0]} | {row[1]} | ₹{row[3]} | {row[4]}")
    print()

def main():
    while True:
        print("==== Personal Expense Tracker ====")
        print("1. Add Expense")
        print("2. View All Expenses")
        print("3. View Total Spending")
        print("4. Delete Expense")
        print("5. Search by Category")
        print("6. Exit")

        choice = input("Enter your choice (1-6): ")

        if choice == "1":
            add_expense()
        elif choice == "2":
            view_expenses()
        elif choice == "3":
            total_expenses()
        elif choice == "4":
            delete_expense()
        elif choice == "5":
            search_by_category()
        elif choice == "6":
            print("👋 Exiting... Goodbye!")
            break
        else:
            print("❌ Invalid choice! Please try again.\n")


if __name__ == "__main__":
    main()
    conn.close()
