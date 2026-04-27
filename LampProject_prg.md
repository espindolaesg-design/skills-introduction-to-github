# Morse Code Lamp Controller via Bluetooth

Arduino project that controls a lamp through Bluetooth using the HM-10 module. Supports direct on/off commands and text input that gets translated into Morse code flashes through a relay.

---

## Connections

| HM-10 | Arduino |
|---|---|
| RX | Pin 11 |
| TX | Pin 10 |
| VCC | 5V |
| GND | GND |

| Relay | Arduino |
|---|---|
| IN | Pin 7 |
| VCC | 5V |
| GND | GND |

---

## Code

```cpp
#include <SoftwareSerial.h>  // Imports the library that allows serial communication on other digital pins

// --- CONFIGURATION ---
SoftwareSerial hm10(10, 11);  // Creates a software serial connection using pin 10 as RX and pin 11 as TX
const int RELAY = 7;           // Assigns pin 7 as the relay control pin

// --- DATA STRUCTURE (Morse List) ---
// Array that stores the Morse code for each letter from A to Z
String morseCode[] = {
  ".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....", "..",    // A-I
  ".---", "-.-", ".-..", "--", "-.", "---", ".--.", "--.-", ".-.",  // J-R
  "...", "-", "..-", "...-", ".--", "-..-", "-.--", "--.."          // S-Z
};

// --- REUSABLE FUNCTIONS ---

// Function that turns the relay on for a given duration, then turns it off
void flash(int duration) {
  digitalWrite(RELAY, HIGH);  // Turns the relay on (lamp ON)
  delay(duration);             // Keeps it on for the specified time in milliseconds
  digitalWrite(RELAY, LOW);   // Turns the relay off (lamp OFF)
  delay(200);                  // Waits 200ms before the next flash (gap between signals)
}

// Function that sends a single character as Morse code using the lamp
void sendLetter(char c) {
  if (c == ' ') {       // Checks if the character is a space
    delay(1000);         // Waits 1 second to represent the gap between words
    return;              // Exits the function without doing anything else
  }

  if (c >= 'A' && c <= 'Z') {          // Checks that the character is an uppercase letter
    int index = c - 'A';               // Calculates the array index (A=0, B=1, C=2, etc.)
    String code = morseCode[index];    // Gets the Morse code string for that letter

    for (int i = 0; i < code.length(); i++) {  // Loops through each symbol in the Morse code
      if (code[i] == '.')      flash(200);      // If the symbol is a dot, flashes for 200ms
      else if (code[i] == '-') flash(600);      // If the symbol is a dash, flashes for 600ms
    }
    delay(600);  // Waits 600ms after each letter to separate it from the next one
  }
}

// --- SETUP ---
void setup() {
  pinMode(RELAY, OUTPUT);       // Sets pin 7 as an output so it can control the relay
  digitalWrite(RELAY, LOW);    // Makes sure the relay starts in the OFF position
  hm10.begin(9600);            // Starts Bluetooth communication at 9600 baud rate
}

// --- MAIN LOOP ---
void loop() {
  if (hm10.available()) {   // Checks if there is incoming data from the Bluetooth module
    String incoming = "";    // Creates an empty string to store the received message

    // Reads the incoming data one character at a time until there is nothing left
    while (hm10.available()) {
      char c = hm10.read();           // Reads one character from the Bluetooth buffer

      if (c != '\n' && c != '\r') {  // Filters out newline and carriage return characters
        incoming += c;               // Adds the valid character to the incoming string
      }

      delay(10);  // Short pause to stabilize the Bluetooth communication
    }

    if (incoming.length() > 0) {  // Proceeds only if the received message is not empty

      if (incoming == "1") {              // If the message is "1", turn the lamp on directly
        digitalWrite(RELAY, HIGH);
      }
      else if (incoming == "0") {         // If the message is "0", turn the lamp off directly
        digitalWrite(RELAY, LOW);
      }
      else {                              // Otherwise, treat the message as text to send in Morse
        incoming.toUpperCase();           // Converts all characters to uppercase for Morse lookup

        for (int i = 0; i < incoming.length(); i++) {  // Loops through each character in the message
          sendLetter(incoming[i]);                      // Sends each character as a Morse flash sequence
        }
      }
    }
  }
}
```
