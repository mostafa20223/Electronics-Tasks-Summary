# Tasks Code Illustration
# Layout
* <a href="#A"> Task1 - ESP Hello World! (Vital Signal Monitoring via Wireless) </a>
* <a href="#B"> Task2 - GPS Module (Localization) </a>
* <a href="#C"> Task3 - Autonomous Vehicle (Self Driving Car) </a>

<h1 id="A"> Task1 - ESP Hello World! (Vital Signal Monitoring via Wireless) </h1>
<h3> Arduino Code </h3>

``` C
#include <ESP8266WiFi.h>  // esp8266 library
#include <FirebaseArduino.h>  // firebase library
#include <DHT.h>  // dht11 temperature and humidity sensor library

#define FIREBASE_HOST "fir-iot-90928.firebaseio.com"  // project name address from firebase id
#define FIREBASE_AUTH "xvAXttUWcq0hlVihXWCOsM7mmTShY0LNcUIZAwCC"  // secret key generated from firebase

#define WIFI_SSID "Vodafone_VDSL_9A52"  // wifi name 
#define WIFI_PASSWORD "HanaaYehia116" // wifi password
 
#define DHTPIN D2 // what digital pin we're connected to
#define DHTTYPE DHT11 // select dht type as DHT11 or DHT22
DHT dht(DHTPIN, DHTTYPE); // declaration of DHT function

void setup() {
  Serial.begin(9600); // baud rate
  delay(1000);                
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD); // try to connect with wifi
  Serial.print("Connecting to ");
  Serial.print(WIFI_SSID);
  while (WiFi.status() != WL_CONNECTED) { // check connected or not
    Serial.print(".");
    delay(500);
  }
  Serial.println();
  Serial.print("Connected to ");
  Serial.println(WIFI_SSID);
  Serial.print("IP Address is : ");
  Serial.println(WiFi.localIP()); // print local IP address
  Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH); // connect to firebase
  dht.begin(); // start reading dht sensor
}

void loop() {
  // reading temperature or humidity takes about 250 milliseconds!
  float h = dht.readHumidity(); // read humidity
  float t = dht.readTemperature(); // read temperature as Celsius (the default)
  if (isnan(h) || isnan(t)) { // check if any reads failed and exit early (to try again).
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  Serial.print("Humidity: ");
  Serial.print(h);
  float fireHumid = h; // convert integer humidity to float humidity
  Serial.print("%  Temperature: ");
  Serial.print(t);
  Serial.println("Â°C ");
  float fireTemp = t; // convert integer temperature to float temperature
  delay(200);
  // setup path and send readings
  Firebase.setFloat("/FirebaseIOT/humidity", fireHumid);
  Firebase.setFloat("/FirebaseIOT/temperature", fireTemp);
}
```

<h3> Python Code </h3>

``` Python
# importing libraries for Pyqt5
from PyQt5 import QtWidgets, QtCore
from PyQt5.QtWidgets import QTableWidgetItem
# using modules/classes from qt-designer app
from task1 import Ui_MainWindow
# system library in python to build the app and qdarkstyle for more stylish to the app
import sys, qdarkstyle
# library to initialize or establish connetion between app and firebase
import pyrebase

# config dictionary variable to set configuration for firebase connection as in Arduino
config = {
  "apiKey": "AIzaSyDkv7Tp5OyLd9XKz1LSuZbo56sZ5XAu3s8",
  "authDomain": "fir-iot-90928.firebaseapp.com",
  "databaseURL": "https://fir-iot-90928.firebaseio.com",
  "storageBucket": "fir-iot-90928.appspot.com"
  }
    
class ApplicationWindow(QtWidgets.QMainWindow):
  def __init__(self):
    super(ApplicationWindow, self).__init__()
    self.ui = Ui_MainWindow()
    self.ui.setupUi(self)
    self.temperatureData = [] # empty array to store temperature readings
    self.humidityData = [] # empty array to store humidity readings
    # establish a timer to be called every 50 ms to update readings
    self.timer = QtCore.QTimer()
    self.timer.setInterval(50)
    self.timer.start()
    self.timer.timeout.connect(self.updateData)
    # set environment of qwidget to plot upcoming readings (Title, Aspect Ratio)
    self.ui.TEMPERATURE_GRAPH.setLabels(title = "TEMPERATURE")
    self.ui.HUMIDITY_GRAPH.setLabels(title = "HUMIDITY")
    self.ui.TEMPERATURE_GRAPH.setYRange(20, 100)
    self.ui.HUMIDITY_GRAPH.setYRange(40, 120)
    
  # function to get the current value of temperature and humidity
  def updateData(self):
    firebase = pyrebase.initialize_app(config) # initalize communication with firebase server
    db = firebase.database() # create instane of real-time database in our firebase server
    data = [db.child("FirebaseIOT/temperature").get(), db.child("FirebaseIOT/humidity").get()] # access temperature and humidity values and append them into data array
    self.temperatureData.append(data[0].val()) # append temperature values to temperatureData array
    self.humidityData.append(data[1].val()) # append humidity values to humidityData array
    self.plotData() # call plotData function to start plotting

  # function to plot the readings of temperature and humidity
  def plotData(self):
    self.ui.TEMPERATURE_GRAPH.plot(self.temperatureData)
    self.ui.HUMIDITY_GRAPH.plot(self.humidityData)
    self.ui.temperature_data.addItem(str(self.temperatureData[-1])) # only the last value
    self.ui.humidity_data.addItem(str(self.humidityData[-1])) # only the last value

# main function to be called
def main():
  app = QtWidgets.QApplication(sys.argv)
  app.setStyleSheet(qdarkstyle.load_stylesheet_pyqt5())
  application = ApplicationWindow()
  application.show()
  app.exec_()

# start the app
if __name__ == "__main__":
  main()
```

