
# Portainer Stacks

You will always want to create the required folders with your admin account first.
Create them in the `services` folder of this project.

## Nextcloud:
From the root of the project:
```shell
mkdir ./services/nextcloud_db
mkdir ./services/nextcloud_redis
```

If you are mounting a folder for the `/var/www/html/data`, make sure to create it's mount point with proper ownership.
```shell
mkdir ./services/nextcloud/data
sudo chown www-data:www-data ./services/nextcloud/data
```

Copy and paste the `nextcloud.yaml` file contents in a new `portainer` stack and choose `Advanced Mode` for the environment variables so you can paste what you have prepared from this template:

```
DOMAINNAME=domain.ltd
SUBDOMAIN=mycloud
CONFIG_HOME=/home/adminuser/traefik2-portainer/services
DATA_HOME=/home/adminuser/data
TZ=Pacific/Tahiti
MYSQL_ROOT_PASSWORD=jkahsdh56asd6767567sd675asd
MYSQL_DATABASE=nextcloud
MYSQL_USER=databseuser
MYSQL_PASSWORD=jha78y87dhahsd878768sdh8ashd87hd
TRUSTED_PROXIES=xxx.xxx.0.0/16
```
You will need to add your `traefik-servicenet` IPV4 IPAM Subnet as a trusted proxy for Nexctcloud. Simply go to the `Networks` page in `portainer` to get yours.

## Vaultwarden:
From [dani-garcia](https://github.com/dani-garcia)/**[vaultwarden](https://github.com/dani-garcia/vaultwarden)**
From the root of the project:
```shell
mkdir ./services/nextcloud_db
mkdir ./services/nextcloud_redis
mkdir ./services/nextcloud/data
```

Copy and paste the `vaultwarden.yaml` file contents in a new `portainer` stack and choose `Advanced Mode` for the environment variables so you can paste what you have prepared from this template:

```
DOMAINNAME=domain.ltd
SUBDOMAIN=mycloud
CONFIG_HOME=/home/adminuser/traefik2-portainer/services
TZ=Pacific/Tahiti
```

## Nginx:
From the root of the project:
```shell
mkdir ./services/nginx-staging
mkdir ./services/nginx-staging/cache
mkdir ./services/nginx-staging/pid
```

In order to easily modify the nginx global and server config you must also create a `nginx.conf` and `default.conf` files in `.services/nginx-staging/`. You can use the provided defaults.
```
cp .stacks/nginx.conf.example .services/nginx-staging/nginx.conf
cp .stacks/default.conf.example .services/nginx-staging/default.conf
```

Create a new user that will deploy the website code, use `adduser` to have a fully fledge user that can login and has a password.
```
sudo groupadd -g 2002 deployuser
sudo adduser -uid 2002 -gid 2002 deployuser
```

Give it any password and setup an ssh key for it if you need it to CI//CD deploy the code to this user. Create the web data folder:
```
su - deployuser
mkdir /home/deployuser/nginx-staging
exit
```

Copy and paste the `nginx.yaml` file contents in a new `portainer` stack and choose `Advanced Mode` for the environment variables so you can paste what you have prepared from this template:

```
DOMAINNAME=domain.ltd
SUBDOMAIN=staging
CONFIG_HOME=/home/adminuser/traefik2-portainer/services
TZ=Pacific/Tahiti
DEPLOY_USER=deployuser
```