![t](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-21%20at%202.42.25%20PM.jpeg)

# First task ESP32 → Arduino UNO 
## Components
ESP32
Arduino Uno
LED
Resistor 220Ω
Jumper wires
Power bank (for ESP32)
USB cable (for Arduino)
## Wiring & Pin Mapping
ESP32 to Arduino Connection:
ESP32	Arduino Uno
IO17 (TX2)	Pin 10 (Software RX)
GND	GND

### Important: Both boards must share the same GND

LED Connection (Arduino):
Long leg (Anode): → Resistor → Pin 13
Short leg (Cathode): → GND
## How It Works

This project demonstrates serial communication between an ESP32 (transmitter) and an Arduino Uno (receiver).
[![p3](WhatsApp%20Image%202026-04-22%20at%2010.47.32%20PM.jpeg)](WhatsAppVideo2026-04-23at11.50.49AM.mp4)]

### ESP32 (Transmitter)
Uses Serial2 instead of the default serial
Sends text commands:
"ON" → Turn LED ON
"OFF" → Turn LED OFF
Repeats every 2 seconds
### Arduino (Receiver)
Uses SoftwareSerial on pin 10
Listens for incoming data from ESP32
Cleans the received text using trim()

Compares commands:
"ON" → LED ON
"OFF" → LED OFF
Troubleshooting (Important Notes)

If you see weird characters (garbage data), check:

Common GND connection
Matching baud rate (recommended: 4800 for both)
Use a proper TX pin on ESP32 (like IO17)
## Code 
ESP 32 
![1](https://github.com/sadeem058/Applied-Tasks/blob/main/Screenshot%20(99).png)
Arduino
![2](https://github.com/sadeem058/Applied-Tasks/blob/main/Screenshot%20(100).png)

## Explanation
### ESP32 Code (Transmitter)
void setup() {
  // Start Serial2 at 4800 baud
  // RX = 16, TX = 17
  Serial2.begin(4800, SERIAL_8N1, 16, 17); 
}
Serial2.begin(...): initializes secondary serial communication
4800: communication speed (baud rate)
16: RX pin (not used here)
17: TX pin (sending data to Arduino)
void loop() {
  Serial2.println("ON");   // Send ON
  delay(2000);             // Wait 2 seconds

  Serial2.println("OFF");  // Send OFF
  delay(2000);             // Wait 2 seconds
}
println: sends text + newline
delay: creates visible timing between commands
### Arduino Code (Receiver)
#include <SoftwareSerial.h>
Includes library to create an extra serial port
SoftwareSerial mySerial(10, 11); // RX, TX
Creates a new serial channel:
Pin 10 → Receive (from ESP32)
Pin 11 → Transmit (not used)
void setup() {
  Serial.begin(9600);     
  mySerial.begin(9600);   
  pinMode(13, OUTPUT);
  Serial.println("System Ready...");
}
Serial.begin: communication with PC
mySerial.begin: communication with ESP32
pinMode: sets LED pin as output

### Recommended: change 9600 → 4800 to match ESP32

void loop() {
  if (mySerial.available()) {
Checks if data is available
String data = mySerial.readStringUntil('\n');
Reads incoming text until newline
data.trim();
Removes unwanted spaces/newline characters
Serial.print("Received: "); 
Serial.println(data);
Prints received data to Serial Monitor
if (data == "ON") {
  digitalWrite(13, HIGH);
  Serial.println("LED is NOW ON");
}
If "ON" → turn LED ON
else if (data == "OFF") {
  digitalWrite(13, LOW);
  Serial.println("LED is NOW OFF");
}
If "OFF" → turn LED OFF

# ESP32 Stepper Motor Control via Web Server
Project Overview

This project demonstrates how to control a 28BYJ-48 Stepper Motor using an ESP32 and a ULN2003 driver over a local Wi-Fi network (Access Point mode).

The ESP32 creates its own Wi-Fi network and hosts a simple web page that allows the user to control the motor direction:

Clockwise (Forward)
Counter-Clockwise (Backward)

## Components
ESP32
28BYJ-48 Stepper Motor
ULN2003 Motor Driver
Jumper Wires
Power Supply

## Wiring & Pin Mapping

[m](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-23%20at%2012.47.07%20PM.jpeg)
[m](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-23%20at%2012.47.08%20PM.jpeg)

Connections:
IN1 → GPIO 17
IN2 → GPIO 19
IN3 → GPIO 5
IN4 → GPIO 18
VCC → 5V
GND → GND

### Important Note:
The pin sequence in the code is adjusted to: (17, 5, 19, 18)

This is required because the Stepper library needs a specific sequence to ensure correct rotation in both directions.

## How It Works
![x](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-23%20at%2012.33.01%20PM%20(1).jpeg)
The ESP32 creates a Wi-Fi network named:
Sadeem
After connecting to the network, open a browser and go to:
192.168.4.1
A simple web page will appear with two control links:
/H → Move motor forward (clockwise)
/L → Move motor backward (counter-clockwise)
![1](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-23%20at%2012.47.11%20PM.jpeg)
When a link is clicked:
An HTTP request is sent to the ESP32
The ESP32 reads the request
The motor rotates based on the command
## Code 

![c](https://github.com/sadeem058/Applied-Tasks/blob/main/Screenshot%20(102).png)
![c1](https://github.com/sadeem058/Applied-Tasks/blob/main/Screenshot%20(103).png)

## Explanation
📌 1. Library Inclusion
#include <Arduino.h>
#include <WiFi.h>
#include <NetworkClient.h>
#include <WiFiAP.h>
#include <Stepper.h>
WiFi.h → Creates the Access Point
NetworkServer → Handles HTTP requests
Stepper.h → Controls the stepper motor
📌 2. Stepper Configuration
const int stepsPerRevolution = 2048;
Stepper myStepper(stepsPerRevolution, 17, 5, 19, 18);
2048 steps = one full rotation
Pin order is adjusted for correct direction control
📌 3. Wi-Fi and Server Setup
const char *ssid = "Sadeem";
const char *password = "123456789";

NetworkServer server(80);
Creates a Wi-Fi Access Point
Runs a web server on port 80
📌 4. setup() Function
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
  myStepper.setSpeed(10);
Sets motor speed (RPM)
WiFi.softAP(ssid, password);
Starts the Wi-Fi network
server.begin();
Starts the server
📌 5. loop() Function
Accept Client Connection:
NetworkClient client = server.accept();
Read HTTP Request:
char c = client.read();
📌 6. Send Web Page
client.print("<h1>Sadeem's Stepper Control</h1>");
client.print("Click <a href=\"/H\">here</a> to move Forward (CW).<br>");
client.print("Click <a href=\"/L\">here</a> to move Backward (CCW).<br>");
📌 7. Motor Control Logic
Forward (Clockwise):
if (currentLine.endsWith("GET /H")) {
  myStepper.step(stepsPerRevolution);
}
Backward (Counter-Clockwise):
if (currentLine.endsWith("GET /L")) {
  myStepper.step(-stepsPerRevolution);
}
## How to Use
Upload the code to your ESP32
Connect to Wi-Fi:
Sadeem
Open your browser and go to:
192.168.4.1
Click the links to control the motor
