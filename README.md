#include <Arduino.h>
#include <DHT.h>

#define DHTPIN 4
#define DHTTYPE DHT22

#define RELAY_KIPAS 18
#define BUZZER 19

DHT dht(DHTPIN, DHTTYPE);

float suhu = 0;
float kelembaban = 0;

void setup()
{
    Serial.begin(115200);

    pinMode(RELAY_KIPAS, OUTPUT);
    pinMode(BUZZER, OUTPUT);

    digitalWrite(RELAY_KIPAS, HIGH);
    digitalWrite(BUZZER, LOW);

    dht.begin();

    Serial.println("===================================");
    Serial.println("MONITORING SUHU KANDANG TERNAK");
    Serial.println("ESP32 + DHT22 + Relay + Buzzer");
    Serial.println("===================================");
}

void loop()
{
    suhu = dht.readTemperature();
    kelembaban = dht.readHumidity();

    if (isnan(suhu) || isnan(kelembaban))
    {
        Serial.println("Sensor DHT22 gagal dibaca!");
        delay(2000);
        return;
    }

    Serial.println("-----------------------------------");
    Serial.print("Suhu       : ");
    Serial.print(suhu);
    Serial.println(" C");

    Serial.print("Kelembaban : ");
    Serial.print(kelembaban);
    Serial.println(" %");

    if (suhu > 32)
    {
        digitalWrite(RELAY_KIPAS, LOW);
        digitalWrite(BUZZER, HIGH);

        Serial.println("STATUS : TERLALU PANAS");
        Serial.println("KIPAS  : ON");
        Serial.println("ALARM  : ON");
    }
    else if (suhu < 18)
    {
        digitalWrite(RELAY_KIPAS, HIGH);

        digitalWrite(BUZZER, HIGH);
        delay(500);
        digitalWrite(BUZZER, LOW);

        Serial.println("STATUS : TERLALU DINGIN");
        Serial.println("KIPAS  : OFF");
        Serial.println("PERLU LAMPU PEMANAS");
    }
    else
    {
        digitalWrite(RELAY_KIPAS, HIGH);
        digitalWrite(BUZZER, LOW);

        Serial.println("STATUS : NORMAL");
        Serial.println("KIPAS  : OFF");
        Serial.println("ALARM  : OFF");
    }

    delay(3000);
}
