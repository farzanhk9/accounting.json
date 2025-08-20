import os
import json
from datetime import datetime

DATA_FILE = "accounting.json"

def load_data():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    return {}

def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=4)

def add_entry(data, entry_type):
    month = datetime.today().strftime("%Y-%m")
    if month not in data:
        data[month] = {"income": [], "expenses": []}

    description = input("Description: ")
    category = input("Category (e.g., Food, Rent, Salary, Misc): ")
    try:
        amount = float(input("Amount: "))
    except ValueError:
        print("⚠️ Invalid amount.")
        return

    entry = {"description": description, "category": category, "amount": amount}

    if entry_type == "income":
        data[month]["income"].append(entry)
    else:
        data[month]["expenses"].append(entry)

    save_data(data)
    print(f"✅ {entry_type.capitalize()} added!")

def view_summary(data):
    month = datetime.today().strftime("%Y-%m")
    if month not in data:
        print("📭📭 No records for this month.")
        return

    income_total = sum(e["amount"] for e in data[month]["income"])
    expense_total = sum(e["amount"] for e in data[month]["expenses"])
    balance = income_total - expense_total

    print(f"\n📅📅 Summary for {month}")
    print(f"💰 Total Income: {income_total:.2f}")
    print(f"💸 Total Expenses: {expense_total:.2f}")
    print(f"📊 Balance: {balance:.2f}")

    # Show by category
    categories = {}
    for e in data[month]["expenses"]:
        categories[e["category"]] = categories.get(e["category"], 0) + e["amount"]

    if categories:
        print("\n📂 Expenses by Category:")
        for cat, amt in categories.items():
            print(f"   - {cat}: {amt:.2f}")

def menu():
    data = load_data()
    while True:
        print("\n==== MONTHLY ACCOUNTING ====")
        print("1. Add Income")
        print("2. Add Expense")
        print("3. View Monthly Summary")
        print("4. Exit")
        choice = input("Choose an option: ")

        if choice == "1":
            add_entry(data, "income")
        elif choice == "2":
            add_entry(data, "expenses")
        elif choice == "3":
            view_summary(data)
        elif choice == "4":
            break
        else:
            print("⚠️ Invalid choice.")

if __name__ == "__main__":
    menu()
