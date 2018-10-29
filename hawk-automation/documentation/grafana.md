# Setup Grafana Server
### 1.  Spin up an EC2/VM instance with Ubuntu 16.04

### 2. Login to Grafana Server from the Bastion Server. 
```
ssh -i <pem> ubuntu@<hostname>
```

### 3. Download and install the latest version of Grafana. 
```
# Add the following line to your /etc/apt/sources.list file.

deb https://packagecloud.io/grafana/stable/debian/ stretch main

# Use the above line even if you are on Ubuntu or another Debian version. There is also a testing repository if you want beta or release candidates.

deb https://packagecloud.io/grafana/testing/debian/ stretch main

# Then add the Package Cloud key. This allows you to install signed packages.

curl https://packagecloud.io/gpg.key | sudo apt-key add -

#Update your Apt repositories and install Grafana

sudo apt-get update
sudo apt-get install grafana -y

# On some older versions of Ubuntu and Debian you may need to install the 
apt-transport-https package which is needed to fetch packages over HTTPS.

sudo apt-get install -y apt-transport-https

sudo service grafana-server start

# To configure the Grafana server to start at boot time:

sudo update-rc.d grafana-server defaults

#To start the service using systemd:

systemctl daemon-reload
systemctl start grafana-server
systemctl status grafana-server
Enable the systemd service so that Grafana starts at boot.

sudo systemctl enable grafana-server.service
```
