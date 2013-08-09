Arduino Nunchuk Library
=============


#ArduinoNunchuk


This project is a Library inteded to use with the Arduino framework. It creates a simple interface by which the Arduino can read the state
of the Wii Nunchuk using the I2C interface with the help of the Wire Library.

The project is based on the Nunchuk Library created in an article [Make Your Own Video Game System](http://pragprog.com/magazines/2011-08/make-your-own-video-game-system). 
But unfortunately the Nunchuk library created in this article is obsolete because of the API changes in the Wire library.

##Does it run on Magic Smoke ? (Of course not ! its I2C...)


The Arduino interfaces with the Nunchuk using the [I2C](http://en.wikipedia.org/wiki/I%C2%B2C) protocol. Though it might seem challenging to learn a new protocol, In reality its a very
simple protocol thats easy to grasp. And what makes it even more easier is the [Wire](http://arduino.cc/en/Reference/Wire) library that can be used along with the Arduino. With the help of this library we can forget all the 
tedious time keeping, clock signal generation and sychnronization hassels and just focus on talking to the Nunchuk.

###Baby Steps - (Talking to the Nunchuk)
--------------------

The process of getting the data from the Nunchuk follows the process shown in the below diagram:

![alt text](http://a.pragprog.com/magazines/2011-08/images/nunchuck_communications__11l1a7__.jpg)

The communication with the Nunchuk is initiated by sending a Handshake message to the Nunchuk and then by sending a data request
each time we want the latest Gamepad state.

###WTFH do those bytes mean ? - (Data Structure)
--------------------

The one rule of thumb when working with game controllers:

No matter what the interface is or what kind of a console a game controller is attached to a game controller always transmits the states of all its buttons and Joysticks (In this case accelerometers) as a frame everytime data is requested.
This is what I call the controller state.

So in that analogy the Nunchuk's contoller state consist of the following fields/data:

- Z Button (Pressed/Released)
- C Button (Pressed/Released)
- Integer Value of the Joystick's position in X-Axis
- Integer Value of the Joystick's position in the Y-Axis 
- Integer Value of the Nunchuk's acceleration in the X-Axis
- Integer Value of the Nunchuk's acceleration in the Y-Axis
- Integer Value of the Nunchuk's acceleration in the Z-Axis

Now these 7 values are packed into a 6 Byte long data packet that can be descibed by the following diagram.

![alt text](http://oi40.tinypic.com/e9hi10.jpg "Data Diagram")

In order to save space, these values are packed into 6 Bytes by breaking and placing the 2 **Least Significant Bits**
of the acceleration values (X,Y,Z - Axes) into the 6th Byte.

The various fields described in the Nunchuk.h show how the data from these bytes are extracted used to represent the states of the controller.

###Wires ! Wires ! Wires ! - The Pin Mapping
-------------------

No matter how much we code the most important part of any hardware project is the getting the connections right. As the I2C prococol only uses 
The Nunchuk's Pins are described by the following diagram.

![alt text](http://oi42.tinypic.com/20h7ldu.jpg "Pin Diagram")

And the Pin mapping are as follows:

**Board**		|	**I2C / TWI pins**

Uno, Ethernet	|	A4	-->	(Data)	,A5	-->	(Clock)

Mega2560		  |	20	--> (Data)	,21	-->	(Clock)

Leonardo		  |	2	-->	(Data)  	,3	-->	(Clock)

Note: Older Versions of the Arduno boards have similar pin configurations in order to allow the new boards to support older shields and hence 
will most likely have the same pin-out configurations as the newer counterparts.

##Ok I cloned it, now what ? (How to use it inside the Arduino IDE)

1. Zip the entire folder's contents into ArduinoNunchuk.zip
1. From the Arduino IDE goto : Sketch->Import Library->Add Library and then select the ArduinoNunchuk.zip that you just created.
1. Check if the line ``` #include <Nunchuk.h> ``` is included automatically, and include the line if it did not.
1. Verify/Compile (Ctrl+R) the project to see if there are any errors. (Log the errors into [Issues](https://github.com/rkrishnasanka/ArduinoNunchuk/issues) if any occour).

##Show me some example code !

```
#include <Nunchuk.h>
#include <Wire.h>

Nunchuk _controller;

// the setup routine runs once when you press reset:
void setup() {
  // initialize serial communication at 9600 bits per second:
  Serial.begin(9600);

  _controller.initialize();
}

// the loop routine runs over and over again forever:
void loop() {
  // print out the value you read:
  _controller.update();
  Serial.print("Z: ");
  Serial.print(_controller.z_button());
  Serial.print(" ");
  Serial.print("C :");
  Serial.print(_controller.c_button());
  Serial.print("\n");
  Serial.print("UP: ");
  Serial.print(_controller.up());
  Serial.print(" ");
  Serial.print("DOWN: ");
  Serial.print(_controller.down());
  Serial.print(" ");
  Serial.print("LEFT: ");
  Serial.print(_controller.left());
  Serial.print(" ");
  Serial.print("RIGHT: ");
  Serial.print(_controller.right());
  Serial.print("\n");
  char buffer[50];
  sprintf(buffer, "x:%d y:%d z:%d",_controller.x_acceleration(),_controller.y_acceleration(),_controller.z_acceleration());
  Serial.println(buffer);
  delay(1);        // delay in between reads for stability
}

```
##I didn't figure out all of this on my own ! (Credits)

* [Make Your Own Video Game System](http://pragprog.com/magazines/2011-08/make-your-own-video-game-system)
* [Maik Schmidt](https://github.com/maik) for the creating the original version of this library.
* [Reading the Wii Nunchuk data on the Arduino](http://www.windmeadow.com/node/42)
* And the deus Ex- Machina that led me to the project in the first place !


