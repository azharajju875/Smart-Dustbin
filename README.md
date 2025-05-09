# SMART-DUSTBIN

#define BLYNK_TEMPLATE_ID "TMPL3qYmJVbGO" 
#define BLYNK_TEMPLATE_NAME "distance" 
#define BLYNK_AUTH_TOKEN "7yWEleW7S5z9jrn0x1cMvUaZxYGsb6EH" 
#include<ESP8266WiFi.h> 
#include<BlynkSimpleEsp8266.h> 
#include<Servo.h> 
char auth[]="7yWEleW7S5z9jrn0x1cMvUaZxYGsb6EH"; 
char ssid[]="*******"; 
char pass[]="********"; 
#define trig1 D5 
#define echo1 D6 
#define servo1 D3 
#define trig2 D1 
#define echo2 D2 
#define trig3 D7 
#define echo3 D8 
#define servo2 D4 
#define sensor A0 
#define irpin D0 
Servo servoMotor1; 
Servo servoMotor2; 
BlynkTimer timer; 
void setup() { 
pinMode (trig1,OUTPUT); 
pinMode (echo1,INPUT); 
pinMode (trig2,OUTPUT); 
pinMode (echo2,INPUT); 
pinMode (trig3,OUTPUT); 
pinMode (echo3,INPUT); 
pinMode (irpin,INPUT); 
pinMode(sensor,INPUT); 
Serial.begin(115200); 
22 
servoMotor1.attach(servo1); 
servoMotor2.attach(servo2); 
servoMotor2.write(90); 
Blynk.begin(auth,ssid,pass); 
timer.setInterval(500L,sendSensor); 
} 
void loop() { 
Blynk.run(); 
timer.run(); 
digitalWrite(trig1,LOW); 
delayMicroseconds(2); 
digitalWrite(trig1,HIGH); 
delayMicroseconds(10); 
digitalWrite(trig1,LOW); 
long duration = pulseIn(echo1,HIGH); 
long distance = duration*0.034/2; 
Serial.print("Distance = "); 
Serial.print(distance); 
Serial.println("cm"); 
delay(200); 
if(distance<10){ 
servoMotor1.write(180); 
}else{ 
servoMotor1.write(0); 
} 
int irval=digitalRead(irpin); 
long value = analogRead(sensor); 
long moisture = map(value, 0, 1024, 100, 0); 
Serial.print("Moisture ="); 
Serial.println(moisture); 
delay(2000); 
if (irval == LOW && moisture > 2){ 
servoMotor2.write(0); 
23 
delay(2000); 
servoMotor2.write(90);  
}else if(irval == LOW && moisture <  50){ 
servoMotor2.write(180); 
delay(2000); 
servoMotor2.write(90);  
} 
} 
void sendSensor(){ 
long duration1, distance1, duration2, distance2;  
digitalWrite(trig2,LOW); 
delayMicroseconds(2); 
digitalWrite(trig2,HIGH); 
delayMicroseconds(10); 
digitalWrite(trig2,LOW); 
duration1 = pulseIn(echo2,HIGH); 
distance1 = duration1*0.034/2; 
Serial.print("Distance2 = "); 
Serial.println(distance1); 
Serial.print("cm"); 
Blynk.virtualWrite(V0,distance1); 
delay(200); 
if(distance1>=5){ 
Blynk.logEvent("dustbin_alert1","dry bin is full"); 
} 
digitalWrite(trig3,LOW); 
delayMicroseconds(2); 
digitalWrite(trig3,HIGH); 
delayMicroseconds(10); 
digitalWrite(trig3,LOW); 
duration2 = pulseIn(echo3,HIGH); 
distance2 = duration2*0.034/2; 
Serial.print("Distance3 = "); 
24 
Serial.println(distance2); 
Serial.print("cm"); 
Blynk.virtualWrite(V1,distance2); 
delay(200); 
if(distance2>=5){ 
Blynk.logEvent("dustbin_alert2","wet bin is full"); 
} 
}
