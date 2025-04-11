#!/usr/bin/env python3
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.slider import Slider
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.spinner import Spinner
from kivy.clock import Clock

import RPi.GPIO as GPIO
from adafruit_servokit import ServoKit
import time

# --- Hardware Setup ---

# Set up GPIO for motors (using IBT-4 controllers)
GPIO.setmode(GPIO.BCM)
# Motor 1 on GPIO 18 & 19; Motor 2 on GPIO 20 & 21
MOTOR1_IN1 = 18
MOTOR1_IN2 = 19
MOTOR2_IN1 = 20
MOTOR2_IN2 = 21

GPIO.setup(MOTOR1_IN1, GPIO.OUT)
GPIO.setup(MOTOR1_IN2, GPIO.OUT)
GPIO.setup(MOTOR2_IN1, GPIO.OUT)
GPIO.setup(MOTOR2_IN2, GPIO.OUT)

# For speed control, we assume PWM is applied on the IN1 pin.
MOTOR1_PWM = GPIO.PWM(MOTOR1_IN1, 1000)  # 1 kHz frequency
MOTOR2_PWM = GPIO.PWM(MOTOR2_IN1, 1000)
MOTOR1_PWM.start(0)
MOTOR2_PWM.start(0)

# Set up ServoKit for servos (assume channel 0 for servo 1, channel 1 for servo 2)
kit = ServoKit(channels=16)

# --- Hardware Control Functions ---

def set_motor_speed(motor, speed):
    """
    Controls IBT-4 motor speed (0-100).
    For Motor 1: uses MOTOR1_IN1 (PWM) and MOTOR1_IN2 for direction.
    For Motor 2: uses MOTOR2_IN1 (PWM) and MOTOR2_IN2.
    Here we assume only forward motion; adjust as needed.
    """
    if motor == 1:
        GPIO.output(MOTOR1_IN2, GPIO.LOW)
        MOTOR1_PWM.ChangeDutyCycle(speed)
        print(f"Motor 1 set to speed {speed}")
    elif motor == 2:
        GPIO.output(MOTOR2_IN2, GPIO.LOW)
        MOTOR2_PWM.ChangeDutyCycle(speed)
        print(f"Motor 2 set to speed {speed}")
    else:
        print("Invalid motor selection")

def set_servo1_mode(mode_value):
    """
    Controls Servo 1 on channel 0.
    mode_value should be: 60 (Fast), 85 (Medium), 105 (Slow), 115 (Very Slow), or 125 (Off).
    Moves the servo, waits a bit, but does NOT detach (angle=None) anymore.
    """
    print(f"Setting Servo 1 to {mode_value}")
    kit.servo[0].angle = mode_value
    time.sleep(0.5)
    print("Servo 1 position set.")

def set_servo2_action(action):
    """
    Controls Servo 2 on channel 1.
    action: 'left' -> 72, 'right' -> 180.
    Moves the servo, waits, then detaches.
    """
    if action == 'left':
        value = 72
    elif action == 'right':
        value = 180
    else:
        print("Invalid action for Servo 2")
        return
    print(f"Setting Servo 2 to {value} for action {action}")
    kit.servo[1].angle = value
    time.sleep(0.5)
    kit.servo[1].angle = None
    print("Servo 2 detached.")

def stop_all():
    """Stop both motors and set Servo 1 to off (125)."""
    set_motor_speed(1, 0)
    set_motor_speed(2, 0)
    set_servo1_mode(125)   # Value may require modification depending on the servo. 
    print("Stop all command executed.")

# --- Kivy UI ---

