import tkinter as tk

from tkinter import ttk, messagebox

import json

import os

FILE_NAME = "student.json"

inventory = []

# ---------- LOAD DATA ----------

def load_data():

    global inventory

    if os.path.exists(FILE_NAME):

        with open(FILE_NAME, "r") as f:

            try:

                inventory = json.load(f)

            except:

                inventory = []

# ---------- SAVE DATA ----------

def save_data():

    with open(FILE_NAME, "w") as f:

        json.dump(inventory, f, indent=4)

# ---------- MAIN WINDOW ----------

def main_menu():

    root = tk.Tk()

    root.title("Inventory Management System")

    root.geometry("400x400")

    tk.Label(root, text="Inventory Management System",

             font=("Arial", 16, "bold")).pack(pady=20)

    tk.Button(root, text="Add Product", width=25, command=add_product_window).pack(pady=5)

    tk.Button(root, text="View Inventory", width=25, command=view_inventory_window).pack(pady=5)

    tk.Button(root, text="Update Stock", width=25, command=update_stock_window).pack(pady=5)

    tk.Button(root, text="Low Stock Alert", width=25, command=low_stock_window).pack(pady=5)

    root.mainloop()

# ---------- ADD PRODUCT ----------

def add_product_window():

    win = tk.Toplevel()

    win.title("Add Product")

    win.geometry("300x250")

    tk.Label(win, text="Product Name").pack(pady=5)

    name_entry = tk.Entry(win)

    name_entry.pack()

    tk.Label(win, text="Stocks").pack(pady=5)

    stock_entry = tk.Entry(win)

    stock_entry.pack()

    def save():

        name = name_entry.get()

        stock = stock_entry.get()

        if name and stock.isdigit():

            inventory.append({"name": name, "qty": int(stock)})

            save_data()

            messagebox.showinfo("Success", "Product Added!")

            win.destroy()

        else:

            messagebox.showerror("Error", "Invalid input!")

    tk.Button(win, text="Add", command=save).pack(pady=20)

# ---------- VIEW ----------

def view_inventory_window():

    win = tk.Toplevel()

    win.title("View Inventory")

    win.geometry("400x300")

    table = ttk.Treeview(win, columns=("Name", "Stock"), show="headings")

    table.heading("Name", text="Name")

    table.heading("Stock", text="Stocks")

    for item in inventory:

        table.insert("", "end", values=(item["name"], item["qty"]))

    table.pack(fill="both", expand=True)

# ---------- SIMPLE INPUT ----------

def simple_input(prompt):

    popup = tk.Toplevel()

    popup.title("Input")

    tk.Label(popup, text=prompt).pack(pady=5)

    entry = tk.Entry(popup)

    entry.pack()

    result = []

    def submit():

        result.append(entry.get())

        popup.destroy()

    tk.Button(popup, text="OK", command=submit).pack(pady=5)

    popup.wait_window()

    return result[0] if result else None

# ---------- UPDATE STOCK ----------

def update_stock_window():

    win = tk.Toplevel()

    win.title("Update Stock")

    win.geometry("400x350")

    tk.Label(win, text="Search Product").pack(pady=5)

    search_entry = tk.Entry(win)

    search_entry.pack()

    listbox = tk.Listbox(win)

    listbox.pack(fill="both", expand=True)

    def search():

        listbox.delete(0, tk.END)

        keyword = search_entry.get().lower()

        for i, item in enumerate(inventory):

            if keyword in item["name"].lower():

                listbox.insert(tk.END, f"{i} - {item['name']} ({item['qty']})")

    def update():

        selected = listbox.get(tk.ACTIVE)

        if not selected:

            messagebox.showwarning("Warning", "Select a product first!")

            return

        index = int(selected.split(" - ")[0])

        new_qty = simple_input("Enter new stock:")

        if new_qty and new_qty.isdigit():

            inventory[index]["qty"] = int(new_qty)

            save_data()

            messagebox.showinfo("Updated", "Stock Updated!")

            search()

    def remove():

        selected = listbox.get(tk.ACTIVE)

        if not selected:

            messagebox.showwarning("Warning", "Select a product first!")

            return

        index = int(selected.split(" - ")[0])

        confirm = messagebox.askyesno("Confirm", "Delete this product?")

        if confirm:

            del inventory[index]

            save_data()

            messagebox.showinfo("Removed", "Product deleted!")

            search()

    tk.Button(win, text="Search", command=search).pack(pady=5)

    tk.Button(win, text="Update Selected", command=update).pack(pady=5)

    tk.Button(win, text="Remove Product", command=remove, bg="red", fg="white").pack(pady=5)

# ---------- LOW STOCK ----------

def low_stock_window():

    win = tk.Toplevel()

    win.title("Low Stock Alert")

    win.geometry("400x300")

    table = ttk.Treeview(win, columns=("Name", "Stock"), show="headings")

    table.heading("Name", text="Name")

    table.heading("Stock", text="Stock")

    for item in inventory:

        if item["qty"] < 5:

            table.insert("", "end", values=(item["name"], item["qty"]))

    table.pack(fill="both", expand=True)

# ---------- START ----------

load_data()

main_menu()
