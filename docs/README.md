# OpenC2 Integration Framework OIF

## Overview

The OpenC2 Integration Framework (OIF) is a project that 
enables developers to create and test OpenC2 specifications
and implementations without having to recreate an entire
OpenC2 ecosystem. OIF consists of two major parts. The
["OIF Orchestrator" (this
repository)](https://github.com/oasis-open/openc2-oif-orchestrator)
which functions as an OpenC2 producer and the "[OIF
Device](https://github.com/oasis-open/openc2-oif-device)"
which functions as an OpenC2 consumer. Due to port bindings
it is recommended that the Orchestrator and the Device not
be run on the same machine without careful handling of the
container ports.

This document contains the information necessary for experienced
developers to begin working with the OIF Orchestrator. A
more detailed start-up explanation can be found in
[WALKTHROUGH.md](./WALKTHROUGH.md)

## Container/Services ReadMe

At runtime, OIF Orchestrator utilizes at least four Docker containers. This architecture allows OIF to support multiple transports and multiple consumer-clients at the same time. The main orchestrator-compose.yaml defines which containers are used, and their respective Dockerfiles and sources are located in the orchestrator subdirectories core, gui, and transport. As hinted by the name, Core is the brains of the operation. Transport containers (https, mqtt, etc) terminate all communication with any remote OpenC2 Consumer(s), then forward incoming messages to a RabbitMQ queue container, from which Core pulls. Outgoing communications are published to the queue, with each transport container waiting on the queue for their respective messages to send out.

A basic OIF instantiation would have the user interacting with the GUI container using a web-browser, with Core, HTTPS, RabbitMQ containers doing the real work behind the scenes. 


The ReadMe files for the OIF Orchestrator containers are linked here:

|Orchestrator   | Transport  | Logger  |
|:-:|:-:|:-:|
| [Core](../orchestrator/core/ReadMe.md)  | [HTTPS](../orchestrator/transport/https/README.md)  | [GUI](../logger/gui/ReadMe.md)  |
| [GUI](../orchestrator/gui/client/ReadMe.md)  | [MQTT](../orchestrator/transport/mqtt/ReadMe.md)  | [Server](../logger/server/ReadMe.md)  |



## OIF Container/Service Defaults
### GUI User Credentials

> Note: Admin and User GUI use the same default credentials
> but represent different logins.

* Username: admin
* PW: password

### Ports
The following ports are configured as defaults for OIF functions: 
- 8080 - OIF GUI: Access the OIF Orchestrator GUI in a web browser
- 5000 - HTTPS Listener: Consumer Devices send messages to this port
- 8081 - Logger GUI (beta)
- 8080/api - OIF API


## System Requirements
Minimum requirements to run the OIF Orchestrator
- Docker v18+
- Docker-Compose v1.20+
- Python 3.6+
- pip 18+

## Running the Orchestrator
### Configuration
- Run `configure.py` for a default installation. Advanced users may use the options below:
	- Options
		- `-b` or `--build-image` -- Build base containers
		- `-d` or `--dev` -- Build using the development python image
    	- `-f FILE` or `--log_file FILE` -- Enables logging to the designated file
    	- `-h` or `--help` -- Shows the help and exits
    	- `-v` or `--verbose` -- Enables verbose output
	```bash
	python configure.py
	```


### General Info
- Start:
	- Run the docker-compose `up` command in the same directory as orchestrator-compose.yaml:
		- Options
			- `-f FILE` or `--file FILE` -- Specify an alternate compose file (default: docker-compose.yml)
			- `-p NAME` or `--project-name NAME` -- Specify an alternate project name (default: directory name)
			- `d` or `--detach` -- Detached mode: Run containers in the background, print new container names. Incompatible with --abort-on-container-exit.
	```bash
	docker-compose -f orchestrator-compose.yaml -p Orchestrator up
	```

- Open GUI:
	- Open a web-browser to http://localhost:8080 and login to OIF Orchestrator
	- Quickstart! Register a device and accompanying actuator, upload a json schema, and start publishing OpenC2 Commands!

- Stop:
	- If running attached (showing log output, no -d option), first use 'Ctrl + C'
	```bash
	docker-compose -f orchestrator-compose.yaml -p Orchestrator down
	```


- Build Images:
	- Images are built automatically on your first run, but you can build them manually too.
	- Options
        - SERVICE_NAME - The name of the service to rebuild
          the image, if not specified all services will
          build
	- Notes
		- Should be run after adding a new Protocol or Serialization
	```bash
	docker-compose -f orchestrator-compose.yaml -p Orchestrator build
	```


### Docker Compose Files

#### Central Logging (*Still In Beta*)
- Run the `docker-compose` as normal with the additional option of a second `-f/--file`
- Allows for a central location for logging rather than the Docker default of per container
- Runs on default port of 8081 for logger web GUI

	```bash
	docker-compose -f orchestrator-compose.yaml -f orchestrator-compose.log.yaml
	```


### Registration

In the OIF, Devices are containers for one or more
Actuators. In order to send OpenC2 commands to an actuator,
you must register a device to contain the actuator, and then
register an actuator and associate it with a device.

> __NOTE:__  Device and Actuator identifiers within OIF *must* be
> UUIDs, even though the OpenC2 language does not include
> such a requirement.

#### Registering Devices
- Give the Device a name and generate a UUID for it.
- Select a transport
    - HTTPS: Enter host and port (Default Port 5001)
    - MQTT: Enter host and port of the broker (Default Port 1883)
- Select which serializations in which the device utilizes.
    - Default included device supports JSON, CBOR, and XML.
- Note: include a brief description about what type of device you are adding.

#### Registering Actuators
> Note: a device should be registered before the actuator.
- Give the actuator a name and generate a UUID for it.
- Select a parent device.
    
- Upload/Copy-Paste schema. Schema for the default included ISR actuator can be found at [device/actuator/isr/act_server/schema.json](../device/actuator/isr/act_server/schema.json).
- This information can also be found under the [ISR Actuator](../device/actuator/isr/ReadMe.md) page.
- If you are registering a new actuator for the first time
  while utilizing the MQTT transport you may need to update
  the `MQTT_TOPICS` environment variable. Read the MQTT
  Topics section [here](transport/mqtt/ReadMe.md)

## Extending OIF

OIF is intended as a platform for exploration and
development of OpenC2, and is designed to be be extended.
Follow these links for tutorials for adding new
* [message transfer protocols](./Transport.md), and 
* [content serializations](./Serializations.md). 