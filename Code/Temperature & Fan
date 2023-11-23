#include <Arduino.h>
#define tempPin A0 // the output pin of LM35
#define fan 11 // the pin where fan should be
#define led 8 // led pin
int tempr;
int temp;
int tempMin = 15; // the temperature to start the fan 0%
int tempMax = 24; // the maximum temperature when fan is at 100%
int fanSpeed;
int TMS;
 
 //pwm impulse with modulation 
void setup() {
  pinMode(fan, OUTPUT);
  pinMode(led, OUTPUT);
  Serial.begin(4800);
}

void readTemp() { // get the temperature and convert it to celsius
  pinMode(tempPin, INPUT);
  tempr = analogRead(tempPin);
  temp = tempr * 0.48828125;
  Serial.print("temp: ");
  Serial.print(temp);
  Serial.println("C");
}

void CTMP()
{
  if(temp < tempMin) // if temp is lower than minimum temp
  {
    fanSpeed = 0; // fan is not spinning
    analogWrite(fan, fanSpeed);
    Serial.print("Fan speed: ");
    Serial.print(fanSpeed);
    Serial.println("%");
    digitalWrite(led, LOW);
    digitalWrite(fan, LOW);
  } else if(temp>=tempMin && temp<=tempMax) // if temperature is higher than minimum temp
  {
    TMS = temp;
    fanSpeed = 1.5*TMS;
    analogWrite(fan, fanSpeed); // spin the fan at the fanSpeed speed
    Serial.println("Fan speed: ");
    Serial.print(fanSpeed); 
    digitalWrite(led, LOW);
    digitalWrite(fan, HIGH);
  } else{
    digitalWrite(led, HIGH);
    digitalWrite(fan, HIGH);}
}

void loop()
{
 readTemp();
 CTMP();

}
