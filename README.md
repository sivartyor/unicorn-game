from tkinter import *
import random

window = Tk()
window.title ('The Candy Monster Game')

canvas = Canvas(window, width=1280, height=720, bg = 'pink')
canvas.pack()

title = canvas.create_text(640, 360, text= 'The Candy Monster', fill='white', font = ('Helvetica', 75))
directions = canvas.create_text(640, 440, text= 'Collect candy, but avoid the red ones', fill= 'white', font= ('Helvetica', 30))

score = 0
score_display = Label(window, text="Score :" + str(score))
score_display.pack()

level = 1
level_display = Label(window, text= "Level : " + str(level))
level_display.pack()

player_image = PhotoImage(file="unicorn.gif")
mychar = canvas.create_image(150, 500, image = player_image)

candy_list = []
bad_candy_list = []
candy_speed = 2
candy_color_list = ['red', 'yellow','blue', 'green', 'purple', 'black', 'white']


def make_candy():
    xposition = random.randint(1, 1280)
    candy_color = random.choice(candy_color_list)
    candy = canvas.create_oval(xposition, 0, xposition+30, 30, fill = candy_color)

    candy_list.append(candy)
    if candy_color =='red':
        bad_candy_list.append(candy)
    window.after(1000, make_candy)

def move_candy():
    for candy in candy_list:
        canvas.move(candy, 0, candy_speed)
        if canvas.coords(candy)[1] > 1280:
            xposition = random.randint(1,1280)
            canvas.coords(candy, xposition, 0, xposition+30,30)
    window.after(50, move_candy)

def update_score_level():
    global score, level, candy_speed
    score = score + 1
    score_display.config(text="Score :" + str(score))
    if score > 5 and score <= 10:
        candy_speed = candy_speed + 1
        level = 2
        level_display.config(text="Level :" + str(level))
    elif score > 10:
        candy_speed = candy_speed + 1
        level = 3
        level_display.config(text="Level :" + str(level))
        
        
def end_game_over():
    window.destroy()
    return window.mainloop()

def end_title():
        canvas.delete(title)
        canvas.delete(directions)

def collision(item1, item2, distance):
    xdistance = abs(canvas.coords(item1)[0] - canvas.coords(item2)[0])
    ydistance = abs(canvas.coords(item1)[1] - canvas.coords(item2)[1])
    overlap = xdistance < distance and ydistance < distance 
    return overlap

def check_hits():
    for candy in bad_candy_list:
        if collision(mychar, candy, 30):
           game_over = canvas.create_text(680, 150, text= 'Game Over', fill='red', font=('Helvetica', 75))
           window.after(1000, end_game_over)
           return window.mainloop()

    for candy in candy_list:
        if collision(mychar, candy, 30):
           canvas.delete(candy)
           candy_list.remove(candy)
           update_score_level()
    window.after(100, check_hits)


move_direction = 0
def check_input(event):
    global move_direction
    key = event.keysym
    if key == "Right":
        move_direction = "Right"
    elif key == "Left":
        move_direction = "Left"
def end_input(event):
        global move_direction
        move_direction = "None"

def move_character():
    if move_direction == "Right" and canvas.coords(mychar)[0] < 1280:
        canvas.move(mychar, 10,0)
    if move_direction == "Left" and canvas.coords(mychar)[0] > 0:
        canvas.move(mychar, -10,0)
    window.after(16, move_character)



canvas.bind_all('<KeyPress>', check_input)
canvas.bind_all('<KeyRelease>', end_input)
    
window.after(1000, end_title)
window.after(1000, make_candy)
window.after(1000, move_candy)
window.after(1000, check_hits)
window.after(1000, move_character)


window.mainloop()
