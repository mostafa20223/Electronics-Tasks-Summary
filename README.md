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

<h1 id="C"> Task3 - Autonomous Vehicle (Self Driving Car) </h1>