<h1 id="B"> Task2 - GPS Module (Localization) </h1>
<h3> Meteor App (Client Side - Layout) </h3>

``` HTML
<!-- set title and style of the app to be responsive -->
<head>
  <title>Localization</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
</head>
<!-- main app inside the body -->
<body>
  <div class="container-fluid">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarTogglerDemo01" aria-controls="navbarTogglerDemo01" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarTogglerDemo01">
        <a class="navbar-brand" href="#">Welcome to our GPS!</a>
        <ul class="navbar-nav mr-auto mt-2 mt-lg-0">
          <li class="nav-item">
            <a class="nav-link" href="https://electronics-93d2b.firebaseio.com/">Show DataBase</a>
          </li>
        </ul>
      </div>
    </nav>
    <h1>Your Location!</h1>
    {{> map}} <!-- calling the template to show the map -->
  </div>
  <!-- add interactive properties for page using javascript and jquery -->
  <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/js/bootstrap.min.js" integrity="sha384-w1Q4orYjBQndcko6MimVbzY0tgp4pWB4lZ7lr30WKz0vr/aWKhXdBNmNb5D92v7s" crossorigin="anonymous"></script>
</body>

<!-- template contains the map of department floor -->
<template name="map">
  <div id="location" class="animation css"></div>
  <div class="image" style="text-align: center;">
    <img class="img-fluid" id="img" src='Map.png' alt='Position Detection'/>
  </div>
</template>
```

<h3> Meteor App (Client Side - Style) </h3>

``` CSS
/* main style for the body (outlines of app) */
body {
  padding: 10px; /* space in-between of elements */
  font-family: sans-serif; /* font type */
}

/* properties for the point to be moved to */
#location {
  position: relative; /* flexibility to move */
  width: 15px; /* main width */
  height: 15px; /* main height */
  border-radius: 50%; /* circle point with a specific radius */
  background-color: darkblue; /* point color */
  transition: all 2s linear; /* transition (animation) type of point */
}

/* adjust dimensions of the map image */
.image {
  width: 632px; /* main width */
  height: 753px; /* main height */
}
```

<h3> Meteor App (Client Side - Bahavior) </h3>

``` JavaScript
// importing layout to work on
import './main.html';

// firebase variable to be used when location changes
var firebase = require("firebase/app");

// allow access to the usage of firebase variable
require("firebase/auth");
require("firebase/firestore");
require("firebase/database");

// firebaseConfig object adjust the configuration of firebase application (similar to Python)
var firebaseConfig = {
  apiKey: "AIzaSyBOoM5r29oPhSkFyBUQVCv4XzpM1lzGa5I",
  authDomain: "electronics-93d2b.firebaseapp.com",
  databaseURL: "https://electronics-93d2b.firebaseio.com",
  projectId: "electronics-93d2b",
  storageBucket: "electronics-93d2b.appspot.com",
  messagingSenderId: "601948409562",
  appId: "1:601948409562:web:fff3008d05cdecf14a5fe3",
  measurementId: "G-1J3SSCFF3X"
};

// initialize connection between app and firebase real-time database
firebase.initializeApp(firebaseConfig);
// destination variable to determine where to go on map
var destination = firebase.database().ref().child("/Result/location");
// key and val variables simulate specific location
var key, val;
// locations constant variable to define location of different places in map
const locations = {
  0: { top: '435px', left: '140px' }, // Electronics Lab
  1: { top: '275px', left: '430px' }, // Corridor1
  2: { top: '530px', left: '430px' }, // Corridor2
  3: { top: '65px', left: '155px' }, // Elevator
  4: { top: '80px', left: '430px' }, // Corridor0
  5: { top: '150px', left: '175px' }, // TAs Office
  6: { top: '655px', left: '165px' } // Shared Office / HallWay
};

// function to be called when location changes to make the point go to the current place
destination.on('value', function(snapshot) {
  key = snapshot.key; // name of destination as in key variable
  val = snapshot.val(); // destination value to be accessed from locations constant variable
  // print them to be sure that function work well
  console.log(key, val);
  console.log(locations[val]);
  // make the point moves to the current place
  document.getElementById("location").style.top = locations[val]['top'];
  document.getElementById("location").style.left = locations[val]['left'];
});
```

