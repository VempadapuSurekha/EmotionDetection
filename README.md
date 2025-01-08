# EmotionDetection

# import cv2
# from deepface import DeepFace

# # Load face cascade classifier
# face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

# # Start capturing video
# cap = cv2.VideoCapture(0)

# while True:
#     # Capture frame-by-frame
#     ret, frame = cap.read()

#     # Convert frame to grayscale
#     gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

#     # Convert grayscale frame to RGB format
#     rgb_frame = cv2.cvtColor(gray_frame, cv2.COLOR_GRAY2RGB)

#     # Detect faces in the frame
#     faces = face_cascade.detectMultiScale(gray_frame, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

#     for (x, y, w, h) in faces:
#         # Extract the face ROI (Region of Interest)
#         face_roi = rgb_frame[y:y + h, x:x + w]

        
#         # Perform emotion analysis on the face ROI
#         result = DeepFace.analyze(face_roi, actions=['emotion'], enforce_detection=False)

#         # Determine the dominant emotion
#         emotion = result[0]['dominant_emotion']

#         # Draw rectangle around face and label with predicted emotion
#         cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 0, 255), 2)
#         cv2.putText(frame, emotion, (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 0, 255), 2)

#     # Display the resulting frame
#     cv2.imshow('Real-time Emotion Detection', frame)

#     # Press 'q' to exit
#     if cv2.waitKey(1) & 0xFF == ord('q'):
#         break

# # Release the capture and close all windows
# cap.release()
# cv2.destroyAllWindows()

# import cv2
# from deepface import DeepFace
# import threading
# import tkinter as tk
# from tkinter import Button, Label

# # Load face cascade classifier
# face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

# # Global variable to control the camera
# running = False

# def start_camera():
#     """Starts the camera feed and performs emotion detection."""
#     global running
#     running = True
    
#     # Start capturing video
#     cap = cv2.VideoCapture(0)
    
#     while running:
#         # Capture frame-by-frame
#         ret, frame = cap.read()
#         if not ret:
#             break
        
#         # Convert frame to grayscale
#         gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

#         # Convert grayscale frame to RGB format
#         rgb_frame = cv2.cvtColor(gray_frame, cv2.COLOR_GRAY2RGB)

#         # Detect faces in the frame
#         faces = face_cascade.detectMultiScale(gray_frame, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

#         for (x, y, w, h) in faces:
#             # Extract the face ROI (Region of Interest)
#             face_roi = rgb_frame[y:y + h, x:x + w]

#             try:
#                 # Perform emotion analysis on the face ROI
#                 result = DeepFace.analyze(face_roi, actions=['emotion'], enforce_detection=False)
                
#                 # Determine the dominant emotion
#                 emotion = result[0]['dominant_emotion']
                
#                 # Draw rectangle around face and label with predicted emotion
#                 cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 0, 255), 2)
#                 cv2.putText(frame, emotion, (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 0, 255), 2)
#             except Exception as e:
#                 print(f"Error: {e}")
        
#         # Display the resulting frame
#         cv2.imshow('Real-time Emotion Detection', frame)

#         # Press 'q' to stop via keyboard
#         if cv2.waitKey(1) & 0xFF == ord('q'):
#             break

#     # Release the capture and close all windows
#     cap.release()
#     cv2.destroyAllWindows()

# def stop_camera():
#     """Stops the camera feed."""
#     global running
#     running = False

# def run_camera_thread():
#     """Runs the camera in a separate thread to avoid blocking the GUI."""
#     threading.Thread(target=start_camera, daemon=True).start()

# # GUI Setup
# root = tk.Tk()
# root.title("Emotion Detection")
# root.geometry("400x300")
# root.configure(bg="#add8e6")  # Light blue background

# # Create buttons
# start_button = Button(root, text="Start", command=run_camera_thread, font=("Helvetica", 14), bg="green", fg="white")
# start_button.pack(pady=20)

# stop_button = Button(root, text="Stop", command=stop_camera, font=("Helvetica", 14), bg="red", fg="white")
# stop_button.pack(pady=20)

# # Add a label for the title
# label = Label(root, text="Emotion Detection System", font=("Helvetica", 16), bg="#add8e6", fg="black")
# label.pack(pady=20)

# # Run the GUI main loop
# root.mainloop()


import cv2
from deepface import DeepFace
import threading
import tkinter as tk
from tkinter import Button, Label

# Load face cascade classifier
face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')

# Global variable to control the camera
running = False
last_emotion = "No face detected"  # Cache for the last detected emotion

def start_camera():
    """Starts the camera feed and performs emotion detection."""
    global running, last_emotion
    running = True

    # Start capturing video
    cap = cv2.VideoCapture(0)

    while running:
        # Capture frame-by-frame
        ret, frame = cap.read()
        if not ret:
            break

        # Convert frame to grayscale
        gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

        # Convert grayscale frame to RGB format
        rgb_frame = cv2.cvtColor(gray_frame, cv2.COLOR_GRAY2RGB)

        # Detect faces in the frame
        faces = face_cascade.detectMultiScale(gray_frame, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

        for (x, y, w, h) in faces:
            # Extract the face ROI (Region of Interest)
            face_roi = rgb_frame[y:y + h, x:x + w]

            try:
                # Perform emotion analysis on the face ROI
                result = DeepFace.analyze(face_roi, actions=['emotion'], enforce_detection=False)

                # Determine the dominant emotion
                last_emotion = result[0]['dominant_emotion']

                # Draw rectangle around face and label with predicted emotion
                cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 0, 255), 2)
                cv2.putText(frame, last_emotion, (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 0, 255), 2)
            except Exception as e:
                print(f"Error: {e}")

        # Display the resulting frame
        cv2.imshow('Real-time Emotion Detection', frame)

        # Press 'q' to stop via keyboard
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    # Release the capture and close all windows
    cap.release()
    cv2.destroyAllWindows()

def stop_camera():
    """Stops the camera feed."""
    global running
    running = False

def run_camera_thread():
    """Runs the camera in a separate thread to avoid blocking the GUI."""
    threading.Thread(target=start_camera, daemon=True).start()

# GUI Setup
root = tk.Tk()
root.title("Emotion Detection")
root.geometry("500x400")

# Add a unique gradient background using a Canvas
canvas = tk.Canvas(root, width=500, height=400)
canvas.pack(fill="both", expand=True)

# Add gradient background
gradient = canvas.create_rectangle(0, 0, 500, 400, fill="#1E90FF", outline="")
canvas.tag_lower(gradient)

# Add title label
label = Label(root, text="Emotion Detection System", font=("Helvetica", 18, "bold"), bg="#87CEFA", fg="black")
label_window = canvas.create_window(250, 50, window=label, anchor="center")

# Create Start button
start_button = Button(root, text="Start", command=run_camera_thread, font=("Helvetica", 14), bg="green", fg="white")
start_button_window = canvas.create_window(150, 300, window=start_button, anchor="center")

# Create Stop button
stop_button = Button(root, text="Stop", command=stop_camera, font=("Helvetica", 14), bg="red", fg="white")
stop_button_window = canvas.create_window(350, 300, window=stop_button, anchor="center")

# Run the GUI main loop
root.mainloop()
