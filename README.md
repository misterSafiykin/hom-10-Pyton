
import tkinter as tk

# ---------------- GAME VARIABLES ----------------

bomb = 100
score = 0
game_started = False

BG = "#151525"
PANEL = "#24243A"
WHITE = "#F5F5FF"
ORANGE = "#FF7043"
YELLOW = "#FFD54F"
GREEN = "#4CAF50"


# ---------------- WINDOW ----------------

root = tk.Tk()
root.title("Bomb Clicker")
root.geometry("600x650+700+100")
root.resizable(False, False)
root.configure(bg=BG)

try:
    root.iconbitmap("favicon.ico")
except:
    pass


# ---------------- BOMB IMAGES ----------------

img_1 = tk.PhotoImage(file="bomb_1.png").subsample(3, 3)
img_2 = tk.PhotoImage(file="bomb_2.png").subsample(3, 3)
img_3 = tk.PhotoImage(file="bomb_3.png").subsample(3, 3)
img_4 = tk.PhotoImage(file="bomb_4.png").subsample(3, 3)


# ---------------- TITLE ----------------

title = tk.Label(
    root,
    text="💣 BOMB CLICKER",
    font=("Arial", 25, "bold"),
    bg=BG,
    fg=ORANGE
)
title.pack(pady=(20, 5))

label_1 = tk.Label(
    root,
    text="PRESS ENTER TO START",
    font=("Arial", 13, "bold"),
    bg=BG,
    fg=WHITE
)
label_1.pack(pady=5)


# ---------------- SCORE PANEL ----------------

info_frame = tk.Frame(root, bg=PANEL)
info_frame.pack(fill="x", padx=45, pady=12)

fuse_label = tk.Label(
    info_frame,
    text="FUSE: 100",
    font=("Arial", 15, "bold"),
    bg=PANEL,
    fg=YELLOW
)
fuse_label.pack(pady=(12, 5))

score_label = tk.Label(
    info_frame,
    text="SCORE: 0",
    font=("Arial", 15, "bold"),
    bg=PANEL,
    fg=WHITE
)
score_label.pack(pady=(5, 12))


# ---------------- FUSE BAR ----------------

bar = tk.Canvas(
    root,
    width=400,
    height=18,
    bg=PANEL,
    highlightthickness=0
)
bar.pack(pady=5)


def update_display():
    fuse_label.config(text=f"FUSE: {bomb}")
    score_label.config(text=f"SCORE: {score}")

    bar.delete("all")
    bar.create_rectangle(
        0, 0, 400, 18,
        fill="#41415A",
        outline=""
    )

    bar_color = GREEN

    if bomb <= 25:
        bar_color = "#FF3333"
    elif bomb <= 50:
        bar_color = ORANGE
    elif bomb <= 75:
        bar_color = YELLOW

    bar.create_rectangle(
        0, 0, bomb * 4, 18,
        fill=bar_color,
        outline=""
    )


# ---------------- BOMB IMAGE ----------------

bomb_label = tk.Label(
    root,
    image=img_1,
    bg=BG,
    cursor="hand2"
)
bomb_label.pack(pady=15)


# ---------------- CHANGE BOMB IMAGE ----------------

def change_bomb_image():
    if bomb > 75:
        bomb_label.config(image=img_1)
    elif bomb > 50:
        bomb_label.config(image=img_2)
    elif bomb > 25:
        bomb_label.config(image=img_3)
    else:
        bomb_label.config(image=img_4)


# ---------------- GAME OVER ----------------

def game_over():
    global game_started

    game_started = False

    label_1.config(
        text=f"BOOM! FINAL SCORE: {score}",
        fg=ORANGE
    )

    bomb_label.config(image=img_4)
    bonus_button.config(state="disabled")


# ---------------- NORMAL CLICK ----------------

def click_bomb(event=None):
    global bomb, score

    if not game_started:
        return

    score += 1
    bomb -= 1

    if bomb <= 0:
        bomb = 0
        update_display()
        game_over()
        return

    update_display()
    change_bomb_image()


# ---------------- NEW FEATURE: BONUS CLICK ----------------

def bonus_click():
    global bomb, score

    if not game_started:
        return

    score += 5
    bomb -= 5

    if bomb <= 0:
        bomb = 0
        update_display()
        game_over()
        return

    label_1.config(text="BONUS CLICK! +5 POINTS!", fg=YELLOW)

    update_display()
    change_bomb_image()


# ---------------- START GAME ----------------

def start_game(event=None):
    global bomb, score, game_started

    if game_started:
        return

    bomb = 100
    score = 0
    game_started = True

    label_1.config(
        text="CLICK THE BOMB!",
        fg=WHITE
    )

    bomb_label.config(image=img_1)
    bonus_button.config(state="normal")

    update_display()


# ---------------- RESTART GAME ----------------

def restart_game():
    global game_started

    game_started = False
    start_game()


# ---------------- BUTTONS ----------------

bonus_button = tk.Button(
    root,
    text="⚡ BONUS CLICK (+5)",
    font=("Arial", 12, "bold"),
    bg="#6C4AB6",
    fg=WHITE,
    activebackground="#8B68D8",
    activeforeground=WHITE,
    relief="flat",
    cursor="hand2",
    width=22,
    pady=8,
    state="disabled",
    command=bonus_click
)
bonus_button.pack(pady=8)

restart_button = tk.Button(
    root,
    text="↻ RESTART GAME",
    font=("Arial", 11, "bold"),
    bg=GREEN,
    fg=WHITE,
    activebackground="#388E3C",
    activeforeground=WHITE,
    relief="flat",
    cursor="hand2",
    width=22,
    pady=7,
    command=restart_game
)
restart_button.pack(pady=5)

hint = tk.Label(
    root,
    text="Click the bomb to score points!",
    font=("Arial", 10),
    bg=BG,
    fg="#A6A6C8"
)
hint.pack(pady=8)


# ---------------- CONTROLS ----------------

bomb_label.bind("<Button-1>", click_bomb)

root.bind_all("<Return>", start_game)
root.bind_all("<KP_Enter>", start_game)

update_display()

# ---------------- RUN GAME ----------------

root.mainloop()
