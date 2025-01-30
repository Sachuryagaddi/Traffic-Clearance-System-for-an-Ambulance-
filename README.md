This project presents a traffic clearance system designed to enhance ambulance response in congested urban environments, leveraging the Arduino Mega microcontroller. The pressing issue of delayed emergency responses due to traffic congestion is addressed by implementing a real-time solution. Ultrasonic sensors mounted on the ambulance continually scan the surroundings, detecting obstacles in the vehicle's path. The Arduino Mega processes this data, triggering a dynamic LED display on the ambulance that communicates specific messages to nearby drivers, alerting them to the emergency situation and urging cooperation in clearing a path.  Beyond obstacle detection, the system integrates with existing traffic signal infrastructure using signal preemption technology. This functionality allows the Arduino Mega to communicate with traffic signals, prioritizing the ambulance's movement by temporarily modifying the signal phases to facilitate its unimpeded passage. The integration of GPS technology further optimizes the ambulance route in real-time, leveraging live traffic data to navigate the fastest and safest path to the destination.  To enhance public awareness and collaboration, the system incorporates wireless communication modules. These modules send real-time notifications to nearby vehicles, informing drivers about the approaching emergency vehicle and encouraging them to yield the right of way. This proactive approach fosters a cooperative environment among drivers, contributing to improved emergency vehicle navigation. 

# Traffic-Clearance-System-for-an-Ambulance- CODE
#include <MFRC522.h>
#include <SPI.h>

#define SS_PIN 53  // Pin connected to the SS/SDA pin of the RFID module
#define RST_PIN 49 // Pin connected to the RST pin of the RFID module

MFRC522 mfrc522(SS_PIN, RST_PIN);

// Define traffic light pins
int d_red = 2;
int d_yellow = 3;
int d_green = 4;
int r_red = 5;
int r_yellow = 6;
int r_green = 7;
int l_red = 8;
int l_yellow = 9;
int l_green = 10;
int u_red = 11;
int u_yellow = 12;
int u_green = 13;

void setup() {
	Serial.begin(9600);		// Initialize serial communications with the PC
	while (!Serial);		// Do nothing if no serial port is opened (added for Arduinos based on ATMEGA32U4)
	SPI.begin();			// Init SPI bus
	mfrc522.PCD_Init();		// Init MFRC522
	delay(4);				// Optional delay. Some board do need more time after init to be ready, see Readme
	mfrc522.PCD_DumpVersionToSerial();	// Show details of PCD - MFRC522 Card Reader details
	Serial.println(F("Scan PICC to see UID, SAK, type, and data blocks..."));
  pinMode(d_red, OUTPUT);
  pinMode(d_yellow, OUTPUT);
  pinMode(d_green, OUTPUT);
  pinMode(r_red, OUTPUT);
  pinMode(r_yellow, OUTPUT);
  pinMode(r_green, OUTPUT);
  pinMode(l_red, OUTPUT);
  pinMode(l_yellow, OUTPUT);
  pinMode(l_green, OUTPUT);
  pinMode(u_red, OUTPUT);
  pinMode(u_yellow, OUTPUT);
  pinMode(u_green, OUTPUT);
}

void loop() {
  if (  ! mfrc522.PICC_ReadCardSerial() &&   ! mfrc522.PICC_IsNewCardPresent()  ) 
  {
     changeLights();
     
  } else {
  handleRFID();
  }
}

