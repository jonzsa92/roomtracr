# ESP32:n liiketunnistimen datan lähetys nettiin.

1. Tehdään uusi tietokanta (esim. Pukki) keräämään ESP32:n data.
2. Tehdään REST API saamaan data ja kirjoittamaan se tietokantaan
3. ESP32 lähettää PIR datan HTTP POST:illa

main.py (esimerkki)
```
from fastapi import FastAPI, Request
import psycopg2
from datetime import datetime
import os

app = FastAPI()

# Pukki DB connection
conn = psycopg2.connect(
    dbname="your_db",
    user="your_user",
    password="your_password",
    host="your_hostname",
    port="5432"
)
cursor = conn.cursor()

@app.post("/motion")
async def log_motion(request: Request):
    data = await request.json()
    motion = data.get("motion", 0)

    cursor.execute(
        "INSERT INTO motion_logs (motion, timestamp) VALUES (%s, %s)",
        (bool(motion), datetime.utcnow())
    )
    conn.commit()
    return {"status": "ok"}
```

Arduino sketch (esimerkki)
```
#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "your_wifi";
const char* password = "your_pass";
const char* serverUrl = "https://your-app.rahtiapp.fi/motion";

#define PIR_PIN 13

void setup() {
  Serial.begin(115200);
  pinMode(PIR_PIN, INPUT);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected");
}

void loop() {
  int motion = digitalRead(PIR_PIN);

  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    http.begin(serverUrl);
    http.addHeader("Content-Type", "application/json");

    String payload = "{\"motion\":" + String(motion) + "}";
    int httpResponseCode = http.POST(payload);

    if (httpResponseCode > 0) {
      Serial.print("HTTP Response: ");
      Serial.println(httpResponseCode);
    } else {
      Serial.print("Error code: ");
      Serial.println(httpResponseCode);
    }

    http.end();
  }

  delay(5000); // wait 5 seconds
}
```