<h1 id="C"> Task3 - Autonomous Vehicle (Self Driving Car) </h1>
<h3> Arduino Code (Car) </h3>

``` C
#define ENA   14          // Enable/speed motors Right        GPIO14(D5)
#define ENB   12          // Enable/speed motors Left         GPIO12(D6)
#define IN_1  15          // L298N in1 motors Right           GPIO15(D8)
#define IN_2  13          // L298N in2 motors Right           GPIO13(D7)
#define IN_3  2           // L298N in3 motors Left            GPIO2(D4)
#define IN_4  0           // L298N in4 motors Left            GPIO0(D3)

#include <ESP8266WiFi.h>
#include <WiFiClient.h> 
#include <ESP8266WebServer.h>

//String command;             //String to store app command state.
int speedCar = 800;         // 400 - 1023.

const char* ssid     = "STUDBME2";
const char* password = "BME2Stud";
ESP8266WebServer server(80);

void goAhead() {
  digitalWrite(IN_2, LOW); digitalWrite(IN_1, HIGH); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, LOW); digitalWrite(IN_4, HIGH); analogWrite(ENB, speedCar);
}

void goBack() {
  digitalWrite(IN_2, HIGH); digitalWrite(IN_1, LOW); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, HIGH); digitalWrite(IN_4, LOW); analogWrite(ENB, speedCar);
}

void goAheadRight() {
  digitalWrite(IN_2, LOW); digitalWrite(IN_1, HIGH); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, LOW); digitalWrite(IN_4, LOW); analogWrite(ENB, speedCar);
}

void goAheadLeft() {
  digitalWrite(IN_2, LOW); digitalWrite(IN_1, LOW); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, LOW); digitalWrite(IN_4, HIGH); analogWrite(ENB, speedCar);
}

void goBackRight() {
  digitalWrite(IN_2, HIGH); digitalWrite(IN_1, LOW); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, LOW); digitalWrite(IN_4, LOW); analogWrite(ENB, speedCar);
}

void goBackLeft() {
  digitalWrite(IN_2, LOW); digitalWrite(IN_1, LOW); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, HIGH); digitalWrite(IN_4, LOW); analogWrite(ENB, speedCar);
}

void stopRobot() {
  digitalWrite(IN_1, LOW); digitalWrite(IN_2, LOW); analogWrite(ENA, speedCar);
  digitalWrite(IN_3, LOW); digitalWrite(IN_4, LOW); analogWrite(ENB, speedCar);
}

void setup() {
  pinMode(ENA, OUTPUT); pinMode(ENB, OUTPUT); pinMode(IN_1, OUTPUT); pinMode(IN_2, OUTPUT); pinMode(IN_3, OUTPUT); pinMode(IN_4, OUTPUT);
  Serial.begin(115200); 
  while (!Serial) {
  ; // wait for serial port to connect. Needed for native USB port only
  }

  // Trying to connect to the WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print("*");
  }
  IPAddress ip(192, 168, 130, 50);
  IPAddress gateway(192, 168, 1, 1);
  IPAddress subnet(255, 255, 255, 0);
  WiFi.config(ip, gateway, subnet);
  Serial.println(WiFi.localIP());
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  server.on("/up", goAhead);
  server.on("/left", goAheadLeft);
  server.on("/right", goAheadRight);
  server.on("/down", goBack);
  server.on("/stop", stopRobot);
  server.begin();
  server.send(200, "text/html", "<h1>You are connected</h1>");
}

void loop() {
  server.handleClient();  
}
```

<h3> Arduino Code (RFID) </h3>

