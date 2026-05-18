# OpenAgri Bootstrap Deployment
🇪🇺
*"This repository was created in the context of OpenAgri project (https://horizon-openagri.eu/). OpenAgri has received funding from the EU’s Horizon Europe research and innovation programme under Grant Agreement no. 101134083."*


This repository serves to bootstrap the deployment process of OpenAgri services with a modular configuration.

# Dependencies
This project depends on docker (version > 20.10.0) and Python (2 or 3).

# Raspberry Pi (ARM64) Deployment

Deploying on a Raspberry Pi is supported, but requires the following prerequisites:

**64-bit OS is mandatory.** Several services (MongoDB in particular) have no 32-bit ARM builds. Running 32-bit Raspberry Pi OS (armhf) will cause containers to exit immediately with code 159, even if the kernel reports `aarch64`.

ARM64-specific service files are provided in `available_services_arm64/`. These differ from the standard files in two ways: all services have `platform: linux/arm64` set explicitly (working around Docker's platform auto-detection on some Pi configurations), and PDM runs without Gatekeeper (`USING_GATEKEEPER=False`). Currently available:

- `pestmanagement.yml`
- `weatherservice.yml`

To deploy these on a Raspberry Pi, copy from `available_services_arm64/` instead of `available_services/`:
```
$ cp available_services_arm64/pestmanagement.yml services_in_use/
$ cp available_services_arm64/weatherservice.yml services_in_use/
```
Then follow the standard setup and running instructions below.

# Setup
1. Copy any service YAML (.yml) file from the `available_services` directory into the `services_in_use` directory.
2. Copy the `example.env` to a new file called `.env`. In this new file change the configurations of the selected services to meet your deployment scenario. We strongly suggest changing configurations for the default usernames and passwords of the services used.
3. To setup a local deployment, run the command: `$ python run_compose.py deploy-localhost`. This will automatically create a `docker-compose.override.yml` with the necessary services ports exposed to the host machine (i.e., have them accessible through `http://localhost:<service-port>`). These service ports are defined by the `<SERVICE_NAME>_APP_PORT` variable in the `.env` configuration file (e.g., `GATEKEEPER_APP_PORT=8001`).

## Fine Tuning Setup
Any fine tuning on the deployment setup for the services can be done by overriding the individual services configurations on the `docker-compose.override.yml` and/or changing the environment variable in the `.env` configuration files.

# Running
To run all, execute the following command:
```
$ python run_compose.py [docker_compose_commands]
```
replacing `[docker_compose_commands]` with the actual docker compose command you wish to use.

Bellow is a list of examples:

## Update Local Docker Images
Update images from all services listed on `services_in_use`:
```
$ python run_compose.py pull
```
Update the image of only a single service (e.g., gatekeeper):

```
$ python run_compose.py pull gatekeeper
```

## Starting Services in Background
Start in background all services listed on `services_in_use`:
```
$ python run_compose.py up -d
```
Start in background only a single service (e.g., gatekeeper):
```
$ python run_compose.py up -d gatekeeper
```

## Tearing Down Services
Stop and remove containers from all services listed on `services_in_use`:
```
$ python run_compose.py down
```
Stop and remove container of only a single service (e.g., gatekeeper):
```
$ python run_compose.py down gatekeeper
```


## Start/Stop Services
To stop all running container (without removing it):
```
$ python run_compose.py stop
```
To stop a specific running container (without removing it) (e.g., gatekeeper):
```
$ python run_compose.py stop gatekeeper
```

Similarly, to start all stopped container:
```
$ python run_compose.py start
```
or if re-starting a specific service(e.g., gatekeeper)
```
$ python run_compose.py start gatekeeper
```

# License
This project code is licensed under the EUPL 1.2 license, see the LICENSE file for more details.
Please note that each service may have different licenses, which can be found their specific source code repository.