void handleRFID() {
  Serial.println("RFID Tag Detected!");

  // Check the current state of the junction lights
  int currentUpperJunction = digitalRead(u_green);
  int currentRightJunction = digitalRead(r_green);
  int currentLeftJunction = digitalRead(l_green);

  // Set the lights for the RFID tag way (green) and delay the junction for 30 seconds
  digitalWrite(u_red, HIGH);
  digitalWrite(d_red, HIGH);
  digitalWrite(r_red, HIGH);
  digitalWrite(l_green, HIGH);

  digitalWrite(u_yellow, LOW);
  digitalWrite(d_yellow, LOW);
  digitalWrite(r_yellow, LOW);
  digitalWrite(l_yellow, LOW);

  // Other junction lights (turn lower junction green, and others to red)
  digitalWrite(u_green, LOW);
  digitalWrite(d_green, LOW);
  digitalWrite(r_green, LOW);
  digitalWrite(l_green, HIGH);

  delay(30000);
  // Reset lights to their previous state
  digitalWrite(u_green, currentUpperJunction);
  digitalWrite(r_green, currentRightJunction);
  digitalWrite(l_green, currentLeftJunction);
  // Reset lights to normal operation
  resetLights();
}
  void changeLights()
{
//Start (all yellow)
digitalWrite(u_red,LOW);
digitalWrite(d_red,LOW);
digitalWrite(r_red,LOW);
digitalWrite(l_green,LOW);
digitalWrite(u_yellow,HIGH);
digitalWrite(d_yellow,HIGH);
digitalWrite(r_yellow,HIGH);
digitalWrite(l_yellow,HIGH);
delay(1000);
 
//upper lane go
digitalWrite(u_yellow,LOW);
digitalWrite(d_yellow,LOW);
digitalWrite(r_yellow,LOW);
digitalWrite(l_yellow,LOW);
digitalWrite(u_green,HIGH);
digitalWrite(r_red,HIGH);
digitalWrite(l_red,HIGH);
digitalWrite(d_red,HIGH);
delay(1000);
//ALL YELLOW
digitalWrite(u_yellow,HIGH);
digitalWrite(d_yellow,HIGH);
digitalWrite(r_yellow,HIGH);
digitalWrite(l_yellow,HIGH);
digitalWrite(u_green,LOW);
digitalWrite(r_red,LOW);
digitalWrite(l_red,LOW);
digitalWrite(d_red,LOW);
delay(1000);
//RIGHT LANE GO
digitalWrite(u_yellow,LOW);
digitalWrite(d_yellow,LOW);
digitalWrite(r_yellow,LOW);
digitalWrite(l_yellow,LOW);
digitalWrite(u_red,HIGH);
digitalWrite(l_red,HIGH);
digitalWrite(d_red,HIGH);
digitalWrite(r_green,HIGH);
delay(1000);
//ALL YELLOW ON
digitalWrite(u_yellow,HIGH);
digitalWrite(d_yellow,HIGH);
digitalWrite(r_yellow,HIGH);
digitalWrite(l_yellow,HIGH);
digitalWrite(u_red,LOW);
digitalWrite(l_red,LOW);
digitalWrite(d_red,LOW);
digitalWrite(r_green,LOW);
delay(500);
//DOWN LANE GO
digitalWrite(u_yellow,LOW);
digitalWrite(d_yellow,LOW);
digitalWrite(r_yellow,LOW);
digitalWrite(l_yellow,LOW);
digitalWrite(u_red,HIGH);
digitalWrite(l_red,HIGH);
digitalWrite(r_red,HIGH);
digitalWrite(d_green,HIGH);
delay(1000);
//ALL YELLOW
digitalWrite(u_yellow,HIGH);
digitalWrite(d_yellow,HIGH);
digitalWrite(r_yellow,HIGH);
digitalWrite(l_yellow,HIGH);
digitalWrite(u_red,LOW);
digitalWrite(l_red,LOW);
digitalWrite(r_red,LOW);
digitalWrite(d_green,LOW);
delay(500);
//LEFT LANE GO
digitalWrite(u_yellow,LOW);
digitalWrite(d_yellow,LOW);
digitalWrite(r_yellow,LOW);
digitalWrite(l_yellow,LOW);
digitalWrite(u_red,HIGH);
digitalWrite(d_red,HIGH);
digitalWrite(r_red,HIGH);
digitalWrite(l_green,HIGH);
delay(1000);
}
void resetLights() {
  // Reset all lights to their initial state
  digitalWrite(u_red, LOW);
  digitalWrite(d_red, LOW);
  digitalWrite(r_red, LOW);
  digitalWrite(l_green, LOW);
  digitalWrite(u_yellow, HIGH);
  digitalWrite(d_yellow, HIGH);
  digitalWrite(r_yellow, HIGH);
  digitalWrite(l_yellow, HIGH);
  delay(1000); // 5 seconds for all yellow lights
}