``` C
// serial communication between Arduino and ESP8266 NodeMCU
#include <SPI.h>
#include <MFRC522.h>
#include <Ethernet.h>
#include<SoftwareSerial.h>
#define SS_PIN 10
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);   // Create MFRC522 instance.
SoftwareSerial esp(5, 6);

String str = "";
void setup() {
  esp.begin(115200);
  Serial.begin(9600);   // Initiate a serial communication
  SPI.begin();      // Initiate  SPI bus
  mfrc522.PCD_Init();   // Initiate MFRC522
  Serial.println("Approximate your card to the reader...");
  Serial.println();
}

void loop() {
  // Look for new cards
  if ( ! mfrc522.PICC_IsNewCardPresent()) 
  {
    return;
  }
  // Select one of the cards
  if ( ! mfrc522.PICC_ReadCardSerial()) 
  {
    return;
  }
  //Show UID on serial monitor
  Serial.print("UID tag :");
  String content;
  byte letter;
  for (byte i = 0; i < mfrc522.uid.size; i++) 
  {
     Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
     content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  str =String("coming from arduino: ") + String("RFID= ")+String(content);
  esp.println(str);
  Serial.println();
  Serial.print("Message : ");
  content.toUpperCase();
  if (content.substring(1) == "BD 31 15 2B") { // change here the UID of the card/cards that you want to give access
    Serial.println("Authorized access");
    Serial.println();
    delay(3000);
  }
 else {
    Serial.println(" Access denied");
    delay(3000);
  }
}
```

<h3> Arduino Code (Ultrasonic) </h3>

``` C
// Ultrasonic sensor variables
int Echo_1 = A4;
int Trig_1 = A5;
int Echo_2 = A0;
int Trig_2 = A1;
int trig,echo;
int rightDistance_1 = 0, rightDistance_2 = 0;
long cm_1, cm_2;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  pinMode(Echo_1, INPUT);    
  pinMode(Trig_1, OUTPUT); 
}

void loop() {
  // put your main code here, to run repeatedly:
  rightDistance_1 = Distance_test();
  rightDistance_2 = Distance_test_1();
  //inches = microsecondsToInches(rightDistance_1);
  //inches = inches +2;// Ultrasonic Calibration factor
  cm_1 = microsecondsToCentimeters(rightDistance_1);
  cm_1 = cm_1 +2; // Ultrasonic Calibration factor
  cm_2 = microsecondsToCentimeters(rightDistance_2);
  cm_2 = cm_2 +2; // Ultrasonic Calibration factor
 
  Serial.print("distance: ");
  Serial.println(cm_1);
  Serial.print("distance: ");
  Serial.println(cm_2);
  delay(2000);
}

// Ultrasonic distance measurement method
int Distance_test() {
  digitalWrite(Trig_1, LOW);   
  delayMicroseconds(2);
  digitalWrite(Trig_1, HIGH);  
  delayMicroseconds(20);
  digitalWrite(Trig_1, LOW);   
  float Fdistance = pulseIn(Echo_1, HIGH);  
  //Fdistance= Fdistance / 58;       
  return (float)Fdistance;
}
int Distance_test_1() {
  digitalWrite(Trig_2, LOW);   
  delayMicroseconds(2);
  digitalWrite(Trig_2, HIGH);  
  delayMicroseconds(20);
  digitalWrite(Trig_2, LOW);   
  float Fdistance = pulseIn(Echo_2, HIGH);  
  //Fdistance= Fdistance / 58;       
  return (float)Fdistance;
}

long microsecondsToCentimeters(long microseconds)
{
  // The speed of sound is 340 m/s or 29 microseconds per centimeter.
  // The ping travels out and back, so to find the distance of the
  // object we take half of the distance travelled.
  return microseconds / 29 / 2;
}

long microsecondsToInches(long microseconds)
{
  // According to Parallax's datasheet for the PING))), there are
  // 73.746 microseconds per inch (i.e. sound travels at 1130 feet per
  // second).  This gives the distance travelled by the ping, outbound
  // and return, so we divide by 2 to get the distance of the obstacle.
  // See: http://www.parallax.com/dl/docs/prod/acc/28015-PING-v1.3.pdf
  return microseconds / 74 / 2;
}
```

<h3> Meteor App (Client Side - Layout) </h3>

