# Vehicle Tracking System with GPS and GSM

## Project Overview
This project is a **Vehicle Tracking System** that utilizes GPS and GSM modules to track the vehicle’s real-time location. The system outputs the vehicle’s latitude and longitude, which can be monitored through SMS. The design and simulation are implemented using **Proteus software**.

## Features
- Real-time GPS tracking of vehicle's latitude and longitude.
- Communication via GSM module for remote location updates.
- Designed and simulated in **Proteus** for testing and visualization.

## Requirements
- **GPS Module** (e.g., NEO-6M)
- **GSM Module** - SIM900D
- Microcontroller - Arduino UNO
- **Proteus** software for simulation
- Additional components (resistors, wires, etc.)

## Installation and Setup
1. Clone the repository:
   ```bash
   git clone https://github.com/Deepthipriya30/Vehicle-Tracking-System-GPS-GSM.git

## Code
#include <TinyGPS.h>
#include <SoftwareSerial.h>
SoftwareSerial SIM900(7, 8);

TinyGPS gps;  //Creates a new instance of the TinyGPS object


void setup()
{
  Serial.begin(9600);
  SIM900.begin(9600);  
 
}

void loop()
{
  bool newData = false;
  unsigned long chars;
  unsigned short sentences, failed;

  // For one second we parse GPS data and report some key values
  for (unsigned long start = millis(); millis() - start < 1000;)
  {
    while (Serial.available())
    {
      char c = Serial.read();
      //Serial.print(c);
      if (gps.encode(c)) 
        newData = true;  
    }
  }

  if (newData)      //If newData is true
  {
    float flat, flon;
    unsigned long age;
    gps.f_get_position(&flat, &flon, &age);   
    SIM900.print("AT+CMGF=1\r"); 
    delay(400);
    SIM900.println("AT + CMGS = \"+91xxxxxxxxxx\"");// recipient's mobile number with country code
    delay(300);
    SIM900.print("Latitude = ");
    SIM900.print(flat == TinyGPS::GPS_INVALID_F_ANGLE ? 0.0 : flat, 6);
    SIM900.print(" Longitude = ");
    SIM900.print(flon == TinyGPS::GPS_INVALID_F_ANGLE ? 0.0 : flon, 6);
    delay(200);
    SIM900.println((char)26); // End AT command with a ^Z, ASCII code 26
    delay(200);
    SIM900.println();
    

  }
 
  Serial.println(failed);
 // if (chars == 0)
   // Serial.println("** No characters received from GPS: check wiring **");
}
