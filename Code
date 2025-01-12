import cv2
from cvzone.HandTrackingModule import HandDetector
from picamera2 import Picamera2
from gpiozero import LED
from time import sleep

# Initialize Picamera2
picam2 = Picamera2()
picam2.preview_configuration.main.size = (720, 560)
picam2.preview_configuration.main.format = "RGB"
picam2.preview_configuration.align()
picam2.configure("preview")
picam2.start()

# Initialize HandDetector
detector = HandDetector(maxHands=2, detectionCon=0.5, minTrackCon=0.5)

# Initialize LEDs connected to GPIO pins
led1 = LED(17)  # GPIO pin 17
led2 = LED(18)  # GPIO pin 18
led3 = LED(27)  # GPIO pin 27

# Variables to track LED states
led1_state = False
led2_state = False
led3_state = False

while True:
    # Capture image from Picamera2
    im = picam2.capture_array()
    im = cv2.cvtColor(im, cv2.COLOR_RGB2BGR)

    # Detect hands and draw landmarks
    hands, im = detector.findHands(im, draw=True)

    # Check if hands are detected
    if hands:
        # Iterate over each hand detected
        for hand in hands:
            lmlist = hand['lmList']
            cx = lmlist[4][0]
            cy = lmlist[4][1]
            cv2.circle(im, (cx, cy), 13, (255, 0, 0), -1)

            fingers = detector.fingersUp(hand)
            count_1 = fingers.count(1)
            count_0 = fingers.count(0)

            print("Count of 1s:", count_1)
            print("Count of 0s:", count_0)

            # Gesture for turning on/off LED1
            if count_1 == 5:
                if not led1_state:
                    led1.on()
                    led1_state = True
                    print("LED1 turned on")
            elif count_0 == 4:
                if led1_state:
                    led1.off()
                    led1_state = False
                    print("LED1 turned off")

            # Gesture for turning on/off LED2
            if count_1 == 3:
                if not led2_state:
                    led2.on()
                    led2_state = True
                    print("LED2 turned on")
            elif count_0 == 2:
                if led2_state:
                    led2.off()
                    led2_state = False
                    print("LED2 turned off")

            # Gesture for turning on/off LED3
            if count_1 == 2:
                if not led3_state:
                    led3.on()
                    led3_state = True
                    print("LED3 turned on")
            elif count_0 == 1:
                if led3_state:
                    led3.off()
                    led3_state = False
                    print("LED3 turned off")

    cv2.imshow("im", im)
    key = cv2.waitKey(1) & 0xFF

    if key == ord("q"):
        cv2.destroyAllWindows()
        break