``` HTML
<head>
  <title> Smart Car </title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
</head>

<body>
  <div class="container">
    <div class="row">
      <div class="col-sm align-self-center" align="center">
        <button id="ESP" type="button" class="btn btn-info btn-primary"> Connect to ESP server! </button>
      </div>
      <div id="ESP_Status" class="col-sm" align="center">
        <div class="alert alert-primary" role="alert">
          <span id="esp"> </span>
        </div>
      </div>
    </div>
    
    <div class="row">
      <div class="col-sm">
        <h1 align="center" class="display-3 h1"> Manual </h1>
        <!-- <button id="manual" type="button" class="btn btn-info"> Manual Mode </button> -->
      </div>
    </div>
    
    <div class="row">
      <div class="col-sm align-self-center">
        <div class='up_arrow_cont'>
          <button id="up" type="button" class="btn btn-outline-primary"> &uarr; </button>
        </div>
        <div class="left_right_arrow_cont">
          <button id="left" type="button" class="btn btn-outline-primary"> &larr; </button>
          <button id="stop" type="button" class="btn btn-outline-primary"> &#9632; </button>
          <button id="right" type="button" class="btn btn-outline-primary"> &rarr; </button>
        </div>
        <div class='down_arrow_cont'>
          <button id="down" type="button" class="btn btn-outline-primary"> &darr; </button>
        </div>
      </div>
      <div class="col-sm align-self-center">
        <h3> <span id="status"> </span> </h3>
      </div>
    </div>

    <div class="row">
      <div class="col-sm">
        <h4> RFID Value: </h4>
        <div id="RFID" class="alert alert-primary" role="alert">
          <span id="rfid"> </span>
        </div>
      </div>
    </div>
    
    <div class="row">
      <div class="col-sm">
        <h1 align="center" class="display-3 h1"> Automatic </h1>
        <!-- <button id="automatic" type="button" class="btn btn-info"> Automatic Mode </button> -->
      </div>
    </div>
    
    <div class="row">
      <video id="video" width="500" height="300" autoplay></video> <!-- width="640" height="480" -->
      <div class="col-4" id="frame">
        <canvas id="canvas" width="250" height="150"></canvas>
        <button id="play" type="button" class="btn btn-dark" data-toggle="tooltip" data-placement="top" title="Start Streaming!"> &#9654; </button>
        <button id="pause" type="button" class="btn btn-dark" data-toggle="tooltip" data-placement="top" title="Pause Streaming!"> &#x23F8; </button>
        <button id="snap" type="button" class="btn btn-dark" data-toggle="tooltip" data-placement="top" title="Start taking frames"> &#x1F609; </button>
      </div>
    </div>
  </div>

  <script>
    var video = document.getElementById('video');
    const flask_url = 'http://127.0.0.1:5000/detect/';

    // Elements for taking the snapshot
    var canvas = document.getElementById('canvas');
    var context = canvas.getContext('2d');

    document.getElementById("snap").addEventListener("click", function() {
      setInterval(() => {
        var dataURL = canvas.toDataURL(); // data:image/jpg;base64,
        context.drawImage(video, 0, 0, 300, 200);

        $.ajax({
          url: flask_url + dataURL,
          crossOrigin: true,
          contentType: "application/json; charset=utf-8",
          xhrFields: {
              withCredentials: false
          },
          headers: {
              "Access-Control-Allow-Header": "*"
          },
          processData: false,
          success: () => { console.log('Success!'); },
          error: () => { console.log('An error occurred!'); },
          complete: () => { console.log('Completed Request...'); }
        });
      }, 1000);
    });

    document.getElementById("play").addEventListener("click", function() {
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        const constraints = {
          video: {
              width: 800,
              height: 250,
              zoom: true,
              aspectRatio: 16/9
              // facingMode: {
              //     exact: 'environment'
              // }
          },
          video: true
       };
       navigator.mediaDevices.getUserMedia(constraints).then(function(stream) {
            //video.src = window.URL.createObjectURL(stream);
            // console.log(stream);
            video.srcObject = stream;
            video.play();
        });
      }
    });

    document.getElementById("pause").addEventListener("click", function() {
      video.pause();
    });
  </script>
</body>
```

<h3> Meteor App (Client Side - Style) </h3>

``` CSS
body {
    /* margin: 25px; */
    font-family: sans-serif;
    /* background-color: purple; */
}

.up_arrow_cont, .down_arrow_cont, .left_right_arrow_cont {
    /* margin: 10px;
    padding: 5px; */
    text-align: center;
    /* padding: 10px; */
}

#up, #left, #right, #down, #stop {
    font-size: 25px;
    margin: 5px;
}

#frame, #canvas {
    align-items: center;
    text-align: center;
}

#ESP_Status, #RFID {
    display: none;
}

#ESP_Status {
    margin: 10px;
}
```

<h3> Meteor App (Client Side - Bahavior) </h3>

