import tkinter as tk
from tkinter import scrolledtext, messagebox, filedialog
import requests
from datetime import datetime
import threading

# Tutorial content
tutorial_pages = [
    "Welcome to the very cool amazing leak checker\n\nThis tool checks if your email and password have appeared in known data breaches. It helps you audit your security and take action.",
    "To use it:\n\nTake the first part of your email (before the '@') and paste it into the input box.\n\nExample: 'exampleemail123' from 'exampleemail123@gmail.com'.",
    "Reading the results:\n\nIf leaks are found, they’ll be listed below.\nIf not, you’ll see a clean message.\n\nIf your credentials are compromised, change your password immediately.",
    "Bulk Scan:\n\nYou can scan a list of emails from a .txt file using the Bulk Scan button.\nChoose whether entries are full emails or partial usernames.\n⚠️ Large files may slow scanning.\n\nTry testing with:\nexampleemail123@gmail.com\ntechwizard88@yahoo.com\nsunnydays2020@outlook.com"
]

def check_comb(query, output_box):
    url = f"https://api.proxynova.com/comb?query={query}&start=0&limit=15"
    try:
        response = requests.get(url)
        data = response.json()
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        output_box.insert(tk.END, f"\n[{timestamp}] Checking: {query}\n")
        if data.get("lines"):
            output_box.insert(tk.END, "Leaked credentials found:\n")
            for line in data["lines"]:
                output_box.insert(tk.END, f"  {line}\n")
        else:
            output_box.insert(tk.END, "No leaked credentials found.\n")
    except Exception as e:
        output_box.insert(tk.END, f"Error: {e}\n")
    output_box.insert(tk.END, "-"*40 + "\n")
    output_box.see(tk.END)

def on_submit(entry, output_box):
    query = entry.get().strip()
    if query:
        check_comb(query, output_box)
        entry.delete(0, tk.END)

def wipe_logs(output_box):
    output_box.delete("1.0", tk.END)

def copy_output(output_box):
    root.clipboard_clear()
    root.clipboard_append(output_box.get("1.0", tk.END))
    messagebox.showinfo("Copied", "Output copied to clipboard.")

def bulk_scan(output_box):
    filepath = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt")])
    if not filepath:
        return

    def run_bulk(emails):
        progress = tk.Toplevel(root)
        progress.title("Scanning...")
        progress.geometry("400x100")
        label = tk.Label(progress, text="Scanning emails...")
        label.pack(pady=10)
        bar = tk.Canvas(progress, width=300, height=20)
        bar.pack()
        fill = bar.create_rectangle(0, 0, 0, 20, fill="green")

        for i, email in enumerate(emails):
            check_comb(email.strip(), output_box)
            bar.coords(fill, 0, 0, (i+1)/len(emails)*300, 20)
            progress.update()

        progress.destroy()

    def confirm_format():
        format_win = tk.Toplevel(root)
        format_win.title("Bulk Scan Settings")
        format_win.geometry("400x200")

        tk.Label(format_win, text="Are entries full emails or partial usernames?").pack(pady=10)
        var = tk.StringVar(value="partial")
        tk.Radiobutton(format_win, text="Full emails", variable=var, value="full").pack()
        tk.Radiobutton(format_win, text="Partial usernames", variable=var, value="partial").pack()
        tk.Label(format_win, text="⚠️ Large files may slow scanning.").pack(pady=10)

        def start_scan():
            with open(filepath, "r") as f:
                lines = f.readlines()
            emails = [line.strip().split("@")[0] if var.get() == "full" else line.strip() for line in lines]
            threading.Thread(target=run_bulk, args=(emails,), daemon=True).start()
            format_win.destroy()

        tk.Button(format_win, text="Start Scan", command=start_scan).pack(pady=10)

    confirm_format()

def apply_theme(theme):
    themes = {
        "Default": {"bg": "#f0f0f0", "fg": "black", "highlight": "#87CEEB"},
        "Dark Mode": {"bg": "#2e2e2e", "fg": "white", "highlight": "#3a3a3a"},
        "Night Sky": {"bg": "#0a0a23", "fg": "#ffffcc", "highlight": "#ffe066"},
        "Spooky": {"bg": "black", "fg": "white", "highlight": "#ff6600"}
    }
    style = themes.get(theme)
    if style:
        root.configure(bg=style["bg"])
        entry.configure(bg=style["bg"], fg=style["fg"], insertbackground=style["fg"])
        output_box.configure(bg=style["bg"], fg=style["fg"])
        submit_btn.configure(bg=style["highlight"], fg=style["fg"])
        wipe_btn.configure(bg=style["highlight"], fg=style["fg"])
        copy_btn.configure(bg=style["highlight"], fg=style["fg"])
        batch_btn.configure(bg=style["highlight"], fg=style["fg"])

