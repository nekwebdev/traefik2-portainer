
# Portainer behind a hardened Traefik2 Proxy
Traefik2 setup to run as a non root user behind [Tecnativa](https://github.com/Tecnativa)/**[docker-socket-proxy](https://github.com/Tecnativa/docker-socket-proxy)** to access the docker socket. Most of the Traefik2 configuration is from [wollomatic](https://github.com/wollomatic)/**[traefik2-hardened](https://github.com/wollomatic/traefik2-hardened)**.
Also using [containrrr](https://github.com/containrrr)/**[watchtower](https://github.com/containrrr/watchtower)** to keep containers updated.

## Initial setup
### DNS setup
Traefik2 will be using the TLS challenge so you can not use wildcards and need a CNAME entry for all of your subdomains. 
### Setup Docker log rotation
Add the following values in `/etc/docker/daemon.json`. Create this file if it doesn’t exist. Adjust the values to fit.
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "10"
  }
}
```
Then reload the config and restart docker.
```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```
### Create Traefik2 user
Create a user named `traefik` with an id of `2000`, no home folder and no shell to prevent any login.
```shell
sudo useradd -u 2000 -M -s /usr/sbin/nologin traefik
```
### Create Traefik2 proxy network
We want to manually create this network. Now is a good time to add your admin user to the `docker` group so you do not have to use `sudo` for docker commands.
```shell
docker network create traefik-servicenet
```
### Setup .env and htpasswd files
- Set the correct values in `.env.example`, rename it to `.env` and change it's permissions `chmod 600 .env`
- Rename `./traefik/htpasswd.example` to `./traefik/htpasswd`
- Change it's permissions and ownership, change `admin` to your username.
```
chmod 640 ./traefik/htpasswd
sudo chown admin:traefik ./traefik/htpasswd
```
- Generate a htpasswd string using Bcrypt mode at https://hostingcanada.org/htpasswd-generator/ and replace the dummy string in `./traefik/htpasswd` with this one.
### Create acme.json file
Docker can not create new files to be mounted so we do it before hand and give it the proper ownership and permissions.
```shell
touch ./traefik/acme.json
chmod 600 ./traefik/acme.json
sudo chown traefik:traefik ./traefik/acme.json