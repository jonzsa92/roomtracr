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

![20250425_092844](https://github.com/user-attachments/assets/0971e46d-3752-491f-a67b-c37827d6318b)


Kytke GND kohdassa oleva johdin ESP32:n GND kohtaan. Tässä tapauksessa musta johto.

Kytke D1 kohdassa oleva johdin ESP32:n IO13 kohtaan. Tässä tapauksessa keltainen johto.

![20250425_092826](https://github.com/user-attachments/assets/7ad6cba5-8a41-4491-b02e-6ce94fdac222)


Kytke VCC kohdassa oleva johdin ESP32:n 3V3 kohtaan. Tässä tapauksessa punainen johto.

![20250425_092835](https://github.com/user-attachments/assets/7630b3f6-aa29-46cb-bfb3-ada71e3959b1)

Yhdistettynä, pitäisi näyttää tältä.

![20250425_092819](https://github.com/user-attachments/assets/5291fc5b-9ce3-437b-8fb2-3762ab408143)
![20250425_092815](https://github.com/user-attachments/assets/991e40ca-69bb-47c1-b972-3537296c59b8)


# Koodi

Lataa Arduino IDE ohjelma. Asenna se.
Ensin piirilevyn asennus. Arduino IDE:ssä -> Tools -> Board -> Board Manager. Hakukenttään laita esp32. Lataa (varmuuden vuoksi) ne mitkä löytyvät hakukentässä. Itsellä oli nämä: 

![whatesp32](https://github.com/user-attachments/assets/fd65b3e2-31b9-4208-93ed-cbcf988e2fe4)


Jos sinulla ei vielä ole ajureita, voit lataa ne täältä: https://sparks.gogo.co.nz/ch340.html. Sinä et niitä välttämättä tarvitse, mutta oma levyni ei toiminut ilman niitä. 

Kun olet asentanut levyt IDEssä, valitse Tools -> Board -> ja listasta valitset omasi, tai ESP32 Dev Module. Minulla tässä tapauksessa Wemos D1 R32.

![boardsmanager](https://github.com/user-attachments/assets/dee052dc-50ba-4347-a79a-9df5ff750d42)


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
Tallenna projekti. Kytke piirilevysi USB-kaapelilla kiinni. Mene Laitehallintaan (Device Manager), ja etsi sieltä oikea COM portti. Tässä tapauksessa COM3. 

![devicemanager](https://github.com/user-attachments/assets/b3ddc9f3-04c0-4b38-a918-f73cdc64c912)


Arduino IDEssä: Valitse Tools -> Port -> oma porttisi, tässä tapauksessa COM3.

![whatport](https://github.com/user-attachments/assets/fe36e34d-9b05-4898-8034-f4257b6e59c7)


Paina Upload.

Jos kaikki meni oikein, avaa Tools -> Serial Monitor. Tämä avaa terminal näkymän, ja syöttö pitäisi olla tämän mukaista.

![output](https://github.com/user-attachments/assets/3bf171c5-5d20-49e6-a989-686aeb4982f6)


Jos näin, onneksi olkoon! Sinulla on nyt toimiva liiketunnistin!

Troubleshooting: WIP