class ControlPanel(BoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = 'vertical'
        self.padding = 20
        self.spacing = 20

        # Title Label
        self.add_widget(Label(text="Pickleball Control Panel", font_size=35))

        # Motor 1 slider and label
        self.motor1_value = 0
        self.motor1_label = Label(text="Topspin: 0", font_size=30)
        self.motor1_slider = Slider(min=0, max=100, value=0)
        self.motor1_slider.bind(value=self.on_motor1_change)
        self.add_widget(self.motor1_label)
        self.add_widget(self.motor1_slider)

        # Motor 2 slider and label
        self.motor2_value = 0
        self.motor2_label = Label(text="Backspin: 0", font_size=30)
        self.motor2_slider = Slider(min=0, max=100, value=0)
        self.motor2_slider.bind(value=self.on_motor2_change)
        self.add_widget(self.motor2_label)
        self.add_widget(self.motor2_slider)

        # Servo 1 control (Spinner)
        self.add_widget(Label(text="Feed Speed", font_size=30))
        self.servo1_selection = "Off"  # default
        self.servo1_spinner = Spinner(
            text="Off",
            values=("Fast", "Medium", "Slow", "Very Slow", "Off"),
            size_hint=(None, None),
            size=(200, 44),
	    font_size='30sp'
        )
        self.servo1_spinner.bind(text=self.on_servo1_change)
        self.add_widget(self.servo1_spinner)

        # Servo 2 control (Buttons)
        self.add_widget(Label(text="Launch Angle", font_size=30))
        btn_layout = BoxLayout(orientation='horizontal', spacing=20, size_hint=(1, None), height=50)
        self.btn_left = Button(text="Rotate Left", font_size='30sp')
        self.btn_left.bind(on_release=lambda instance: set_servo2_action('left'))
        self.btn_right = Button(text="Rotate Right", font_size='30sp')
        self.btn_right.bind(on_release=lambda instance: set_servo2_action('right'))
        btn_layout.add_widget(self.btn_left)
        btn_layout.add_widget(self.btn_right)
        self.add_widget(btn_layout)

        # Delay selection (Spinner) for motor start
        self.add_widget(Label(text="Delay before start (seconds):", font_size=30))
        self.delay_spinner = Spinner(
            text="0",
            values=("0", "5", "10"),
            size_hint=(None, None),
            size=(200, 44),
	    font_size='30sp'
        )
        self.add_widget(self.delay_spinner)

        # Start button to initiate the system
        self.start_button = Button(text="Start", size_hint=(1, None), height=50, font_size='30sp')
        self.start_button.bind(on_release=self.start_system)
        self.add_widget(self.start_button)

        # Stop All button
        self.stop_button = Button(text="Stop All", size_hint=(1, None), height=50, font_size='30sp')
        self.stop_button.bind(on_release=lambda instance: stop_all())
        self.add_widget(self.stop_button)

    def on_motor1_change(self, instance, value):
        self.motor1_value = int(value)
        self.motor1_label.text = f"Topspin: {int(value)}"

    def on_motor2_change(self, instance, value):
        self.motor2_value = int(value)
        self.motor2_label.text = f"Backspin: {int(value)}"

    def on_servo1_change(self, spinner, text):
        self.servo1_selection = text

    def start_system(self, instance):
        try:
            delay = float(self.delay_spinner.text)
        except ValueError:
            delay = 0
        print(f"Starting system in {delay} seconds...")
        # Schedule the system initiation after the specified delay
        Clock.schedule_once(lambda dt: self.initiate_system(), delay)

    def initiate_system(self):
        # Apply the stored motor values
        set_motor_speed(1, self.motor1_value)
        set_motor_speed(2, self.motor2_value)
        # Map the servo1 selection to the proper value: Fast=60, Medium=85, Slow=105, VerySlow=115, Off=125  (may require modification depending on the servo)
        mapping = {"Fast": 60, "Medium": 85, "Slow": 105, "Very Slow": 115, "Off": 125}  
        mode_value = mapping.get(self.servo1_selection, 130)
        set_servo1_mode(mode_value)
        print("System initiated.")

class PickleballApp(App):
    def build(self):
        return ControlPanel()

if __name__ == '__main__':
    PickleballApp().run()

