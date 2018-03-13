# Example configs for Mesos + Marathon

This directory provides a [`docker-compose.yml`](docker-compose.yml) file that
can be used to run [Mesos](http://mesos.apache.org/) and
[Marathon](https://mesosphere.github.io/marathon/). It also provides a
Marathon config file for running a sample web app, once the docker
cluster is initialized.

## Get Docker IP address

The `$DOCKER_IP` environment variable is required by the docker-compose file.
The command to obtain this variable may vary depending on your docker setup.

```bash
export DOCKER_IP="$(docker run --rm -t --net=host alpine ip addr show docker0 | grep 'inet ' | awk '{print $2}' | cut -f1  -d'/')"
```

### IMPORTANT: Docker for mac

Use special Mac-only DNS name `docker.for.mac.localhost` which will resolve to the internal IP address used by the host. It works from Docker Guest <> Docker Host (biderectional)

```bash
export DOCKER_IP=docker.for.mac.localhost
```
**Note**: Since 17.12 onwards the recommendation is to connect to the special Mac-only DNS name `docker.for.mac.host.internal`, which resolves to the internal IP address used by the host. But when it was tested just works from Docker Guest >> Docker Host (one direction only). Ref: see [Docker Docs I cannot ping my containers](https://docs.docker.com/docker-for-mac/networking/#i-cannot-ping-my-containers)

## Boot Mesos + Marathon

```bash
docker-compose up -d

open http://localhost:5050
open http://localhost:8080
```

## Deploy webapp, linkerd, and linkerd-viz

```bash
# deploy webapp
curl -H "Content-type: application/json" -X POST http://localhost:8080/v2/apps -d @webapp.json
```


## Test webapp

```bash
# locate the exposed port by the webapp
$docker ps

CONTAINER ID        IMAGE                                              COMMAND                  CREATED              STATUS              PORTS                                                  NAMES
146652b23bf7        python:3                                           "/bin/sh -c '/bin/..."   39 seconds ago       Up 38 seconds       0.0.0.0:7771->7771/tcp                                 mesos-af7ebbc2-b306-44a6-8f9f-1eb4cdff916a-S0.82126716-08a3-4a3e-9944-fa821a5dc84a
ae981726a09e        python:3                                           "/bin/sh -c '/bin/..."   44 seconds ago       Up 43 seconds       0.0.0.0:7767->7767/tcp                                 mesos-af7ebbc2-b306-44a6-8f9f-1eb4cdff916a-S0.cdd583b5-a22d-4268-b999-c0b6f63c2df8
aa62e0c04121        mesosphere/marathon:v1.3.0                         "./bin/start --mas..."   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp                                 mesosmarathon_marathon_1
2af1d4e00bfb        mesosphere/mesos-slave:1.1.0-2.0.107.ubuntu1404    "mesos-slave --res..."   About a minute ago   Up About a minute   0.0.0.0:5051->5051/tcp                                 mesosmarathon_mesos-agent_1
9c44c73512b1        mesosphere/mesos-master:1.1.0-2.0.107.ubuntu1404   "mesos-master --re..."   About a minute ago   Up About a minute   0.0.0.0:5050->5050/tcp                                 mesosmarathon_mesos-leader_1
7c3a309e7eae        netflixoss/exhibitor:1.5.2                         "java -jar exhibit..."   4 hours ago          Up About a minute   2888/tcp, 3888/tcp, 0.0.0.0:2181->2181/tcp, 8080/tcp   mesosmarathon_zookeeper_1

# access to the webapp
curl -I http://localhost:7771
curl -I http://localhost:7767
```

Or directly from the browser under

* http://localhost:7771
* http://localhost:7767