``` JavaScript
import { data } from 'jquery';
import { HTTP } from 'meteor/http';
import './main.html';

Meteor.startup( () => {        
    if (Meteor.isClient) {
        console.log("Printed in browsers and mobile apps");
        start();
    }

    if (Meteor.isCordova) {
        document.addEventListener('deviceready', onDeviceReady, false);

        function onDeviceReady() {
            console.log("Device is ready now");
        }
        cordova.plugins.diagnostic.requestRuntimePermission(function(status) {
            switch (status) {
                case cordova.plugins.diagnostic.permissionStatus.GRANTED:
                    console.log("0");
                    break;
                case cordova.plugins.diagnostic.permissionStatus.NOT_REQUESTED:
                    console.log("1");
                    break;
                case cordova.plugins.diagnostic.permissionStatus.DENIED_ONCE:
                    console.log("2");
                    break;
                case cordova.plugins.diagnostic.permissionStatus.DENIED_ALWAYS:
                    console.log("3");
                    break;
            }
        }, function(error) {
            console.error(error);
        }, cordova.plugins.diagnostic.permission.CAMERA);
        start();
    }
});

function start() {

    const esp_url = 'http://172.28.129.8/';
    const flask_url = 'http://127.0.0.1:5000/detect/';

    var firebase = require("firebase/app");

    require("firebase/auth");
    require("firebase/firestore");
    require("firebase/database");
    
    var firebaseConfig = {
        apiKey: "AIzaSyAQL9IKZM9g07hx69LHd6cAAJwaGaI9A94",
        authDomain: "rfid-47581.firebaseapp.com",
        databaseURL: "https://rfid-47581-default-rtdb.firebaseio.com",
        projectId: "rfid-47581",
        storageBucket: "rfid-47581.appspot.com",
        messagingSenderId: "1097575767969",
        appId: "1:1097575767969:web:db10b4765b5cc48733b5e0",
        measurementId: "G-TYC91D88N7"
    };
    
    firebase.initializeApp(firebaseConfig);

    var TAG = firebase.database().ref().child("/RFID");
    var DIS = firebase.database().ref().child("/Ultra");

    TAG.on('value', function(snapshot) {
        rfid_key = snapshot.key;
        rfid_val = (snapshot.val()).toString(16);
        console.log(rfid_key, rfid_val);
        document.getElementById('RFID').style.display = "block";
        document.getElementById('rfid').innerHTML = rfid_val;
        if (rfid_val === String(0)) {
            document.getElementById('rfid').innerHTML = 'Card does not exist anymore!';
        }
    });

    DIS.on('value', function(snapshot) {
        ultra_key = snapshot.key;
        ultra_val = snapshot.val()
        console.log(ultra_key, ultra_val);
        if (ultra_val < 15) {
            move('http://172.28.129.8/' + 'left');
        }
        else {
            move('http://172.28.129.8/' + 'stop');
        }
    });

    $('#left').on('mousedown touchstart', function() {
        status("Car is moving Left");
        move('http://172.28.129.8/' + 'left');
        console.log('Car is moving Left');
    });
    $('#left').on('mouseup touchend', function() {
        status("Car is Stopped");
        move('http://172.28.129.8/' + 'stop');
        console.log("Car is Stopped");
    });

    $('#right').on('mousedown touchstart', function() {
        status("Car is moving Right");
        move('http://172.28.129.8/' + 'right');
        console.log("Car is moving Right");
    });
    $('#right').on('mouseup touchend', function() {
        status("Car is Stopped");
        move('http://172.28.129.8/' + 'stop');
        console.log("Car is Stopped");
    });

    $('#up').on('mousedown touchstart', function() {
        status("Car is moving Forward");
        move('http://172.28.129.8/' + 'up');
        console.log("Car is moving Forward");
    });
    $('#up').on('mouseup touchend', function() {
        status("Car is Stopped");
        move('http://172.28.129.8/' + 'stop');
        console.log("Car is Stopped");
    });

    $('#down').on('mousedown touchstart', function() {
        status("Car is moving Back");
        move('http://172.28.129.8/' + 'down');
        console.log("Car is moving Back");
    });
    $('#down').on('mouseup touchend', function() {
        status("Car is Stopped");
        move('http://172.28.129.8/' + 'stop');
        console.log("Car is Stopped");
    });

    $('#stop').on('mousedown touchstart', function() {
        status("Car is Stopped");
        move('http://172.28.129.8/' + 'stop');
        console.log("Car is Stopped");
    });

    document.addEventListener('keydown', function(event) {
        if (event.code == 'ArrowLeft') {
            status("Car is moving Left");
            move('http://172.28.129.8/' + 'left');
        }
        if (event.code == 'ArrowRight') {
            status("Car is moving Right");
            move('http://172.28.129.8/' + 'right');
        }
        if (event.code == 'ArrowUp') {
            status("Car is moving Forward");
            move('http://172.28.129.8/' + 'up');
        }
        if (event.code == 'ArrowDown') {
            status("Car is moving Back");
            move('http://172.28.129.8/' + 'down');
        }
        if (event.code == 'KeyZ' && (event.ctrlKey || event.metaKey)) { //  && (event.ctrlKey || event.metaKey)
            status("Car is Stopped");
            move('http://172.28.129.8/' + 'stop');
        }
    });

    document.getElementById('ESP').addEventListener('click', esp_status);

    function move(URL) {
        $.ajax({
            url: URL,
            crossOrigin: true,
            xhrFields: {
                withCredentials: false
            },
            headers: {
                "Access-Control-Allow-Header": "*"
            },
            processData: false,
            success: (success) => { console.log(success); },
            error: (error) => { console.log(error) }
        });
    }

    function esp_status() {
        $.ajax({
            url: esp_url,
            crossOrigin: true,
            xhrFields: {
                withCredentials: false
            },
            headers: {
                "Access-Control-Allow-Header": "*"
            },
            processData: false,
            success: () => { console.log('Connected Successfully'); document.getElementById('ESP_Status').style.display = "block"; document.getElementById('esp').innerHTML = 'Connected Successfully'; },
            error: () => { console.log('An error occurred during connecting to ESP'); document.getElementById('ESP_Status').style.display = "block"; document.getElementById('esp').innerHTML = 'An error occurred during connecting to ESP'; },
        });
    }

    function status(input) {
        document.getElementById('status').classList.add('badge');
        document.getElementById('status').classList.add('rounded-pill');
        document.getElementById('status').classList.add('bg-success');
        document.getElementById('status').innerHTML = input;
    }
}
```

