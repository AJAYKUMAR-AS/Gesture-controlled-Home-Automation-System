# Gesture-controlled-Home-Automation-System
# Importing required libraries
import cv2
from cvzone.HandTrackingModule import HandDetector
from gpiozero import LED
from picamera2 import Picamera2
import time

# Setting up GPIO pins for LEDs
led1 = LED(17)
led2 = LED(18)
led3 = LED(27)

# Initializing Pi Camera
picam2 = Picamera2()
picam2.preview_configuration.main.size = (720, 560)
picam2.preview_configuration.main.format = "RGB"
picam2.configure("preview")
picam2.start()

# Initializing HandDetector for hand gesture recognition
detector = HandDetector(maxHands=2, detectionCon=0.5, minTrackCon=0.5)

while True:
    # Capturing video frame
    img = picam2.capture_array()
    
    # Detecting hands in the current frame
    hands, img = detector.findHands(img)

    if hands:
        # Extracting hand landmarks
        for hand in hands:
            # Counting the number of fingers raised
            fingers = hand['lmList']
            count_1 = 0
            count_0 = 0

            # Loop through all 5 fingers and count how many are up
            for i in range(5):
                if fingers[i][2] < fingers[i - 2][2] if i > 0 else False:
                    count_1 += 1
                else:
                    count_0 += 1

            # Control LEDs based on the number of fingers
            if count_1 == 5:
                led1.on()
            elif count_0 == 4:
                led1.off()

            if count_1 == 3:
                led2.on()
            elif count_0 == 2:
                led2.off()

            if count_1 == 2:
                led3.on()
            elif count_0 == 1:
                led3.off()

    # Displaying the processed video feed with hand landmarks
    cv2.imshow("im", img)

    # Exiting the program on pressing "q"
    key = cv2.waitKey(1) & 0xFF
    if key == ord("q"):
        cv2.destroyAllWindows()
        break
