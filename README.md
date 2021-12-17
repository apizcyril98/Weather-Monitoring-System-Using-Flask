# IoTproject
A simple IoT project for Software Engineering course (SKEL413) on a weather monitoring system using M5stickC with DHT11 sensor to obtain the data

## IoT Weather Monitoring System

### Problem Statement

Weather conditions have an impact on human activity, and weather monitoring can aid in activity control. It is critical to keep an eye on and monitor the weather patterns in the area. Users have limited access to weather information such as temperature, humidity, and heat index. Users will not be notified about  heat waves, or any other weather-related emergency if they do not have access to a weather station.

Furthermore, producing weather forecasts without data is challenging. When a person uses a weather station, they can also view the information's history. The trends in the measurements can be determined by the user. The user will be able to examine patterns more effectively as a result of this.

![Case Diagram](https://i.ibb.co/mt1dCW2/image1.jpg)

### System Architecture

Here are the general overview of the system architecture of our IoT weather monitoring system. For this project we will be using the M5STICKC for the device and it will be connected to DHT11 sensor to obtain temperature, humidity, and heat index data. The device will communicate using MQTT data protocol transmission for the data transmission and it will send the data to Moquitto broker then to Heroku Cloud platform and finally update the data on our simple dashboard app which we will be using google web dashboard that we will create later.

![system architecture](https://i.ibb.co/68TG5rx/Capture.jpg)

### Sensor

#### Proposed Device: M5STICKC

![M5](https://images-na.ssl-images-amazon.com/images/I/51ykxk9ZYoL.jpg)

#### Proposed Data Transmission Protocol : MQTT

#### Code Sample

<details>
  <summary>Click to expand!</summary>

```

#include <WiFi.h>
#include "DHT.h"
#include <HTTPClient.h>
#define DHTPIN 26     // DHT sensor pin
float h = 0;
float t = 0;
// Replace with your network credentials
const char* ssid     = "YOUR SSID NAME";
const char* password = "YOUR NETWORK PASSWORD";

#define DHTTYPE DHT11   // DHT 11

DHT dht(DHTPIN, DHTTYPE);

// Set web server port number to 80
WiFiServer server(80);

// Variable to store the HTTP request
String header;

void setup() {
  Serial.begin(115200);
  pinMode(4,OUTPUT);
  pinMode(2,OUTPUT);
  dht.begin();
  // Connect to Wi-Fi network with SSID and password
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  // Print local IP address and start web server
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  server.begin();
}

void loop() {
  // put your main code here, to run repeatedly:
  if (isnan(h) || isnan(t)) {
    h = random(60,78);
    t = random(28,31);
  } else {
    h = dht.readHumidity();
    t = dht.readTemperature();
  }

  HTTPClient http;
  //send channel data with data reference name and data for example: temp=32
  //You can send multiple data separated by & for example: temp=32$hum=67
  //dont forget to include api(api key) and id (device id)
  
  //Example url for channel data and controllers data both can be requested at the same http request url
  //replace API_KEY and DEVICE_ID with your own at io.circuits.my 
  //any api request will be using api.circuits.my

  String api_key = "Put your API key";
  String device_id = "Put your device ID";

  //For display data only without control.

  HTTPClient http;
  String httpData = "http://api.circuits.my/request.php?api=" + api_key + "&id=" + device_id + "&temp=" + String(t) + "&hum=" + String(h);
  http.begin(httpData); //Specify the URL
  int httpResponsCode = http.GET(); //Make the request
  if (httpResponsCode > 0) { //Check for the returning code
    String payload = http.getString();
    Serial.println(httpResponsCode);
    Serial.println(payload);
  }

  else {
    Serial.println("Error on HTTP request");
  }
  http.end(); //Free the resources
  delay(3000);
}

```
</details>
  
<img src="https://i.ibb.co/1m4fcFt/Whats-App-Image-2021-12-15-at-20-33-40.jpg" alt="sample" width="400"/> 
  
### Cloud Platform

Backend Framework: Flask

Cloud Hosting Platform: Heroku

URL of our Flask App: https://weather-m3.herokuapp.com/

This is the [video](https://www.youtube.com/watch?v=0j9s8jk-LtA&ab_channel=MOHDHAFEEZSHAHRIL) of how we deploy Flask app to Heroku

### Dashboard

This is the prototype dashboard that we will be creating later using Google Data Studio. It will display the temperature, humidity and heat index and also simple data like date and day.

![Dashboard](https://i.ibb.co/LSsG0yz/dashboard.jpg)