<h3> Python Code </h3>

``` Python
import requests, cv2, math, base64, io, time
import numpy as np
from flask import Flask, render_template, request, flash, redirect, after_this_request, jsonify
import matplotlib.pyplot as plt

# initialize the app
app = Flask(__name__)
app.secret_key = "secret"

# variables to be used in lean detection
r_width, r_height, theta = 640, 480, 0 # 500, 300
threshold, threshold1, threshold2 = 5, 80, 80
url2 = 'http://172.28.129.8/'
# result = None
lower_blue = np.array([94, 80, 2], dtype = "uint8") # lower limit of blue color
upper_blue = np.array([126, 255, 255], dtype="uint8")

@app.route("/")
def index():
    @after_this_request
    def after_request(response):
        response.headers["Access-Control-Allow-Origin"] = "*" # <- You can change "*" for a domain for example "http://localhost"
        response.headers["Access-Control-Allow-Credentials"] = "true"
        response.headers["Access-Control-Allow-Methods"] = "POST, GET, OPTIONS, PUT, DELETE"
        response.headers["Access-Control-Allow-Headers"] = "Accept, Content-Type, Content-Length, Accept-Encoding, X-CSRF-Token, Authorization"
        return response
    return "Flask is working!"

@app.route("/detect/<path:imgData>")
def result(imgData):
    @after_this_request
    def after_request(response):
        response.headers["Access-Control-Allow-Origin"] = "*" # <- You can change "*" for a domain for example "http://localhost"
        response.headers["Access-Control-Allow-Credentials"] = "true"
        response.headers["Access-Control-Allow-Methods"] = "POST, GET, OPTIONS, PUT, DELETE"
        response.headers["Access-Control-Allow-Headers"] = "Accept, Content-Type, Content-Length, Accept-Encoding, X-CSRF-Token, Authorization"
        return response
    # print(type(imgData))
    new_data = imgData.split(",")[1]
    # print(new_data)
    data_np = np.fromstring(base64.b64decode(new_data), np.uint8)
    print(data_np)
    # requests.get(url2 + lean_det(data_np))
    lean_det(data_np)
    return imgData

def lean_det(input):
    frame = cv2.imdecode(input, -1)
    edges = detect_edges(frame)
    roi = region_of_interest(edges)
    line_segments = detect_line_segments(roi)
    lane_lines = average_slope_intercept(frame, line_segments)
    lane_lines_image = display_lines(frame, lane_lines)
    steering_angle = get_steering_angle(frame, lane_lines)
    heading_image = display_heading_line(lane_lines_image, steering_angle)
    # heading_image = cv2.resize(heading_image,(700,700))
    deviation = steering_angle - 90

    requests.get(url2 + "up")
    print("forward")
    requests.get(url2 + "stop")

    if deviation > 5:  # steer right if the deviation is positive
        state = "right"
        requests.get(url2 + "right")
        print("right")

    elif (deviation < 5 and deviation > -5):
        state = "stop"
        requests.get(url2 + "stop")
        print("stop")

    elif deviation < -5:  # steer left if deviation is negative
        state = "left"
        requests.get(url2 + "left")
        print("left")

    else:
        print("No Move!")
    
    requests.get(url2 + state)

    plt.imshow(heading_image)
    plt.show()

    return state

def detect_edges(frame):
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    mask = cv2.inRange(hsv, lower_blue, upper_blue) # this mask will filter out everything but blue

    # detect edges
    edges = cv2.Canny(mask, 50, 100)
    return edges

def region_of_interest(edges):
    height, width = edges.shape # extract the height and width of the edges frame
    mask = np.zeros_like(edges) # make an empty matrix with same dimensions of the edges frame
    # only focus lower half of the screen
    polygon = np.array([[
        (0, height),
        (0,  height/2),
        (width , height/2),
        (width , height),
    ]], np.int32)
    cv2.fillPoly(mask, polygon, 255) # fill the polygon with blue color
    cropped_edges = cv2.bitwise_and(edges, mask)
    return cropped_edges

def detect_line_segments(cropped_edges):
    rho = 1
    theta = np.pi / 180
    min_threshold = 10
    line_segments = cv2.HoughLinesP(cropped_edges, rho, theta, min_threshold,
                                    np.array([]), minLineLength=5, maxLineGap=0)
    return line_segments
def average_slope_intercept(frame, line_segments):
    lane_lines = []

    if line_segments is None:
        print("no line segment detected")
        return lane_lines

    height, width,_ = frame.shape
    left_fit = []
    right_fit = []
    boundary = 1/3

    left_region_boundary = width * (1 - boundary)
    right_region_boundary = width * boundary

    for line_segment in line_segments:
        for x1, y1, x2, y2 in line_segment:
            if x1 == x2:

                continue

            fit = np.polyfit((x1, x2), (y1, y2), 1)
            slope = (y2 - y1) / (x2 - x1)
            intercept = y1 - (slope * x1)

            if slope < 0:
                if x1 < left_region_boundary and x2 < left_region_boundary:
                    left_fit.append((slope, intercept))
            else:
                if x1 > right_region_boundary and x2 > right_region_boundary:
                    right_fit.append((slope, intercept))

    left_fit_average = np.average(left_fit, axis=0)
    if len(left_fit) > 0:
        lane_lines.append(make_points(frame, left_fit_average))

    right_fit_average = np.average(right_fit, axis=0)
    if len(right_fit) > 0:
        lane_lines.append(make_points(frame, right_fit_average))
    return lane_lines

def make_points(frame, line):
    height, width, _ = frame.shape
    slope, intercept = line
    y1 = height  # bottom of the frame
    y2 = int(y1 / 2)  # make points from middle of the frame down

    if slope == 0:
        slope = 0.1

    x1 = int((y1 - intercept) / slope)
    x2 = int((y2 - intercept) / slope)

    return [[x1, y1, x2, y2]]

def display_lines(frame, lines, line_color=(0, 255, 0), line_width=6): # line color (B,G,R)
    line_image = np.zeros_like(frame)
    if lines is not None:
        for line in lines:
            for x1, y1, x2, y2 in line:
                cv2.line(line_image, (x1, y1), (x2, y2), line_color, line_width)

    line_image = cv2.addWeighted(frame, 0.8, line_image, 1, 1)
    return line_image


def get_steering_angle(frame, lane_lines):
    height, width, _ = frame.shape

    if len(lane_lines) == 2:  # if two lane lines are detected
        _, _, left_x2, _ = lane_lines[0][0]  # extract left x2 from lane_lines array
        _, _, right_x2, _ = lane_lines[1][0]  # extract right x2 from lane_lines array
        mid = int(width / 2)
        x_offset = (left_x2 + right_x2) / 2 - mid
        y_offset = int(height / 2)

    elif len(lane_lines) == 1:  # if only one line is detected
        x1, _, x2, _ = lane_lines[0][0]
        x_offset = x2 - x1
        y_offset = int(height / 2)

    elif len(lane_lines) == 0:  # if no line is detected
        x_offset = 0
        y_offset = int(height / 2)

    angle_to_mid_radian = math.atan(x_offset / y_offset)
    angle_to_mid_deg = int(angle_to_mid_radian * 180.0 / math.pi)
    steering_angle = angle_to_mid_deg + 90
    return steering_angle

def display_heading_line(frame, steering_angle, line_color=(0, 0, 255), line_width=5):
    heading_image = np.zeros_like(frame)
    height, width, _ = frame.shape

    steering_angle_radian = steering_angle / 180.0 * math.pi
    x1 = int(width / 2)
    y1 = height
    x2 = int(x1 - height / 2 / math.tan(steering_angle_radian))
    y2 = int(height / 2)

    cv2.line(heading_image, (x1, y1), (x2, y2), line_color, line_width)
    heading_image = cv2.addWeighted(frame, 0.8, heading_image, 1, 1)

    return heading_image

if __name__ == "__main__":
    app.run(host = '0.0.0.0', port = 5000, debug = True)
```