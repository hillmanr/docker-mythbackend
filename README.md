MythTV backend
==============

Docker container to run a MythTV backend. It also includes MythWeb and HDHomeRun utils.
This container is adapted from an0t8/mythtv-server to get a more recent MythTV version and to add volumes for recordings and video.
Note that this container does not include a MySQL or MariaDB database. You must set up a database before (no need to create the mythconverg database as it is automatically created if it does not exist).

## Usage

Launch the container via docker:
```
sudo docker run -d --name mythbackend \
        -p "3389:3389" -p "5000:5000/udp" -p "6543:6543" -p "6544:6544" \
        -v "/path/to/recordings:/mnt/recordings" \
        -v "/path/to/video:/mnt/video" \
        -v "/path/to/mythtv-home:/home/mythtv" \
        -v "/path/to/mythtv-backups:/var/lib/mythtv/db_backups" \
        -e "USER_ID=1001" \
        -e "GROUP_ID=1001" \
        -e "DATABASE_HOST=dbhost" \
        -e "DATABASE_PORT=3306" \
        -e "DATABASE_ROOT=root" \
        -e "DATABASE_ROOT_PWD=rootpassword" \
        -e "TZ=Europe/Paris" \
        -h mythbackend \
        --network="host" \
        thomfab/docker-mythbackend
```

Below are some remarks about the parameters.

## Ports

* Port 3389 is used for RDP access to the Mate desktop. Mainly used to launch mythtv-setup
* Port 5000/udp is used for UPNP
* Port 6543 and 6544 are used by MythTV (API and web GUI)

## Volumes

* /mnt/recordings can be used to store recordings (configure MythTV to point to this folder)
* /mnt/video can be used to store videos (configure MythTV to point to this folder)
* /home/mythtv is mainly useful to persist the .mythtv folder where configuration is stored like connection to the database
* /var/lib/mythtv/db_backups will hold backups of the database that are generated weekly by MythTV

## Environment variables

* USER_ID and GROUP_ID : used to match IDs defined on the Docker host so that UNIX rights are correct when accessing the volumes
* DATABASE_HOST and DATABASE_PORT : the host (and port) where the mythconverg database is. If the database exists it is untouched, if not it is created
* DATABASE_ROOT and DATABASE_ROOT_PWD : credentials used to create the database if needed
* TZ : to set the correct timezone

## Network parameters

* hostname (-h) : MythTV uses a hostname to match the parameters in the database so it is better (almost mandatory) to define your own hostname than to use the hash generated by Docker. This is the "hostname" of the container. My advice is to NOT choose the same name as the Docker host or a name of another computer on your network (be creative !).
* host network option (--network="host") : this option is not mentioned in other MythTV docker images on Docker Hub but my tests showed that without it the web GUI is not accessible and a frontend cannot connect to the backend (an error appears in the log saying the connection to the backend was lost).
