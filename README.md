import tkinter as tk
from tkinter import messagebox, simpledialog
import mysql.connector

# Database connection
def connect_db():
    return mysql.connector.connect(
        host="localhost",
        user="root",
        password="Karanp123",
        database="karandb1"
    )

# Create table if not exists
def create_table():
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("""
    CREATE TABLE IF NOT EXISTS employees (
        id INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(100),
        position VARCHAR(100),
        salary FLOAT
    )
    """)
    conn.commit()
    conn.close()

# Add employee
def add_employee():
    name = simpledialog.askstring("Input", "Enter employee name:")
    position = simpledialog.askstring("Input", "Enter employee position:")
    salary = simpledialog.askfloat("Input", "Enter employee salary:")
    
    if name and position and salary:
        conn = connect_db()
        cursor = conn.cursor()
        cursor.execute("INSERT INTO employees (name, position, salary) VALUES (%s, %s, %s)", (name, position, salary))
        conn.commit()
        conn.close()
        messagebox.showinfo("Success", "Employee added successfully!")
    else:
        messagebox.showerror("Error", "All fields are required!")

# Remove employee
def remove_employee():
    emp_id = simpledialog.askinteger("Input", "Enter employee ID to remove:")
    
    if emp_id:
        conn = connect_db()
        cursor = conn.cursor()
        cursor.execute("DELETE FROM employees WHERE id = %s", (emp_id,))
        if cursor.rowcount > 0:
            conn.commit()
            messagebox.showinfo("Success", "Employee removed successfully!")
        else:
            messagebox.showerror("Error", "Employee not found!")
        conn.close()
    else:
        messagebox.showerror("Error", "Employee ID is required!")

# Promote employee
def promote_employee():
    emp_id = simpledialog.askinteger("Input", "Enter employee ID to promote:")
    new_position = simpledialog.askstring("Input", "Enter new position:")
    new_salary = simpledialog.askfloat("Input", "Enter new salary:")
    
    if emp_id and new_position and new_salary:
        conn = connect_db()
        cursor = conn.cursor()
        cursor.execute("UPDATE employees SET position = %s, salary = %s WHERE id = %s", (new_position, new_salary, emp_id))
        if cursor.rowcount > 0:
            conn.commit()
            messagebox.showinfo("Success", "Employee promoted successfully!")
        else:
            messagebox.showerror("Error", "Employee not found!")
        conn.close()
    else:
        messagebox.showerror("Error", "All fields are required!")

# Display employees
def display_employees():
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM employees")
    employees = cursor.fetchall()
    conn.close()
    
    if employees:
        employee_list = "\n".join([f"ID: {emp[0]}, Name: {emp[1]}, Position: {emp[2]}, Salary: {emp[3]}" for emp in employees])
        messagebox.showinfo("Employees", employee_list)
    else:
        messagebox.showinfo("Employees", "No employees found.")

# Main application window
root = tk.Tk()
root.title("Employee Management System")
root.geometry("300x200")

# Create buttons
add_btn = tk.Button(root, text="Add Employee", command=add_employee)
add_btn.pack(fill=tk.X, padx=20, pady=10)

remove_btn = tk.Button(root, text="Remove Employee", command=remove_employee)
remove_btn.pack(fill=tk.X, padx=20, pady=10)

promote_btn = tk.Button(root, text="Promote Employee", command=promote_employee)
promote_btn.pack(fill=tk.X, padx=20, pady=10)

display_btn = tk.Button(root, text="Display Employees", command=display_employees)
display_btn.pack(fill=tk.X, padx=20, pady=10)

# Create the table if it doesn't exist
create_table()

# Start the application
root.mainloop()
