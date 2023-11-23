#include <Arduino.h>
#include <TM1637Display.h>
int calibrationTime = 30;   //the time we give the sensor to calibrate (10-60 secs according to the datasheet)     
long unsigned int lowIn;    //the time when the sensor outputs a low impulse     
long unsigned int pause = 5000;  //the amount of milliseconds the sensor has to be low  //before we assume all motion has stopped 
boolean lockLow = true;
boolean takeLowTime;  
int pirPin = 6;    //the digital pin connected to the PIR sensor's output
int ledPin = 13;
#define CLK 2 
#define DIO 3 
#define wechoPin 4 
#define wtrigPin 5 
#define Bzr 8
int maxppl = 4; // maximmum number of people are allowed to enter the room
int crppl = 0; // current people "counter count var"
long durts1; // variable for the duration of sound wave travel
int distn1; // variable for the distance measurement
uint8_t allDigits[] = {0xff, 0xff, 0xff, 0xff}; // declear all the digits
TM1637Display CN(CLK, DIO); // assign the pins to CN


void setup() {
  Serial.begin(9600);
  pinMode(pirPin, INPUT);
  pinMode(ledPin, OUTPUT);
  pinMode(Bzr, OUTPUT);
  digitalWrite(pirPin, LOW);
  CN.setBrightness(0x0f);

  //give the sensor some time to calibrate
  Serial.print("calibrating sensor ");
    for(int i = 0; i < calibrationTime; i++){
      Serial.print(".");
      delay(100);
      }
    Serial.println(" done");
    Serial.println("SENSOR ACTIVE");
    delay(1000);
}

void measurDist()
{
  //w[0] => echo, w[1] => trig
  pinMode(wechoPin, INPUT); //set echo as input
  pinMode(wtrigPin, OUTPUT); // set trig as ouput
  digitalWrite(wtrigPin, LOW); //clear the trig condition
  delayMicroseconds(2);
  digitalWrite(wtrigPin, HIGH); //set the trig condition active at 10microSec
  delayMicroseconds(10);
  digitalWrite(wtrigPin, LOW); 
  durts1 = pulseIn(wechoPin, HIGH); //(pin, mode, timeout in microsec)
  distn1 = (durts1*0.034)/2; 
 // Serial.print("Distance Sens1: ");
 // Serial.print(distn1);
 // Serial.println(" cm");
}

void pir()
{
      if(digitalRead(pirPin) == HIGH){
       crppl = crppl +1;      
       CN.showNumberDec(crppl); 
       if(lockLow && crppl != maxppl || crppl == maxppl){  
         //makes sure we wait for a transition to LOW before any further output is made:
         switch(crppl){
           case 1:
           crppl != maxppl;
           digitalWrite(ledPin, LOW);
           break;

           case 2:
           crppl == maxppl;
           digitalWrite(ledPin, HIGH);
           break;

           case 3:
           crppl > maxppl;
            digitalWrite(ledPin, HIGH);   //the led visualizes the sensors output pin state
            digitalWrite(Bzr, HIGH);
           break;
         }
         lockLow = false;            
         Serial.println("---");
         Serial.print("motion detected at ");
         Serial.print(millis()/1000);
         Serial.println(" sec"); 
         delay(3000);
         //if(crppl > maxppl){digitalWrite(Bzr, HIGH);}
         }
         takeLowTime = true;
       }

     if(digitalRead(pirPin) == LOW){       
       digitalWrite(ledPin, LOW);  //the led visualizes the sensors output pin state

       if(takeLowTime){
        lowIn = millis();          //save the time of the transition from high to LOW
        takeLowTime = false;       //make sure this is only done at the start of a LOW phase
        }
       //if the sensor is low for more than the given pause, 
       //we assume that no more motion is going to happen
       if(!lockLow && millis() - lowIn > pause){  
           //makes sure this block of code is only executed again after 
           //a new motion sequence has been detected
           lockLow = true;                        
           Serial.print("motion ended at ");      //output
           Serial.print((millis() - pause)/1000);
           Serial.println(" sec");
           delay(4000);
           }
       }
  }


void US()
{
  if(distn1<10  && crppl> maxppl)
  {
    measurDist();
    digitalWrite(Bzr, HIGH);
    crppl = crppl -1; 
    CN.showNumberDec(crppl);
    delay(3000);
    } else if (distn1<10 && crppl<=maxppl){
    measurDist();
    digitalWrite(Bzr, LOW);
    crppl = crppl-1; 
    CN.showNumberDec(crppl);
    delay(3000);
    }

}

void loop() {
  CN.showNumberDec(crppl);
 pir();
 measurDist();
 US();
}
