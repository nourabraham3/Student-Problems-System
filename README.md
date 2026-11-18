import sqlite3
import tkinter as tk
from tkinter import simpledialog, messagebox

conn = sqlite3.connect("students.db")
c = conn.cursor()

c.execute("""CREATE TABLE IF NOT EXISTS problems(
id INTEGER PRIMARY KEY AUTOINCREMENT,
name TEXT, class TEXT, category TEXT,
description TEXT, status TEXT)""")
conn.commit()

class StudentProblems:

def add(self):  
    name = simpledialog.askstring("Add", "Student name:")  
    if not name:  
        return  
    cl = simpledialog.askstring("Add", "Class:")  
    cat = simpledialog.askstring("Add", "Category:")  
    desc = simpledialog.askstring("Add", "Description:")  
    if not all([cl, cat, desc]):  
        return  

    c.execute("INSERT INTO problems VALUES(NULL,?,?,?,?,?)",  
              (name, cl, cat, desc, "Pending"))  
    conn.commit()  
    messagebox.showinfo("Success", "Problem Added")  

def view(self):  
    c.execute("SELECT * FROM problems")  
    messagebox.showinfo("Problems", "\n".join(map(str, c.fetchall())) or "No problems")  

def search(self):  
    id = simpledialog.askinteger("Search", "Problem ID:")  
    if id:  
        c.execute("SELECT * FROM problems WHERE id=?", (id,))  
        messagebox.showinfo("Result", str(c.fetchone()))  

def update(self):  
    id = simpledialog.askinteger("Update", "Problem ID:")  
    status = simpledialog.askstring("Update", "Pending or Solved:")  
    if id and status in ["Pending", "Solved"]:  
        c.execute("UPDATE problems SET status=? WHERE id=?", (status, id))  
        conn.commit()  
        messagebox.showinfo("Update", "Updated" if c.rowcount else "Not found")

system = StudentProblems()

root = tk.Tk()
root.title("Student Problems System")
root.geometry("400x400")
root.configure(bg="#172554")

start = tk.Frame(root, bg="#172554")
menu = tk.Frame(root, bg="#172554")

tk.Label(start, text="STUDENT PROBLEMS",
font=("Arial", 20, "bold"), bg="#172554",
fg="white").pack(pady=60)

tk.Button(start, text="START", font=("Arial", 18),
command=lambda: (start.pack_forget(), menu.pack())).pack()

tk.Label(menu, text="Dashboard", font=("Arial", 20),
bg="#172554", fg="white").pack(pady=20)

for name, func in [
("Add Problem", system.add),
("View Problems", system.view),
("Search Problem", system.search),
("Update Status", system.update),
]:
tk.Button(menu, text=name, width=25, command=func).pack(pady=7)

tk.Button(menu, text="EXIT", command=root.destroy).pack(pady=15)

start.pack(expand=True)
root.mainloop()
conn.close()

