from tkinter import *
import time
import random

# Constants for the window and velocities
WIDTH = 500
HEIGHT = 500
xVelocity = 3
yVelocity = 2

# Create the window and canvas
window = Tk()
window.title("Animation with Score")
canvas = Canvas(window, width=WIDTH, height=HEIGHT)
canvas.pack()

# Load background and image
background_photo = PhotoImage(file='PYTHON Study\\bamboo.png')
background = canvas.create_image(0, 0, image=background_photo, anchor=NW)

photo_image = PhotoImage(file='PYTHON Study\\image.png')
my_image = canvas.create_image(10, 50, image=photo_image, anchor=NW)

# Get the image dimensions
image_width = photo_image.width()
image_height = photo_image.height()

# Score counter
score = 0

# Create a label to display the score in the window
score_label = Label(window, text=f"Score: {score}", font=("Arial", 16), fg="blue")
score_label.pack()

# Flag to track if the animation is still running
animation_running = False

# Function to handle image click, update score, and move image to a random position
def on_image_click(event):
    global score, animation_running
    if animation_running:  # Only increment score if animation is still running
        score += 1
        # Update the score label with the new score
        score_label.config(text=f"Score: {score}")
        
        # Generate random coordinates for the new position
        new_x = random.randint(0, WIDTH - image_width)
        new_y = random.randint(0, HEIGHT - image_height)
        
        # Move the image to the new coordinates
        canvas.coords(my_image, new_x, new_y)

# Function to start the animation with the user-defined time
def start_animation():
    global animation_running, start_time, animation_time
    try:
        # Get time input from the user
        animation_time = float(time_entry.get())
        
        # Disable the entry field and start button once animation begins
        time_entry.config(state='disabled')
        start_button.config(state='disabled')
        
        animation_running = True
        score_label.config(text=f"Score: {score}")  # Ensure the score label is updated
        
        # Record the start time
        start_time = time.time()

        # Animation loop
        animate()

    except ValueError:
        # Handle invalid input (non-numeric)
        error_label.config(text="Please enter a valid number for time.")
        return

# Animation loop that continues until the time runs out
def animate():
    global animation_running, xVelocity, yVelocity
    if not animation_running:
        return

    # Calculate elapsed time
    elapsed_time = time.time() - start_time
    
    # Stop the animation after the specified time
    if elapsed_time > animation_time:
        animation_running = False  # Stop the animation
        canvas.tag_unbind(my_image, '<Button-1>')  # Disable clicking the image to score
        final_score_label = Label(window, text=f"Final Score: {score}", font=("Arial", 24), fg="green")
        final_score_label.pack(pady=20)  # Show final score on the screen
        return
    
    # Get the coordinates of the image
    coordinates = canvas.coords(my_image)
    
    # Reverse direction if the image hits the window boundaries
    if coordinates[0] >= (WIDTH - image_width) or coordinates[0] < 0:
        xVelocity = -xVelocity
    if coordinates[1] >= (HEIGHT - image_height) or coordinates[1] < 0:
        yVelocity = -yVelocity
    
    # Move the image
    canvas.move(my_image, xVelocity, yVelocity)
    
    # Update the window
    window.update()
    
    # Pause briefly to control the frame rate
    time.sleep(0.01)
    
    # Call the animation function again
    animate()

# Bind click event to the canvas
canvas.tag_bind(my_image, '<Button-1>', on_image_click)

# Create an input field for the user to enter time duration
time_label = Label(window, text="Enter animation time (seconds):", font=("Arial", 14))
time_label.pack(pady=10)

time_entry = Entry(window, font=("Arial", 14))
time_entry.pack(pady=5)

# Frame for the buttons to keep them horizontally aligned
button_frame = Frame(window)
button_frame.pack(pady=20)

# Start button
start_button = Button(button_frame, text="Start", font=("Arial", 14), command=start_animation,bg="red")
start_button.pack(side=LEFT, padx=5)  # Added padding between buttons

# Restart button
restart_button = Button(button_frame, text="Restart ", font=("Arial", 14), command=lambda: restart_game(),bg="blue")
restart_button.pack(side=LEFT, padx=5)  # Added padding between buttons

# Quit button
quit_button = Button(button_frame, bg="yellow",text="Quit ", font=("Arial", 14), command=lambda: quit_game(),)
quit_button.pack(side=LEFT, padx=5)  # Added padding between buttons

# Error message label
error_label = Label(window, text="", font=("Arial", 12), fg="red")
error_label.pack()

# Function to restart the game
def restart_game():
    global score, animation_running
    # Reset score and label
    score = 0
    score_label.config(text=f"Score: {score}")
    
    # Re-enable the time input and start button
    time_entry.config(state='normal')
    start_button.config(state='normal')
    
    # Clear the final score label if it exists
    for widget in window.winfo_children():
        if isinstance(widget, Label) and widget.cget("text").startswith("Final Score:"):
            widget.destroy()
    
    # Re-enable click events for the image
    canvas.tag_bind(my_image, '<Button-1>', on_image_click)

    # Reset animation flag
    animation_running = False

# Function to quit the game
def quit_game():
    window.quit()  # Closes the Tkinter window and quits the program

# Start the Tkinter event loop
window.mainloop()
