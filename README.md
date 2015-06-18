Explore MQTT World Behind Bluemix Internet of Thing Service

Internet of Things (IoT) is a very hot topic recently. IBM Bluemix provides its strong support by offering Internet Of Things service. By adopting this service, we can easily intertwine different kinds of devices and applications. The secret behind Bluemix Internet of Thing Service is Message Queue Telemetry Transport(MQTT), which is an IoT connectivity protocol on top of the TCP/IP protocol. 

In this article, we go in details about how MQTT is working behind Bluemix Internet of Thing service.

Bluemix Internet of Thing service works as the MQTT broker. Devices publish status and other relevant messages to IoT service and also subscribe the commands message so that it can update accordingly. Applications subscribe the status and other messages and sends the command to corresponding Device if needed. Unlike the "free" MQTT broker, Bluemix IoT services requests the Device and Application follow some rules, for example, message format, naming rules, etc.

Below is a brief description about it.
Bluemix IoT service (MQTT Broker Host):
tcp://<org-id>.messaging.internetofthings.ibmcloud.com:1883
ssl://<org-id>.messaging.internetofthings.ibmcloud.com:8883

Devices:
clientid d:<orgid>:<type-id>:<divice-id>
publishes iot-2/evt/<event-id>/fmt/<format> 
subscribes iot-2/cmd/<cmd-type>/fmt/<format-id>

Applications:
clientid a:<orgid>:<app-id>
publishes iot-2/type/<type-id>/id/<device-id>/cmd/<cmd-id>/fmt/<format-id>
subscribes iot-2/type/<type-id>/id/<device-id>/evt/<event-id>/fmt/<format-id>

To demonstrate the MQTT mechanism behind IBM IoT service, we build up a very simple Java standalone application using Eclipse Paho MQTT Client library (https://eclipse.org/paho/).

Devices publish a data counter message incrementally for 0 at an internal (for example 15 seconds), and subscribe the command on resetting the data counter to 0. Application subscribe the message about the counter from Devices, once it reaches to a threshold (for example, if the data counter is over 4), then will publish the reset command to the corresponding Device to request it to clear the previous counter and set it to 0. 

It is a very simple demo application. However, the idea behind the demo could be extended dramatically. Besides, you may use any language which support MQTT protocol to build up both Device side and Application side apps. Nothing limits you doing it by yourselves.

There are some tutorials on building up Devices side applications, such as Arduino. Though it does not cover your smart phones, it should be OK as both Android and iOS has third party library to support MQTT.

For the application side, most of the current tutorials are employing RED-Node service, which can provide flow editor to interconnect with Bluemix IoT service. But, you do not have to, you may build up applications in other environment, such as Java Liberty, Node.js, etc.

Once you clearly understand the MQTT mechanism behind IoT service, we do not believe it is a difficult job for you to implement applications upon your requirement.

Outline:
I. Introduction
	a.  App URL: 
	b.  Code URL: https://hub.jazz.net/project/chunbintang/bluemixmqtt/overview
II. Before getting started
	a.  Bluemix Account
	b.  Internet of Things Service
	c.  Java Programming Skill
III. How to create the app on Bluemix
	a.  Java Liberty Runtime
        1. Open the Catalog menu.
		2. From the Runtimes section, click Liberty for Java.
        3. In the App field, specify the name of your app.
        4. Click Create.Wait for your application to provision.
　　b.  Internet of Things Service
        1. Click the App created in the Dashboard. Open the Catalog menu.
		2. Click Add A Service.
        3. Choose Internet of Things under Internet of Things.
		4. Click Create. Click OK if it is prompted to restart the application.
IV. Configure Internet of Things Service (Add Devices and Applications)
	a. Open the Internet of Things console
		1. Navigate to your application overview page.
		2. From the Overview menu, click Internet of Things.
        3. Click Launch to open the Internet of Things console.
	b.  Add Devices
        1. Click Add Device under Device Tab.
    	2. Choose Create a device type for Device Type option.
　　	3. Fill the device type as MQTTDevice. The reason is that in the app we set it to MQTTDevice by default.
　　    4. Fill the Device ID with a unique id, for example, aabbccddee12
　　    5. Click Continue.
　　    6. Copy the information for device ID. Note: the info will be used later.
　　    7. Click Done. You may use the above steps to create some more devices.
　　c.  Add Applications.
        1. Click Add API Key under API Keys Tab.
　　    2. Copy the information in the New API Key dialog prompted. Note: the info will be used later.
　　	3. Click OK.
V. 	Build and Run the App
        1. Go to https://hub.jazz.net/project/chunbintang/bluemixmqtt/overview for the source code. It is the standalone java application, you may compile it by yourself. Or you may find a compiled one (bluemixmqtt.jar) under MyData folder. Note: JDK should be later than Java 7.0.
		2. Go to MyData folder, use a text editor to edit the configuration files, the app.conf is for the App Side application, and the device.conf is for the Device Side application.
		3. Use the info copied previously to update the configuration files accordingly.
		4. Run the App Side application.
java -cp .;org.eclipse.paho.client.mqttv3.jar;json4j-apache-1.1.0.jar;bluemixmqtt.jar com.ibm.bluemixmqtt.AppTest
        5. Run the Device Side application. Note: You may run multiple Device Side applications by simply copied the MyData folder to other directory and fill the relevant Devices info.
java -cp .;org.eclipse.paho.client.mqttv3.jar;json4j-apache-1.1.0.jar;bluemixmqtt.jar com.ibm.bluemixmqtt.DeviceTest
        6. You may go to Internet of Things Device tab to check the message published by Devices. You may find that the count field in the event message increase from 0 to 4 and then reset to 0 again and again.