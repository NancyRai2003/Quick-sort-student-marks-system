# Quick-sort-student-marks-system
import mysql.connector
from tkinter import *
from tkinter import ttk

# ✅ Connect to MySQL database
conn = mysql.connector.connect(
    host="localhost",
    user="root",
    password="2003",   # <-- replace with your MySQL password
    database="quicksort_db"
)
cursor = conn.cursor()

# ---------- Quick Sort Algorithm ----------
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    else:
        pivot = arr[len(arr)//2]
        left = [x for x in arr if x < pivot]
        middle = [x for x in arr if x == pivot]
        right = [x for x in arr if x > pivot]
        return quick_sort(left) + middle + quick_sort(right)

# ---------- Tkinter Window ----------
root = Tk()
root.title("Quick Sort - Student Marks Sorting System")
root.geometry("550x500")
root.config(bg="#e8f4fc")

Label(root, text="🎓 Quick Sort Student Marks System", 
      font=("Helvetica", 18, "bold"), bg="#e8f4fc", fg="#004080").pack(pady=15)

# ---------- TreeView Table ----------
columns = ("id", "name", "marks")
tree = ttk.Treeview(root, columns=columns, show="headings")
tree.heading("id", text="ID")
tree.heading("name", text="Student Name")
tree.heading("marks", text="Marks")
tree.pack(pady=10, fill=BOTH, expand=True)

# ---------- Function to Load Data ----------
def load_data():
    cursor.execute("SELECT * FROM Students")
    rows = cursor.fetchall()
    tree.delete(*tree.get_children())
    for row in rows:
        tree.insert("", END, values=row)

# ---------- Function to Sort Marks ----------
def sort_data():
    cursor.execute("SELECT Marks FROM Students")
    marks = [row[0] for row in cursor.fetchall()]
    sorted_marks = quick_sort(marks)

    # Get sorted data
    query = "SELECT * FROM Students ORDER BY Marks ASC"
    cursor.execute(query)
    sorted_rows = cursor.fetchall()

    tree.delete(*tree.get_children())
    for row in sorted_rows:
        tree.insert("", END, values=row)

# ---------- Buttons ----------
btn_frame = Frame(root, bg="#e8f4fc")
btn_frame.pack(pady=15)

Button(btn_frame, text="Show All Data", command=load_data, bg="#007acc", fg="white", 
       font=("Arial", 12, "bold"), width=15).grid(row=0, column=0, padx=10)
Button(btn_frame, text="Apply Quick Sort", command=sort_data, bg="#00b300", fg="white", 
       font=("Arial", 12, "bold"), width=15).grid(row=0, column=1, padx=10)

# ---------- Footer ----------
Label(root, text="Project: Quick Sort Algorithm (DAA)", 
      bg="#e8f4fc", font=("Arial", 10, "italic")).pack(side="bottom", pady=10)

root.mainloop()
