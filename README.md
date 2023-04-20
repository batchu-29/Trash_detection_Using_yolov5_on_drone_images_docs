# Trash detection and reporting system using YoloV5, Drone Kit and ServiceNow.

## Problem statement
We aim to provide the higher authorities responsible for sanitation and cleaning of large areas such as institutions and small areas of a municipality a method of evaluating the work of their subordinates and also to provide information about places where considerable amount of trash is present.

## Solution
*	Create missions of places where evaluation of trash needs to be done.
*	Performing regular flights over the missions created and collecting aerial images of the waypoints added in the mission using a drone.
*	The collected images will be inferred using custom YoloV5 model which is trained on images of trash.
*	If the number of detections are more than set threshold an alert will be sent to the authorities in the form of an incident in ServiceNow.
*	The inferred images are also stored in Amazon s3 with a reference of them in the database known as Flight ID.
*	The images corresponding to the provided Flight ID can be viewed via our Web Application.


![workflow ](https://user-images.githubusercontent.com/98334366/231242547-266eecc6-4fae-422b-8b7a-a9ef644ab339.png)



## Web Application

![Screenshot (103)](https://user-images.githubusercontent.com/98334366/230270095-b75152f5-23f6-4feb-adac-67a2d94b7122.png)

 
### Drone Command 
*	the drone command application has following functionalities:
    *	Using WebSockets API we connect our application  with Ground Station and perform flight on selected mission from the available missions.
    *	The missions are fetched from pre created missions which are stored in database.
    * Launch is enabled after the drone is checked if it is already in flight and if it is connected or not
	*  After clicking on launch the selected mission is sent to ground station and drone is armed.
*	Tech stack:- ReactJs, JavaScript, Rest API,WebSockets.
*	Repository:- https://github.com/batchu-29/Trash_detection_application

![Screenshot (115)](https://user-images.githubusercontent.com/98334366/230270760-9438ff76-3765-46d6-9203-6f13ae096ffb.png)


### Create Mission
*	A web page for selecting way points is opened 
*	Where n number of waypoints can be selected for a specific area.
*	A map will be rendered of searched location in which waypoints can be marked.
*	Marked waypoints need to be added with there mission name, waypoint name, description and also index of that waypoint.
*	The created mission will be stored in database once submit all is clicked.

![Screenshot (127)](https://user-images.githubusercontent.com/98334366/230270868-33d5252b-4f48-4347-95ec-4c237c6f57a5.png)



### View Images
*	The inferred images stored in Amazon S3 can be viewed using view images option in Web application.
*	Flight ID has to be entered and all the images corresponding to that Flight ID will shown along with the information like which mission it belongs to, location of the image taken and also number of trash detections found in that image.
*	Tech stack:-ReatJs, Amazon S3, Rest API.
*	Repository:-  https://github.com/batchu-29/Trash_detection_applicati 


 ![Screenshot (100)](https://user-images.githubusercontent.com/98334366/230270958-6e45ef78-3c52-43b7-8a2b-affe549245e6.png)


![Screenshot (101)](https://user-images.githubusercontent.com/98334366/230270963-212c4cea-6f5d-4979-9a39-87f86da0e97d.png)


![Screenshot (102)](https://user-images.githubusercontent.com/98334366/230270966-66116a06-ca71-486f-a9de-c93d7d701f8d.png)






### Drone Kit
*	DroneKit is a python API for communicating with the ground station and UAV's which wraps over the ArduPilot API for flight control leveraging the MAVLINK protocol
*	The Flight Controller we used (PixHawk) can communicate with the Onboard Computer as well as the Companion Computer on the drone.
*	The Onboard Computer enables the drone to click pictures programmatically when a way point is hit
*	Camera used: GoPro Hero 9 Black
*	The Ground station connects to the main server with a WebSocket connection
	* Its listens for LAUNCH command
	* It broadcasts it's location and it's update it's arming status to the status
*	Once a mission is completed it creates a flight in the server for the given mission id
*	The flight_id returned is then used for future purposes in Trash Detection Client
*	Simulator used : Ardupilot Simulator (for local development)
*	Tech Stack :- Ardupilot SITL,Dronekit SDK,Websockets using asyncio
*	Repository:- https://github.com/batchu-29/Trash_detection_dronekit

![dronekitss](https://user-images.githubusercontent.com/98334366/230271246-094abbec-2b39-4d30-ab1f-063623be009e.png)

 

## Trash Detection client
*	Trash Detection Client is a GPU enabled client for detecting trash from the images obtained by the drone
*	The script which is run on a folder of images accepts a flight id and performs inference on those images and updates the flight details in the database based on number of inference
*	The custom yolov5 trained model has an accuracy for 56%
*	It also uploads these images to Amazon S3 and preserves a reference URL to them
*	And finally after inference is done it creates an incident in Service Now
*	Tech Stack :- Yolov5,REST API
*	Repository:-  https://github.com/batchu-29/Trash_detection_Yolov5
*	Data Set Used :- https://universe.roboflow.com/alexandros-petkos/marinelitter-4k-test

![yolov5ss](https://user-images.githubusercontent.com/98334366/230271612-4bcd99b6-86ac-405e-a290-7d7d0d4aaa69.png)
![servicenowss](https://user-images.githubusercontent.com/98334366/230271635-cd1eced4-e9f4-465a-87bf-261c99361678.png)

 
 
## Backend
*	The backend is an expressjs server with a MongoDB instance hosted on Atlas .
*	The hosting used is Amazon EC2 Free Tier paired with Amazon S3 as an image storage service
*	It acts a CRUD server as well as a WebSocket Server
*	Resources
 	* Missions
    * Flights
    * Images
*	It's responsible for the fetching the above resources.
*	Repository:-  https://github.com/batchu-29/Trash_detection_server
