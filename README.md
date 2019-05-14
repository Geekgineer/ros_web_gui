# Web GUI for Controlling and Monitoring ROS framework

Based on [robotwebtools](http://robotwebtools.org/tools.html) this package enable a full control over your Robot you can teleoprate ,use the Navigation Stack, ROS parameter server and sending-receiving data is supported with GUI 

## Getting Started
### Prerequisites

- Ubuntu 16.04 or 14.04 - tested you may try another distro  
- Working ROS installation

### Installation 
##### 1- Clone the robot_web_gui package to your ROS workspace:
     
	$ cd ~/catkin_ws/src
	$ git clone https://github.com/Geekgineer/ros-web-gui
	$ cd .. && catkin_make

##### 2- We will also need a number of JavaScript packages 
Thay are not typically distributed as
  Debian packages. These packages are already included in the robot_web_gui/js directory
  but if you need to reinstall them for some reason, you can run the install-js-
  packages.sh script as follows:

	$ roscd robot_web_gui/scripts
	$ ./install-js-packages.sh 


##### 3- Install rosbridge_suite and supporting packages 
run the following commands:
	
	$ sudo apt-get update
	$ sudo apt-get install ros-kinetic-rosbridge-suite \ros-kinetic-robot-pose-publisher ros-kinetic-tf2-web-republisher ros-kinetic-web-video-server
	$ rospack profile

##### 4- We will use the web_video_server package to stream live video from ROS topicsover HTTP to our browser GUI.
Install it with the following command:

	$ sudo apt-get update && sudo apt-get upgrade 
	$ sudo apt-get install ros-kinetic-web-video-server
	
**Note:**
 web_video_server uses port 8080 by default. If you are already using that port for something else, you can run web_video_server with a different port parameter. The following example uses port 8181 :

	$ rosrun web_video_server web_video_server _port:=8181

>When you run web_video_server with a different port as shown above, the port number is stored on the ROS parameter server. So if you run web_video_server in a later session without the port parameter, it will pick up the port number from the parameter server instead of the default port 8080 . To override the value on the parameter server, you can specify the default port on the command line just as we did above with the alternate port. You can also use the included web_video_server launch file which sets the port parameter to the default 8080.

##### 5- Installing a Simple Web Server (mini-httpd)
while web_video_server provides its own HTTP server for viewing video streams,rosbridge requires a separate web server for processing websocket requests.
Since not everyone runs a web server like Apache on their machine or robot's computer, we will use a simple web server called mini-httpd to serve up our robot GUI. Run the following command to install the mini-httpd package:

	$ sudo apt-get install mini-httpd

##### 6- Test our Web Server (mini-httpd)
Included in the robot_web_gui/scripts directory is a simple shell script called mini-httpd.sh that will launch the webserver on port 8181 and set the document directory to the robot_web_gui directory. Run the script now as follows:
	
	$ rosrun ros_web_gui mini-httpd.sh


You will likely see the message:

```
  bind: Address already in use
```
which can be ignored as there seems to be a bug in mini-httpd that causes this message to be displayed even when the port is free. If the port really is in use by another process, change the port in the mini-httpd.conf file to something else, like 8282 and try running it again. (But don't use ports 8080 or 9090 as these will be used by other processes needed by rosbridge.) 
	change ports as 8484 8585 etc..
_**Remember**_ the port number you end up using as we will need it below.

**NOTE:** 
>To control the mini-http server use start|stop|restart force-reloadoptions,example to stop the service till the next reboot:

	$ sudo /etc/init.d/mini-httpd stop

## Testing The Web GUI

### Connect the Web GUI to your Robot
 1. Edit video topic in launch/rosbridge.launch as you like or as the topics you are publishing from simulation or actual hardware over ROS network and also video topic in js/robot_gui.js 
   ```javascript
      videoTopic: '/camera/rgb/image_raw' 
   ```
	
 2. Edit the cmd_vel topic 
   
   ```javascript
      cmdVelTopic: '/cmd_vel_mux/input/teleop'
   ```
 3. Another topics included and you may add your own.

### Running 

1- first launch any robot you want simulated or hardware  but make sure that:

   - They are on the same local network you have.
   - Names of the topics you want the GUI to handel it.

2- for testing you can use turtlebot to test the web GUI

	$ roscore
	$ roslaunch turtlebot_gazebo turtlebot_world.launch

3- launch the web GUI on localhost

	$ roslaunch robot_web_gui rosbridge.launch

4- Using your pc or mobile or tablet browser, simply type the IP address or host name into the URL bar.
Make sure to include any necessary ports or paths required to view your website.
> e.g.	<http://localhost:8085/robot_gui.html>
> e.g.	<http://localhost:8085/simple_gui.html>

**Note:**   
> - you can change the port number as you want,edit   
>  robot_web_gui/scripts/mini-httpd.conf

## Troubleshooting 

**Problem:** If you launch the web GUI with rosbridge.launch twice the min-http server will throw this conflict because is mini-httpd server is running already even if you close the terminal on and you try to lunched on the same port again.

>Launching mini-httpd...
bind: Address already in use
bind: Address already in use
/usr/sbin/mini_httpd: can't bind to any address 

**Solution:**

 - Stop the Server and relaunch rosbridge.launch
  
  		 $ sudo /etc/init.d/mini-httpd stop

 - Or launch it again on different port by modifying mini-httpd.conf or restart the machine



## Contributing 

> - Modifying and adding new fancy GUI web pages.
> - Add more features.


