

NS-3 5G Simulation Guide. By Kalid Muhumed & Ahmed Taher.

System Requirements
Ubuntu 22.04 (or WSL2 with Ubuntu) 8GB RAM minimum
4 CPU cores minimum
Required packages: git g++ python3 python3-pip cmake qt5-default mercurial libcurl openssl

Installation Steps

Install dependencies: sudo apt update
sudo apt install git g++ python3 python3-pip cmake qt5-default mercurial libcurl4-openssl-dev libssl-dev
pip3 install psutil paho-mqtt aiocoap requests

Install NS-3.43:
git clone https://gitlab.com/nsnam/ns-3-allinone.git cd ns-3-allinone
./download.py -n ns-3.43 cd ns-3.43
./waf configure --enable-examples ./waf build

Install mmWave module: cd ~
git clone https://github.com/nyuwireless-unipd/ns3-mmwave.git cd ns3-mmwave
./waf configure ./waf build

Simulation Setup

Create TAP interfaces:
sudo ip tuntap add mode tap user $USER name tap-client sudo ip tuntap add mode tap user $USER name tap-server sudo ip addr add 10.1.1.1/24 dev tap-client
sudo ip addr add 10.1.1.2/24 dev tap-server sudo ip link set tap-client up
sudo ip link set tap-server up

Generate certificates: mkdir certs
cd certs
openssl req -x509 -newkey rsa:2048 -nodes -keyout ca.key -out ca.crt -days 365 -subj "/CN=MyCA"
openssl req -newkey rsa:2048 -nodes -keyout server.key -out server.csr -subj "/CN=server" 

openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365
openssl req -newkey rsa:2048 -nodes -keyout client.key -out client.csr -subj "/CN=client" openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt
-days 365

Running Simulations

Start NS-3 simulations: cd ~/ns-3.43
sudo ./waf --run scratch/mqtt_sim sudo ./waf --run scratch/coap_sim sudo ./waf --run scratch/https_sim

Start servers:
cd ~/project/mqtt ./broker.py
cd ~/project/coap ./server.py
cd ~/project/https ./server.py

Start clients:
cd ~/project/mqtt ./client.py
cd ~/project/coap ./client.py
cd ~/project/https ./client.py

Important Notes
All nodes need static IPs
Certificates must be correctly installed
Use correct ports: 8883 for MQTT, 5684 for CoAP, 443 for HTTPS Protocols must use TLS 1.3 or DTLS 1.2
All measurements should be logged in CSV format
Run tests with 1000 messages per client at 10ms intervals
