# Alcohol-Detector
This project is a real-time alcohol detection system using an ESP32 and MQ-3 sensor. It triggers a buzzer and LED on detection, displays live readings on a mobile web dashboard via the ESP32 WebServer, and sends remote push notifications through Blynk IoT cloud integration.

#define BLYNK_TEMPLATE_ID "TMPL3wZjMC9kJ"
#define BLYNK_TEMPLATE_NAME "ALCOHOL DETECTOR"
#define BLYNK_AUTH_TOKEN "CXVjLRCigY4CpzG-evyMiQckeG1bNkPv
#include <WiFi.h>
#include <BlynkSimpleEsp32.h>

char ssid[] = "Itsme";
char pass[] = "12341234";

#define MQ3_PIN 34
#define BUZZER_PIN 15
#define LED_PIN 2
#define THRESHOLD 1500 // Adjust after calibration

BlynkTimer timer;

void sendSensorData() {
  int sensorValue = analogRead(MQ3_PIN);
  Serial.println(sensorValue);

  Blynk.virtualWrite(V0, sensorValue); // Gauge widget

  if (sensorValue > THRESHOLD) {
    digitalWrite(BUZZER_PIN, HIGH);
    digitalWrite(LED_PIN, HIGH);
    Blynk.virtualWrite(V1, 1); // Alert LED widget
    Blynk.logEvent("alcohol_detected", "High alcohol level detected!");
  } else {
    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(LED_PIN, LOW);
    Blynk.virtualWrite(V1, 0);
  }
}

void setup() {
  Serial.begin(115200);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(LED_PIN, OUTPUT);

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);
  timer.setInterval(1000L, sendSensorData); // Every 1 second
}

void loop() {
  Blynk.run();
  timer.run();
}
