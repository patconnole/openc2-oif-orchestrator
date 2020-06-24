# O.I.F. (OpenC2 Integration Fabric) Orchestrator Walk Through

This document provides a detailed walkthrough of the
installation, configuration, and basic operations of the OIF Orchestrator.

## System Preparation

Developers need the following tools to start working with
OIF:
 - Required:  [Python](https://www.python.org/), [Docker](https://www.docker.com/)
 - Optional: [git](https://git-scm.com/)

The OIF Orchestrator requires
[Python](https://www.python.org/) and
[Docker](https://www.docker.com/) to operate. Developers
should insure both of the following are installed on their
system:

- Docker, version 18 or higher
- Python, version 3.6 or higher

OIF Orchestrator also required [Docker
Compose](https://docs.docker.com/compose/), and
[pip](https://pip.pypa.io/en/stable/).  Docker Compose is
[installed with
Docker](https://docs.docker.com/compose/install/) on Windows
and Mac systems, but must be installed separately on Linux
systems. Pip is usually [installed with
Python](https://pip.pypa.io/en/stable/installing/).
Developers are advised to update all of the software
components to the latest versions.

Developers may optionally install [git](https://git-scm.com/)
version control software, as a means of obtaining the OIF
Orchestrator software.

## Obtain OIF Orchestrator Software

Developers must acquire a local copy of the OIF Orchestrator
software. There are two approaches for this:
 1. Clone the [OIF
    Orchestrator](https://github.com/oasis-open/openc2-oif-orchestrator)
    repository in the desired location:<br>
    `git clone
    https://github.com/oasis-open/openc2-oif-orchestrator.git`
 1. Download an ZIP archive by 
    1. Navigating to the
       [repository](https://github.com/oasis-open/openc2-oif-orchestrator).
	1. Click on the green **Clone** button.
	1. Select **Download ZIP**.
	1. Unwrap the ZIP archive in the desired location.

## Configure OIF Orchestrator Software

To configure the OIF Orchestrator, navigate to the directory
containing the local software copy and Run `configure.py`
with the desired options prior to starting the Orchestrator
for the first time. The available options are:
 - `-b` or `--build-image` -- Build base containers
 - `-d` or `--dev` -- Build using the development python image
 - `-f FILE` or `--log_file FILE` -- Enables logging to the designated file
 - `-h` or `--help` -- Shows the help and exits
 - `-v` or `--verbose` -- Enables verbose output    	

The configuration command is
`bash python configure.py [OPTIONS]`

> **QUESTION**: is there a recommend default for the
> configuration command?

## Container/Services ReadMe

The ReadMe files for OIF Orchestrator components are linked
here:

|Orchestrator   | Transport  | Logger  |
|:-:|:-:|:-:|
| [Core](../orchestrator/core/ReadMe.md)  | [HTTPS](../orchestrator/transport/https/README.md)  | [GUI](../logger/gui/ReadMe.md)  |
| [GUI](../orchestrator/gui/client/ReadMe.md)  | [MQTT](../orchestrator/transport/mqtt/ReadMe.md)  | [Server](../logger/server/ReadMe.md)  |





## Running OIF Orchestrator (Docker Compose)

As described in [its
documentation](https://docs.docker.com/compose/), Docker
Compose is use to "define and run multi-container Docker
applications". To start OIF Orchestrator in its default
configuration, the only required command is:

```bash
	docker-compose -f orchestrator-compose.yaml up
```

This command will:
 - Create the necessary Docker images as defined in the
   `orchestrator-compose.yml` configuration file
 - Execute the application in the defined containers,
   attached to the terminal from which it was launched  

OIF execution can be terminated `ctrl-C`.

The Orchestrator can also be started in detached mode using
the docker-compose `-d` or `--detach` option:

```bash
	docker-compose up --detach
```

A detached instance of OIF is terminated with the complementary command:

```bash
	docker-compose down
```
> **--------------------------------------------------------------------**

> **Note from Dave:** IMO for this walkthrough, which by
> definition supposed to be basic, we should remove the
> material below about Docker options and image building. I
> think those things go beyond "basic walkthrough". I've
> left it untouched for now and am jumping ahead to describe
> first time operation of the Orchestrator.

### Docker Options
- Options
	- `-f FILE` or `--file FILE` -- Specify an alternate compose file (default: docker-compose.yml)
	- `-p NAME` or `--project-name NAME` -- Specify an alternate project name (default: directory name)
	- `d` or `--detach` -- Detached mode: Run containers in the background, print new container names. Incompatible with --abort-on-container-exit.
- Starting
	- Run the `docker-compose` command for the Orchestrator as shown below

-  Stopping
	-  If running attached (showing log output, no -d option)
		-  Use 'Ctrl + C' 
	-  If running detached (not showing log output, -d option)
		-  Run the `docker-compose` that was used to start the Orchestrator **except** replace `up ...` with `down`
			
			```bash
			docker-compose ...... down
			```
- Building Images
	- Run the `docker-compose` that was used to start the Orchestrator **except** replace `up ...` with `build`
	- Options
		- SERVICE_NAME - The name of the service to rebuild the image, if not specified all will build
	- Notes
		- Does not need to be run prior to starting, the containers will autobuild if not available
		- Should be run after adding a new Protocol or Serialization
	
	```bash
	docker-compose ...... build [SERVICE_NAME]
	```

### Docker Compose Files
### Central Logging
- __Still in Beta__
- Run the `docker-compose` as normal with the additional option of a second '-f/--file'
- Allows for a central location for logging rather than the docker default of per container
- Runs on default port of 8081 for logger web GUI

	```bash
	docker-compose -f orchestrator-compose.yaml -f orchestrator-compose.log.yaml ...
	```

#### Orchestrator
- Use [`docker-compose`](https://docs.docker.com/compose/reference/overview/) to start the orchestrator on the system

	```bash
	docker-compose -f orchestrator-compose.yaml [-p NAME] up [-d]
    ```

> **--------------------------------------------------------------------**

## Accessing the Orchestrator GUI

The OIF Orchestrator provides graphical user interfaces
(GUIs) for both the user (i.e., for managing devices and
actuators, and creating and sending OpenC2 commands and
receiving responses) and for administration (i.e., `need
developer input here`). The GUIs are accessed as follows:
 - User GUI `https://localhost:8080`
 - Admin GUI `https://localhost:8081`

> **QUESTION:** are the user and admin GUIs HTTP or HTTPS?

> **QUESTION:** what's the different between "Admin" and
> "logger", if any?

For both GUIs, the login credentials are 
 - Username: `admin`
 - Password: `password`



### Registration
#### Registering a device with the OIF
- Give Device a name and generate a UUID for it.
- Select a transport
    - HTTPS: Enter host and port (Default Port 5001)
    - MQTT: Enter host and port of the broker (Default Port 1883)
- Select which serializations in which the device utilizes.
    - Default included device supports JSON, CBOR, and XML.
- Note: include a note about what type of device you are adding.

#### Registering an actuator with the OIF
- Give actuator a name and generate a UUID for it.
- Select a parent device.
    -  Note: device should be registered before the actuator.
- Upload/Copy-Paste schema. Schema for the default included ISR actuator can be found at [device/actuator/isr/act_server/schema.json](../device/actuator/isr/act_server/schema.json).
- This information can also be found under the [ISR Actuator](../device/actuator/isr/ReadMe.md) page.
- If you are registering a new actuator for the first time while utilizing the MQTT transport you may need to update the `MQTT_TOPICS` environment variable. Read the MQTT Topics section [here](transport/mqtt/ReadMe.md)