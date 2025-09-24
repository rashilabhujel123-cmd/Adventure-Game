# Adventure-Gameimport tkinter as tk
from tkinter import messagebox, ttk

# Adventure data
locations = {
    "forest": "🌲 You find a mysterious treasure chest...",
    "cave": "🦁 A wild animal attacks you! You barely escape...",
    "river": "🚣 You find a boat and escape to safety..."
}

# Typewriter animation
def typewriter(widget, text, delay=50):
    widget.config(text="")
    def animate(i=0):
        if i < len(text):
            widget.config(text=widget.cget("text") + text[i])
            widget.after(delay, animate, i+1)
    animate()

# Progress bar animation
def animate_progress():
    progress_bar['value'] = 0
    def step(i=0):
        if i <= 100:
            progress_bar['value'] = i
            root.after(20, step, i+5)
    step()

# Hover animation
def add_hover_effect(button, color_on="#ffcc99", color_off="#ccffcc", weight_on="bold", weight_off="normal"):
    def on_enter(e):
        button.config(bg=color_on, font=("Arial", 12, weight_on))
    def on_leave(e):
        button.config(bg=color_off, font=("Arial", 12, weight_off))
    button.bind("<Enter>", on_enter)
    button.bind("<Leave>", on_leave)

# Location button handler
def choose_location(loc):
    global selected_location
    selected_location = loc
    name = entry_name.get().strip()

    if not name:
        messagebox.showerror("Missing Name", "Please enter your name before choosing a location.")
        return

    animate_progress()
    typewriter(label_result, locations[loc])
    if loc == "forest":
        frame_decision.pack(pady=10)
    else:
        show_summary(name, loc, locations[loc])

# Chest decision handler
def choose_decision(decision):
    name = entry_name.get().strip()

    if not name:
        messagebox.showerror("Missing Name", "Please enter your name before making a decision.")
        return

    if decision == "open":
        outcome = "💰 You opened the chest and found nothing!"
    else:
        outcome = "🚶 You ignored the chest and you died."

    show_summary(name, "forest", outcome)

# Show final summary
def show_summary(name, location, outcome):
    summary = f"🧍 Name: {name}\n📍 Location: {location}\n🎯 Outcome: {outcome}"
    messagebox.showinfo("Adventure Summary", summary)
    reset_game()

# Reset game
def reset_game():
    entry_name.delete(0, tk.END)
    label_result.config(text="")
    frame_decision.pack_forget()
    progress_bar['value'] = 0

# GUI setup
root = tk.Tk()
root.title("✨ Hover-Animated Adventure Game")
root.geometry("600x550")
root.configure(bg="#e6f2ff")

# Title
tk.Label(root, text="🌍 Welcome to the Magical Adventure!", font=("Helvetica", 18, "bold"), bg="#e6f2ff", fg="#003366").pack(pady=10)

# Name input
tk.Label(root, text="Enter your name:", font=("Arial", 12), bg="#e6f2ff").pack()
entry_name = tk.Entry(root, font=("Arial", 12), bg="#ffffff", width=30)
entry_name.pack(pady=5)

# Location buttons
tk.Label(root, text="Choose a location:", font=("Arial", 12, "bold"), bg="#e6f2ff").pack(pady=5)
frame_buttons = tk.Frame(root, bg="#e6f2ff")
frame_buttons.pack()

for loc in locations:
    btn = tk.Button(frame_buttons, text=loc.capitalize(), font=("Arial", 12), bg="#ccffcc", width=10,
                    command=lambda l=loc: choose_location(l))
    btn.pack(side="left", padx=10)
    add_hover_effect(btn)

# Result label
label_result = tk.Label(root, text="", font=("Arial", 12), bg="#e6f2ff", fg="#003366", wraplength=500, justify="center")
label_result.pack(pady=10)

# Progress bar
progress_bar = ttk.Progressbar(root, orient="horizontal", length=400, mode="determinate")
progress_bar.pack(pady=10)

# Forest decision buttons
frame_decision = tk.Frame(root, bg="#f0fff0")
tk.Label(frame_decision, text="Do you want to 'open' or 'ignore' the chest?", font=("Arial", 12), bg="#f0fff0").pack(pady=5)

btn_open = tk.Button(frame_decision, text="Open", font=("Arial", 12), bg="#ffcc99", width=10, command=lambda: choose_decision("open"))
btn_ignore = tk.Button(frame_decision, text="Ignore", font=("Arial", 12), bg="#99ccff", width=10, command=lambda: choose_decision("ignore"))

btn_open.pack(side="left", padx=20)
btn_ignore.pack(side="right", padx=20)

add_hover_effect(btn_open, color_on="#ff9966", color_off="#ffcc99")
add_hover_effect(btn_ignore, color_on="#66b3ff", color_off="#99ccff")

# Reset button
btn_reset = tk.Button(root, text="🔄 Reset Game", font=("Arial", 11), bg="#ff9999", command=reset_game)
btn_reset.pack(pady=10)
add_hover_effect(btn_reset, color_on="#ff6666", color_off="#ff9999")

root.mainloop()
