# IoT Edge Sample applicaiton with SenseHat
## Required hardware:
- Raspberry Pi 
- Sensehat

This is a sample Azure Iot Edge solution that contains two modules (located in folder IoTEdgeSenseHat/modules/):
- sensorsonhat
- displayonhat 

## sensorsonhat
This module simply read telemetry data from sensors on sensehat, then send to data pipeline in iot edge runtime

## displayonhat
This module receive data from upstream module (sensoronhat), and display it on sensehat LED displayer.
This module also support IoT hub digital twin that one can change the LED display text color.

## Tips
1. When using this repo, you need to create a .env file by using the format in sample.env

2. Documentation for setup IoT Edge on Raspberry Pi: https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-linux-arm  and development environment: https://docs.microsoft.com/en-us/azure/iot-edge/how-to-vs-code-develop-module

3. Development and deployment on different platform (PC vs Raspberry Pi)
If you are building a RaspberryPi-based application on laptop (PC), use cross platform build. See http://kmdouglass.github.io/posts/how-i-built-a-cross-compilation-workflow-for-the-raspberry-pi.html

4. Dev and debug cycle can be too too long when follow the normal process

	**Normal process**: Coding, build docker image, push to docker repo, trigger deploy to device, wait device to pick it up. Check iotedge logs "*modulename*" to see any error (see azure portal page for status). If there is an error, you have to remove the image from device, kill any running container, then repeat. In addition, the process requires you to have the SSH connection to the target device, but sometimes you do not.

	**Improved process**: build and test on target device, but only core logic part. keep docker as a wrapper. The core logic part can be deployed into docker image during build time, by using git/npm/pip. (This means take the advantage of having ssh to device. Also consider to build the docker image directly on the target device.) *(Todo: Does IoT Edge SDK support locally config the module and data pipeline??)*
  
5. **Important**: For each module image update, remember to update version number in module.json.
See https://github.com/Azure/iotedge/issues/364

6.  Run docker image that need to access local hardware
For sensehat based modules, remember to:
	- Install the necessary sensehat libraries (e.g. ./modules/displayonhat/requirements.txt)
	- Set the docker run flag with "Privileged" in deployment.template.json
 
7. Enable debug log: https://docs.microsoft.com/en-us/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-edge-hub

8. Handle error "outofmemory" by disable optimized mode: https://docs.microsoft.com/en-us/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
