### What ?
###### Simply this is how to create a local registry mirror to reflect a public register to pull images through it and from public sectors and store them in centeralized storage.

### Why?
###### If you running a production environment you normally use proxy servers to access the public world and this is for security resons, but you might noticed that with a proxy configuration in docker daemon [/etc/systemd/system/docker.service.d/httpd-proxy.conf], many issues start to be present in the docker nodes behavior, specially:
> Once You need to demote a manager, you will not be able to promote it back.

### Root Cause:
###### Update to gRPC Liberary, starting from docker version 17.08

### Solutions for this issue are:
1. Adding managers IPs to Managers nodes by IPs and not hostnames to NO_PROXY in [/etc/systemd/system/docker.service.d/httpd-proxy.conf]
2. Remove the PROXY configuration from [/etc/systemd/system/docker.service.d/httpd-proxy.conf], but in this case you will not be able to access the external world from docker daemon and the proxy will always through an error to docker daemon.
3. To use an internal registry mirror for the external registry you need to access, for example: https://hub.docker.com

### Technical Steps:
###### If you want to use the docker-compose so copy this docker-compose.yml and adjust it to fit your environment:
```yaml
version: "3"
services:
  registry:
    image: registry:latest
    ports:
      - 5000:5000
    environment:
      - REGISTRY_PROXY_REMOTEURL="https:<add the one you need to access>"
      - HTTP_PROXY=http:<Your internal http proxy>
      - HTTPS_PROXY=http:<Your internal https proxy>
      - NO_PROXY="localhost,127.0.0.1"
      - no_proxy="localhost,127.0.0.1"
    volumes:
      - /var/lib/docker/registry:/var/lib/registry # Better to use a centerlaized storage
    restart: always
```
###### Then: run this docker-compose.yml and you are ready to GO :sparkles: :octocat:
1. Run this command in the directory where you saved your docker-compose.yml file in
```bash
sudo docker-compose up -d
```
2. If you need to make it as a docker service to run over swarm nodes.
    1. create a docker-compose file like we did.
    2. Run the following command:
    ```bash
    sudo docker stack deploy --compose-file docker-compose.yml registry
    ```
