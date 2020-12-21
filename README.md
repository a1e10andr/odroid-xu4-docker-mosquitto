# Odroid XU4 Armbian + Docker + Mosquitto

## Install Armbian for Odroid XU4 (Buster with Kernel 5.4)
https://redirect.armbian.com/region/EU/odroidxu4/Buster_current_minimal
Copy image to the SD Card

## Install Armbian for Odroid XU4 (Buster with Kernel 5.4)
### 1.1 Install firewall
sudo apt install ufw

### 1.2 Setup default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

### 1.3 Allow SSH connection
sudo ufw allow ssh

### 1.3 Run firewall
sudo ufw enable

## Install Docker
### 2.1 install armbian-config
sudo apt install armbian-config

### 2.2 install docker from armbian-config
sudo armbian-config
Navigate to [Software]->[Softy]->[Docker]
Press install

### 2.3 install docker-compose
sudo apt install docker-compose

### 2.4 enable docker as system service
sudo systemctl enable docker

## Install Docker Compose

### 3.1 Create working directory 'mqtt'
mkdir mqtt && cd mqtt

### 3.2 Create mosquitto subdirectories
mkdir config && mkdir data && mkdir logs

### 3.3 Create mosquitto configuration
touch ./config/mosquitto.conf
touch ./config/mosquitto.passwd

Default configuration file 
3.4 Write default configuration
>>
pid_file /var/run/mosquitto.pid

persistence true
persistence_location /mosquitto/data/

log_dest file /mosquitto/log//mosquitto.log
log_dest stdout

password_file /mosquitto/config//mosquitto.passwd
allow_anonymous false
<<

### 3.4 Create docker-compose.yaml
>>
version: '3'

services:
	mosquitto:
		image: eclipse-mosquitto
		container_name: mqtt
		network_mode: host
		ports:
			- 1883:1883
		volumes:
			- ./data:/mosquitto/data
			- ./logs:/mosquitto/logs
			- ./config:/mosquitto/config
		restart: unless-stopped
<<

3.5 Allow MQTT port i filrewall
sudo ufw allow 1883

3.6 Run docker
sudo docker-compose up -d

3.7 reate credentials:
sudo docker-compose exec mosquitto sh -c "mosquitto_passwd /mosquitto/config/passwd [username]"

Now after system reboot it must start auto

