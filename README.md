#include <LiquidCrystal.h>

#include <Wire.h>
 

//initialize pins
#define ENA 9         
#define IN1 5        
#define IN2 4        
#define JOYSTICK A0   
#define ENCODER_A 3  
#define ENCODER_B 2   

volatile int position = 0;  // set motor possition
int speedValue = 0;        //motor speed
String direction = "STOP";  //initialize direction

void setup() {
 
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(JOYSTICK, INPUT);
  pinMode(ENCODER_A, INPUT_PULLUP);
  pinMode(ENCODER_B, INPUT_PULLUP);

 
  attachInterrupt(digitalPinToInterrupt(ENCODER_A), readEncoder, CHANGE);

  Serial.begin(9600);
  delay(2000);
}

void loop() {
  //read from joystick
  int joystickValue = analogRead(JOYSTICK);

 
  if (joystickValue > 530) { 
    direction = "CW";
    speedValue = map(joystickValue, 530, 1023, 0, 255);
    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, speedValue);
  } else if (joystickValue < 490) {
    direction = "CCW";
    speedValue = map(joystickValue, 490, 0, 0, 255);
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, HIGH);
    analogWrite(ENA, speedValue);
  } else { // stop
    direction = "STOP";
    speedValue = 0;
    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, speedValue);
  }

 // set motor speed
  analogWrite(ENA, speedValue);



 
  Serial.print("Speed: ");
  Serial.print(speedValue);
  Serial.print(" Direction: ");
  Serial.print(direction);
  Serial.print(" Position: ");
  Serial.println(position);

  delay(200); 
}


void readEncoder() {
 //read chA and chB
  int stateA = digitalRead(ENCODER_A);
  int stateB = digitalRead(ENCODER_B);


  Serial.print("Encoder A: ");
  Serial.print(stateA);  // print Channel A value
  Serial.print(" Encoder B: ");
  Serial.println(stateB);  //print channel B value

  // update position
  if (stateA == stateB) {
    position++;  
  } else {
    position--;  
  }
}
