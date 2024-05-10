# BlueChi workshop devconf.cz 2024

Requirements:
- podman installed (systemd in a container might be difficult: https://developers.redhat.com/blog/2019/04/24/how-to-run-systemd-in-a-container#)
- 


## Setup containers and bluechi

### Building pr pulling container image

```bash
# Either pull
podman pull quay.io/rh_ee_mengel/bluechi
# or build your own
podman build -t localhost/bluechi -f container/Containerfile .
```

### Starting containers

```bash
podman run -dt --rm --name controller --network host localhost/bluechi:latest
podman run -dt --rm --name worker1 --network host localhost/bluechi:latest
podman run -dt --rm --name worker2 --network host localhost/bluechi:latest
```

### Setup bluechi-controller

```bash
podman exec -it controller /bin/bash
vi /etc/bluechi/controller.conf.d/workshop.conf
```

Insert:
```ini
[bluechi-controller]
# The port the manager listens on to establish connections with the bluechi-agents.
ControllerPort=2020
# Comma separated list of unique bluechi-agent names which can connect to the controller.
AllowedNodeNames=worker1,worker2
# The level used for logging. Supported values are: DEBUG, INFO, WARN and ERROR.
LogLevel=DEBUG
```

Lets start it:
```bash
systemctl status bluechi-controller
systemctl start bluechi-controller
systemctl status bluechi-controller
```

### Setup bluechi-agents

For each worker do:

```bash
podman exec -it worker<no> /bin/bash
vi /etc/bluechi/agent.conf.d/workshop.conf
```

Insert:
```ini
[bluechi-agent]
# The unique name of this agent.
NodeName=worker<no>
# The IP address the bluechi-controller listens on.
ControllerHost=127.0.0.1
# The port bluechi-controller listens on.
ControllerPort=2020
# The level used for logging. Supported values are: DEBUG, INFO, WARN and ERROR.
LogLevel=DEBUG
```

Lets start it:
```bash
systemctl status bluechi-agent
systemctl start bluechi-agent
systemctl status bluechi-agent
```