def show_tutorial_popup():
    tutorial_win = tk.Toplevel(root)
    tutorial_win.title("Tutorial")
    tutorial_win.geometry("500x300")
    page_index = tk.IntVar(value=0)
    content = tk.Label(tutorial_win, text=tutorial_pages[0], wraplength=480, justify="left")
    content.pack(pady=20)

    def update_page():
        content.config(text=tutorial_pages[page_index.get()])
        back_btn.config(state="normal" if page_index.get() > 0 else "disabled")
        next_btn.config(state="normal" if page_index.get() < len(tutorial_pages) - 1 else "disabled")
        finish_btn.pack_forget()
        if page_index.get() == len(tutorial_pages) - 1:
            next_btn.pack_forget()
            finish_btn.pack(pady=10)
        else:
            next_btn.pack(pady=10)

    def next_page(): page_index.set(page_index.get() + 1); update_page()
    def prev_page(): page_index.set(page_index.get() - 1); update_page()
    def finish_tutorial(): tutorial_win.destroy()

    nav_frame = tk.Frame(tutorial_win); nav_frame.pack()
    back_btn = tk.Button(nav_frame, text="Back", command=prev_page)
    next_btn = tk.Button(nav_frame, text="Next", command=next_page)
    finish_btn = tk.Button(nav_frame, text="Finish", command=finish_tutorial)
    back_btn.pack(side="left", padx=10); next_btn.pack(side="left", padx=10)
    update_page()

def first_launch_check():
    if messagebox.askyesno("Welcome", "Would you like to start the tutorial?"):
        show_tutorial_popup()

def show_disclaimer():
    messagebox.showwarning("Disclaimer", "This tool is for cybersecurity auditing only.\nUse responsibly.")

def show_compromise_info():
    messagebox.showinfo("If You're Compromised", "Change your password, enable 2FA, and monitor activity.")

# GUI setup
root = tk.Tk()
root.title("Very Cool Amazing Leak Checker")

entry = tk.Entry(root, width=50)
entry.pack(pady=5)
entry.bind("<Return>", lambda event: on_submit(entry, output_box))

submit_btn = tk.Button(root, text="Send", command=lambda: on_submit(entry, output_box))
submit_btn.pack(pady=5)

output_box = scrolledtext.ScrolledText(root, width=80, height=20)
output_box.pack(padx=10, pady=10)

copy_btn = tk.Button(root, text="Copy Output", command=lambda: copy_output(output_box))
copy_btn.place(x=720, y=10)

wipe_btn = tk.Button(root, text="Wipe Logs", command=lambda: wipe_logs(output_box))
wipe_btn.pack(pady=5)

batch_btn = tk.Button(root, text="Bulk Scan", command=lambda: bulk_scan(output_box))
batch_btn.pack(pady=5)

menu_bar = tk.Menu(root)
info_menu = tk.Menu(menu_bar, tearoff=0)
info_menu.add_command(label="Disclaimer", command=show_disclaimer)
info_menu.add_command(label="If You're Compromised", command=show_compromise_info)
menu_bar.add_cascade(label="Info", menu=info_menu)
menu_bar.add_command(label="Tutorial", command=show_tutorial_popup)

custom_menu = tk.Menu(menu_bar, tearoff=0)
custom_menu.add_command(label="Default", command=lambda: apply_theme("Default"))
custom_menu.add_command(label="Dark Mode", command=lambda: apply_theme("Dark Mode"))
custom_menu.add_command(label="Night Sky", command=lambda: apply_theme("Night Sky"))
custom_menu.add_command(label="Spooky", command=lambda: apply_theme("Spooky"))
menu_bar.add_cascade(label="Customization", menu=custom_menu)

root.config(menu=menu_bar)
apply_theme("Default")
first_launch_check()
root.mainloop()
