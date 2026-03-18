# fire-detection-esp32
Fire detection system using ESP32 and flame sensor
#include <WiFi.h>
#include <HTTPClient.h>

// WiFi Credentials
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

// IFTTT
String server = "http://maker.ifttt.com/trigger/fire_alert/with/key/YOUR_IFTTT_KEY";

// Flame Sensor Pin
#define FLAME_SENSOR 34  // GPIO34 (Analog)

void setup() {
  Serial.begin(115200);

  pinMode(FLAME_SENSOR, INPUT);

  // Connect WiFi
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nConnected!");
}

void loop() {
  int sensorValue = analogRead(FLAME_SENSOR);

  Serial.print("Sensor Value: ");
  Serial.println(sensorValue);

  // Adjust threshold based on your sensor
  if (sensorValue < 1000)  // Fire detected
  {
    Serial.println("🔥 FIRE DETECTED!");

    sendAlert();
    delay(10000); // Avoid spam
  }

  delay(500);
}

void sendAlert() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    http.begin(server);
    int httpResponseCode = http.GET();

    if (httpResponseCode > 0) {
      Serial.println("Alert Sent Successfully!");
    } else {
      Serial.println("Error sending alert");
    }

    http.end();
  }
}
