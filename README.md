# HEALTH-MONITORING-SYSTEM-USING-INTERNET-OF-THINGS-IOT

INTRODUCTION:
With tons of new healthcare technology start-ups, IOT is rapidly revolutionizing the healthcare industry. In this project, we have designed the IOT Based Patient Health Monitoring System using ESP8266 WIFI MODULE. The IOT platform used in this project is Thing Speak. Thing Speak is an open-source Internet of Things (IOT) application and API to store and retrieve data from things using the HTTP protocol over the Internet or via a Local Area Network. This IOT device could read the pulse rate and measure the surrounding temperature. It continuously monitors the pulse rate and surrounding temperature and updates them to an IOT platform. The NODE MCU Sketch running over the device implements the various functionalities of the project like reading sensor data, converting them into strings, passing them to the IOT platform, and displaying measured pulse rate and temperature on character LCD.


The  below link is for the references:
https://drive.google.com/file/d/1Ja2QvRwwh8BG6ifiOFEFvfzY6yZDonGo/view?usp=sharing

CODE:
This Code is written in the software known as “Arduino IDE”. Complied using the same software and uploaded by the IDE protocol by selecting Port & Board.

CODE: 
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
char auth[] = "e94494f93bf5451595c0b4e5a03e414c";

#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_Sensor.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 64 // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

#include "DHT.h"
#define DHTPIN 2 
#define DHTTYPE DHT11 
DHT dht(DHTPIN, DHTTYPE);

char ssid[] = "Sagar";
char pass[] = "AndroidAP1234";
int count=0;
WidgetLCD lcd(V3);

void setup()
{
  Serial.begin(9600);
  Serial.println(F("DHTxx test!"));
  dht.begin();

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  delay(2000);
  display.clearDisplay();
  display.setTextColor(WHITE);
 
  Blynk.begin (auth, ssid, pass);
}

void loop()
{
  dhtSensor();
  Pulse();
  BPread();
  Blynk.run();
}
void BPread()
{
  if(digitalRead(16)==1)
  {
    count++;
    delay(100);
  }
if(count==1)
{
  lcd.clear();
  lcd.print(0,0, "BP 100");
  delay(500);
}
else if(count==2)
{
  lcd.clear();
  lcd.print(0,0, "BP 130");
  delay(500);
}
else if(count==3)
{
  lcd.clear();
  lcd.print(0,0, "BP 140-150");
  delay(500);
}
else if(count==5)
{
  lcd.clear();
  lcd.print(0,0, "BP 90");
  delay(500);
}
else if(count==6)
{
  lcd.clear();
  lcd.print(0,0, "BP 120-130");
  delay(500);
}
else if(count==7)
{
  lcd.clear();
  lcd.print(0,0, "BP 70");
  delay(500);
}
else if(count==8)
{
  lcd.clear();
  lcd.print(0,0, "BP 130");
  delay(500);
}
else
{
  lcd.clear();
  lcd.print(0,0, "BP Moniter Systm");
  delay(500);
}
}

void Pulse()
{
 Serial.println(analogRead(A0));
 Blynk.virtualWrite(V2,analogRead(A0));
  delay(10);

}

void dhtSensor()
{
  delay(20);

  // Reading temperature or humidity takes about 250 milliseconds!
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  float h = dht.readHumidity();
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();
  // Read temperature as Fahrenheit (isFahrenheit = true)
  float f = dht.readTemperature(true);

  // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t) || isnan(f)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }

  // Compute heat index in Fahrenheit (the default)
  float hif = dht.computeHeatIndex(f, h);
  // Compute heat index in Celsius (isFahreheit = false)
  float hic = dht.computeHeatIndex(t, h, false);

  Serial.print(F("Humidity: "));
  Serial.print(h);
  Blynk.virtualWrite(V1,h);
  Serial.print(F("%  Temperature: "));
  Serial.print(t);
  Blynk.virtualWrite(V0,t);
  Serial.print(F("°C "));
  Serial.print(f);
  Serial.print(F("°F  Heat index: "));
  Serial.print(hic);
  Serial.print(F("°C "));
  Serial.print(hif);
  Serial.println(F("°F"));

  display.clearDisplay();
  
  // display temperature
  display.setTextSize(1);
  display.setCursor(0,0);
  display.print("Temperature: ");
  display.setTextSize(2);
  display.setCursor(0,10);
  display.print(t);
  display.print(" ");
  display.setTextSize(1);
  display.cp437(true);
  display.write(167);
  display.setTextSize(2);
  display.print("C");
  
  // display humidity
  display.setTextSize(1);
  display.setCursor(0, 35);
  display.print("Humidity: ");
  display.setTextSize(2);
  display.setCursor(0, 45);
  display.print(h);
  display.print(" %"); 
  
  display.display(); 
}

