#include <Servo.h>

Servo Myservo;

#define trigPin 9           // Trig Pin Of HC-SR04
#define echoPin 8           // Echo Pin Of HC-SR04
#define MLa 4               // left motor 1st pin
#define MLb 5               // left motor 2nd pin
#define MRa 6               // right motor 1st pin
#define MRb 7               // right motor 2nd pin


int brakeTime = 45;
int brkonoff = 1;           // 1 for the electronic braking system, 0 for normal.

void brakeOff(void);
void brakeOn(void);


int command;                // Int to store app command state.
int buttonState = 0;
int lastButtonState = 0;

void setup() {
  pinMode(MLa, OUTPUT);       // Set Motor Pins As O/P
  pinMode(MLb, OUTPUT);
  pinMode(MRa, OUTPUT);
  pinMode(MRb, OUTPUT);

  pinMode(trigPin, OUTPUT);   // Set Trig Pin As O/P To Transmit Waves
  pinMode(echoPin, INPUT);    // Set Echo Pin As I/P To Receive Reflected Waves
  Myservo.attach(10);

   Serial.begin(9600);         // Set the baud rate to your Bluetooth module.
}

void loop() {
  // Check for Bluetooth command
  if (Serial.available() > 0)
  {
    command = Serial.read();
    Stop(); // Initialize with motors stopped.

    switch (command) {
      case 'F':
        forward();
        break;
      case 'B':
        back();
        break;
      case 'L':
        left();
        break;
      case 'R':
        right();
        break;
      case 'S':
        Stop();
        break;
         }

    if (brkonoff == 1) 
    {
      brakeOn();
    } 
    else 
    {
      brakeOff();
    }
  }
  else
  {
    // If no Bluetooth command, perform obstacle avoidance
    obstacleAvoidance();
  }
}

void obstacleAvoidance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);    // Transmit Waves For 10us
  delayMicroseconds(10);
  long duration = pulseIn(echoPin, HIGH); // Receive Reflected Waves
  long distance = duration / 58.2;       // Get Distance

  Serial.println(distance);
  delay(10);

  if (distance > 40) 
  {
    // No obstacle, move forward
    Myservo.write(90);
    forward();
  } 
  
  else
  {
    // Obstacle detected, perform avoidance behavior
     Stop();
    delay(100);

    Myservo.write(0);
    delay(500);
    Myservo.write(180);
    delay(500);
    Myservo.write(90);
    delay(500);

    back();
    delay(500);

    Stop();
    delay(100);

    left();
    delay(500);
  }
}

void forward() {
    analogWrite(MRb, 200);       // Move Forward
    analogWrite(MRa, 0);
    analogWrite(MLb, 200);
    analogWrite(MLa, 0);
}

void back() {
    analogWrite(MRb, 0);        // Move Backward
    analogWrite(MRa, 200);
    analogWrite(MLb, 0);
    analogWrite(MLa, 200);
}

void left() {
    analogWrite(MRb, 200);       // Move Left
    analogWrite(MRa, 0);
    analogWrite(MLb, 0);
    analogWrite(MLa, 0);
}

void right() {
    analogWrite(MRb, 0);       // Move right
    analogWrite(MRa, 0);
    analogWrite(MLb, 200);
    analogWrite(MLa, 0);
}



void Stop() {
    analogWrite(MRb, 0);       // Stop
    analogWrite(MRa, 0);
    analogWrite(MLa, 0);
    analogWrite(MLb, 0);
}

void brakeOn() {
  // Here's the future use: an electronic braking system!
  // read the pushbutton input pin:
  buttonState = command;
  // compare the buttonState to its previous state
  if (buttonState != lastButtonState) {
    // if the state has changed, increment the counter
    if (buttonState == 'S') {
      if (lastButtonState != buttonState) {
        analogWrite(MRb, 200);
        analogWrite(MRa, 200);
        analogWrite(MLa, 200);
        analogWrite(MLb, 200);
        delay(brakeTime);
        Stop();
      }
    }
    lastButtonState = buttonState;
  }
}

   void brakeOff() {
  // Release the electronic brake
    Stop();
}
  
    // save the current state as the last state,
    // for next time through the loop
