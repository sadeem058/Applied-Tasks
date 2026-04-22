![t](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Image%202026-04-21%20at%202.42.25%20PM.jpeg)

# First task ESP32 → Arduino UNO 
## Components
ESP32
Arduino Uno
LED
Resistor (220Ω – 330Ω)
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
[![p3](WhatsApp%20Image%202026-04-22%20at%2010.47.32%20PM.jpeg)](https://github.com/sadeem058/Applied-Tasks/blob/main/WhatsApp%20Video%202026-04-22%20at%2010.30.13%20PM.mp4)

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
