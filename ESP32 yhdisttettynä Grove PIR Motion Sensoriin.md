## ESP32 yhdisttettynä Grove PIR Motion Sensoriin

# Laitteet käytössä:
Wemos D1 R32
Grove PIR Motion Sensor

#Muut vaadittavat asiat:
Grove to M hyppyrijohdin
microUSB – USB - kaapeli
(Windows) Arduino IDE

#Laitteiden yhdistäminen
Yhdistä Grove johdon pääty liiketunnistimeen. Katso piirin alta, mihin kohtaan mikäkin johto osuu. 

kuva

Kytke GND kohdassa oleva johdin ESP32:n GND kohtaan. Tässä tapauksessa musta johto.
Kuvat
Kytke VCC kohdassa oleva johdin ESP32:n 3V3 kohtaan. Tässä tapauksessa punainen johto.
Kuva
Kytke D1 kohdassa oleva johdin ESP32:n IO13 kohtaan. Tässä tapauksessa keltainen johto.
Kuva

#Koodi

Lataa Arduino IDE ohjelma. Asenna se.
Ensin piirilevyn asennus. Arduino IDE:ssä -> Tools -> Board -> Board Manager. Hakukenttään laita esp32. Lataa (varmuuden vuoksi) ne mitkä löytyvät hakukentässä. Itsellä oli nämä: 

kuva

Jos sinulla ei vielä ole ajureita, voit lataa ne täältä: https://sparks.gogo.co.nz/ch340.html. Sinä et niitä välttämättä tarvitse, mutta oma levyni ei toiminut ilman niitä. 

Kun olet asentanut levyt IDEssä, valitse Tools -> Board -> ja listasta valitset omasi, tai ESP32 Dev Module. Minulla tässä tapauksessa Wemos D1 R32.

kuva

Kirjoita projektin koodi-kenttään tämä koodi:
```
const int PIR_SENSOR_OUTPUT_PIN = 13;  /* PIR sensor O/P pin */
int warm_up;

void setup() {
  pinMode(PIR_SENSOR_OUTPUT_PIN, INPUT);
  Serial.begin(115200); /* Define baud rate for serial communication */
  Serial.println("Waiting For Power On Warm Up");
  delay(20000); /* Power On Warm Up Delay */
  Serial.println("Ready!");
}

void loop() {
  int sensor_output;
  sensor_output = digitalRead(PIR_SENSOR_OUTPUT_PIN);
  if( sensor_output == LOW )
  {
    if( warm_up == 1 )
     {
      Serial.print("Warming Up\n\n");
      warm_up = 0;
      delay(2000);
    }
    Serial.print("No object in sight\n\n");
    delay(1000);
  }
  else
  {
    Serial.print("Object detected\n\n");   
    warm_up = 1;
    delay(1000);
  } 
}
```
Tallenna projekti. Kytke piirilevysi USB-kaapelilla kiinni. Mene Laitehallintaan (Device Manager), ja etsi sieltä oikea COM portti. Tässä tapauksessa COM3. Arduino IDEssä: Valitse Tools -> Port -> oma porttisi, tässä tapauksessa COM3. Paina Upload.

Jos kaikki meni oikein, avaa Tools -> Serial Monitor. Tämä avaa terminal näkymän, ja syöttö pitäisi olla tämän mukaista.
kuva

Jos näin, onneksi olkoon! Sinulla on nyt toimiva liiketunnistin!

