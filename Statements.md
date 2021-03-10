# Tasks’ Statement
## Task1 Statement (Required)
* Use a signal sensor and the ESP chip to develop a system for real-time monitoring vital signal via wireless connection
* Signal should be acquired through the sensor, then fed to the ESP that send it to a webserver from which several clients can access it in real-time
* You are required to develop two types of clients that can access and draw the acquired signal in real-time:
    * Desktop application
    * Mobile app

## Task2 Statement (Required)

## Task3 Statement (Required)
### The setup
* A moving vehicle that will carry a mobile phone and RFID reader. A "moving vehicle" can be as simple as an axis connected with two motors and two small tires. You do NOT need to buy an already-installed vehicle! - A remote-control app on your mobile phone
* A desktop/laptop application with some image processing app like OpenCV
Race track consists of two parts: P1 and P2
* P1 will be an arbitrary track that has some obstacles to avoid and some RFID tags to read while passing through
    * You will control the vehicle wireless via your remote-control mobile app to allow the vehicle to read the RFID tags and send their values to your app. The wireless control can be anything (i.e. RF, IR, BT and WiFi) up to your choice
* P2 will be a confined track with clear boundaries as a U shape and will have some small obstacles to avoid. Requirements:
    * The moving vehicle should be able to go forward, backward, left and right
    * When finishing P2, you will switch your car into autonomous control where your laptop application will depend on the images sent from the mobile cam of your phone to guide the vehicle. So, the phone gets the images continuously, send them to the laptop where OpenCV analyze the images and guide the vehicle
    * P2 will be on a U shape where the side lines should be as clear as possible for your algorithm. It will also contain some obstacles to avoid automatically. - The function performed on the laptop in the previous step (i.e. using OpenCV to guide the car) should be repeated on your mobile as a mobile app
* You finish the race when your vehicle crosses the end line of P2 with the RFID values sent correctly to your mobile app
