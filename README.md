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

#define FIREBASE_HOST "fir-iot-90928.firebaseio.com"  // the project name address from firebase id
#define FIREBASE_AUTH "xvAXttUWcq0hlVihXWCOsM7mmTShY0LNcUIZAwCC"  // the secret key generated from firebase

#define WIFI_SSID "Vodafone_VDSL_9A52"  // input your home or public wifi name 
#define WIFI_PASSWORD "HanaaYehia116" // password of wifi ssid
 
#define DHTPIN D2 // what digital pin we're connected to
#define DHTTYPE DHT11 // select dht type as DHT 11 or DHT22
DHT dht(DHTPIN, DHTTYPE);                                                     

void setup() {
  Serial.begin(9600);
  delay(1000);                
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD); // try to connect with wifi
  Serial.print("Connecting to ");
  Serial.print(WIFI_SSID);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println();
  Serial.print("Connected to ");
  Serial.println(WIFI_SSID);
  Serial.print("IP Address is : ");
  Serial.println(WiFi.localIP()); // print local IP address
  Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH); // connect to firebase
  dht.begin();  // sStart reading dht sensor
}

void loop() { 
  float h = dht.readHumidity(); // reading temperature or humidity takes about 250 milliseconds!
  float t = dht.readTemperature();  // read temperature as Celsius (the default)
  if (isnan(h) || isnan(t)) { // check if any reads failed and exit early (to try again).
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  Serial.print("Humidity: ");
  Serial.print(h);
  float fireHumid = h; // convert integer humidity to string humidity
  Serial.print("%  Temperature: ");
  Serial.print(t);
  Serial.println("Â°C ");
  float fireTemp = t; // convert integer temperature to string temperature
  delay(200);
  Firebase.setFloat("/FirebaseIOT/humidity",fireHumid); // setup path and send readings
  Firebase.setFloat("/FirebaseIOT/temperature", fireTemp);  // setup path and send readings
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

# config dictionary variable to set configuration for firebase connection
config = {
      "apiKey": "AIzaSyDkv7Tp5OyLd9XKz1LSuZbo56sZ5XAu3s8",
      "authDomain": "fir-iot-90928.firebaseapp.com",
      "databaseURL": "https://fir-iot-90928.firebaseio.com",
      "storageBucket": "fir-iot-90928.appspot.com",
    }
    
class ApplicationWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super(ApplicationWindow, self).__init__()
        self.ui = Ui_MainWindow()
        self.ui.setupUi(self)
        self.temperatureData = []
        self.humidityData = []
        self.timer = QtCore.QTimer()
        self.timer.setInterval(50)
        self.timer.start()
        self.timer.timeout.connect(self.updateData)
        self.ui.TEMPERATURE_GRAPH.setLabels(title = "TEMPERATURE")
        self.ui.HUMIDITY_GRAPH.setLabels(title = "HUMIDITY")
        self.ui.TEMPERATURE_GRAPH.setYRange(20, 100)
        self.ui.HUMIDITY_GRAPH.setYRange(40, 120)

    # function to get the current value of temperature and humidity
    def updateData(self):
        firebase = pyrebase.initialize_app(config)
        db = firebase.database()
        data = [db.child("FirebaseIOT/temperature").get(), db.child("FirebaseIOT/humidity").get()]
        self.temperatureData.append(data[0].val())
        self.humidityData.append(data[1].val())
        self.plotData()

    def plotData(self):
        self.ui.TEMPERATURE_GRAPH.plot(self.temperatureData)
        self.ui.HUMIDITY_GRAPH.plot(self.humidityData)
        self.ui.temperature_data.addItem(str(self.temperatureData[-1]))
        self.ui.humidity_data.addItem(str(self.humidityData[-1]))

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
    {{> map}}
  </div>

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

// firebaseConfig object adjust the configuration of firebase application (similar to python)
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




