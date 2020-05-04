#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include "DHT.h"        // including the library of DHT11 temperature and humidity sensor
#define DHTTYPE DHT11   // DHT 11
#define dht_dpin D6
DHT dht(dht_dpin, DHTTYPE); 
int t=0;
char data[80];
//data = "abc";
const char* ssid = "Ghita";
const char* password =  "placinta28";
const char* mqttServer = "mqtt.beia-telemetrie.ro";
const int mqttPort = 1883;
const char* mqttUser = "";
const char* mqttPassword = "";
const char* topic = "training/ESP/Daniela-Draghici";
 
WiFiClient espClient;
PubSubClient client(espClient);
 
void setup() {
 
  Serial.begin(9600);
 
  WiFi.begin(ssid, password);
  dht.begin();
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.println("Connecting to WiFi..");
  }
  Serial.println("Connected to the WiFi network");
 
  client.setServer(mqttServer, mqttPort);
  //client.setCallback(callback);
 
  while (!client.connected()) {
    Serial.println("Connecting to MQTT...");
 
    if (client.connect("ESP8266Client", mqttUser, mqttPassword )) 
    {
 
      Serial.println("connected");  
 
    } 
    else {
 
      Serial.print("failed with state ");
      Serial.print(client.state());
      delay(2000);
 
    }
  }
}
 void loop() {
     float h = dht.readHumidity();
     float t = dht.readTemperature();
     String load=String(h);
     String loadd=String(t);
     String data1="{\"TEMP\":"+loadd+','+"\"HUMID\":"+load+'}';
    data1.toCharArray(data,(data1.length()+1));
    client.publish("training/ESP/Daniela-Draghici",data);   //{"TEMP":24,"HUMID":30}  24:, "HUMID":30}
  delay(5000);  
}
