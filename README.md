import cv2
import numpy as np
from tkinter import *
from threading import Thread

def sketch(image):
    # Convert image to grayscale
    img_gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    # Clean up image using Gaussian Blur
    img_gray_blur = cv2.GaussianBlur(img_gray, (5, 5), 0)
    # Extract edges
    canny_edges = cv2.Canny(img_gray_blur, 30, 70)
    # Do an invert binarize the image
    ret, mask = cv2.threshold(canny_edges, 120, 255, cv2.THRESH_BINARY_INV)
    return mask

def liveSketch():
    cap = cv2.VideoCapture(0)  # Open webcam
    while True:
        ret, frame = cap.read()
        if not ret:
            print("Failed to capture video.")
            break
        cv2.imshow("Live Sketch", sketch(frame))

        # Capture an image when 'c' is pressed
        if cv2.waitKey(1) & 0xFF == ord('c'):
            result, image = cap.read()
            if result:
                cv2.imshow("Captured", image)
                # Ensure the 'image' folder exists or change the path
                cv2.imwrite("mypic.jpg", image)
                print("Image saved as 'mypic.jpg'")

        # Exit when 'q' is pressed
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()


def startThread():
    # Run OpenCV functions in a separate thread to avoid blocking Tkinter's main loop
    thread = Thread(target=liveSketch)
    thread.daemon = True
    thread.start()

def exitApp():
    root.destroy()

# Tkinter GUI
root = Tk()
root.title("Live Sketch")
root.geometry("400x300")

heading = Label(root, text="Live Sketch", font="Arial 20 bold", fg="yellow", bg="blue")
heading.pack(pady=20)

btn1 = Button(root, text="Start Live Sketch", width=20, bg='black', fg='white',
              font='Arial 12 bold', command=startThread)
btn1.pack(pady=10)

exitbtn = Button(root, text="Exit", width=20, bg='black', fg='white', font='Arial 12 bold', command=exitApp)
exitbtn.pack(pady=10)

root.mainloop()
