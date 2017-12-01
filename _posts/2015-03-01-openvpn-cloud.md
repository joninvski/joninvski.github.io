---
layout: post
title: Setup private VPN on the cloud
comments: True
type: "blog"
short_summary: "See how to quickly setup an openVpn container using docker and how to configure a Linux client to use that VPC"
keywords: ["docker", "bash"]

---

This post was heavily based on DigitalOcean instruction: [How To Run OpenVPN in a Docker Container on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-run-openvpn-in-a-docker-container-on-ubuntu-14-04?utm_medium=social&utm_source=twitter&utm_campaign=openvpn_docker_ubuntu_tut&utm_content=image)

### Setup host on the cloud with docker

First let's install an openVpn server on DigitalOcean and how to configure a Linux client.

1. If you don't have create a digital ocean account. They give you 10$ to start and have a great service. If you start a new account you can use my [referral link](https://www.digitalocean.com/?refcode=beed9a7630ab).

### Download docker-machine and create the remote host with docker

    # Fetch docker machine
    wget https://github.com/docker/machine/releases/download/v0.1.0/docker-machine_linux-amd64 -O docker-machine && chmod a+x docker-machine

    #Fetch the secretToken from digital cloud site. It's in Apps & API in Personal Acess Token. Just generate a new one

    # Create the docker machine host
    ./docker-machine create -d digitalocean --digitalocean-access-token=secretToken openVpn

    # Connect to the instance
    ./docker-machine ssh

### On cloud instance with docker running:

    OVPN_DATA="ovpn-data"
    docker run --name $OVPN_DATA -v /etc/openvpn busybox

    #Set the IP_OR_URL variable according to the IP or URL of the host machine
    IP_OR_URL="188.123.1.1"
    docker run --volumes-from $OVPN_DATA --rm kylemanna/openvpn ovpn_genconfig -u udp://${IP_OR_URL}:1194
    docker run --volumes-from $OVPN_DATA --rm -it kylemanna/openvpn ovpn_initpki
    docker run --volumes-from ovpn-data --rm -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn

    # Name the client as you want. In my case my laptop is called oscar
    CLIENT_NAME="OSCAR"
    docker run --volumes-from $OVPN_DATA --rm -it kylemanna/openvpn easyrsa build-client-full ${CLIENT_NAME} nopass
    docker run --volumes-from $OVPN_DATA --rm kylemanna/openvpn ovpn_getclient ${CLIENT_NAME} > ${CLIENT_NAME}.ovpn

### Back on the client:

    # Get the CLIENT_NAME.ovpn file
    # Use scp if you like

Then:

    sudo apt-get install openvpn
    sudo install -o root -m 400 CLIENTNAME.ovpn /etc/openvpn/CLIENTNAME.conf
    sudo /etc/init.d/openvpn restart
