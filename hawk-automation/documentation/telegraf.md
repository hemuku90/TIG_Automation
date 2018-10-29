# Telegraf Setup on Ubuntu 16.04

### 1. Setup
Add the InfluxData repository with the following commands:
```
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -

source /etc/lsb-release

echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
  ```

### 2. Install and start the Telegraf service:
```
sudo apt-get update && sudo apt-get install telegraf
  
sudo service telegraf start
```

### 3. Telegraf Configuration file is stored at /etc/telegraf/telegraf.conf
```
vim /etc/telegraf/telegraf.conf

# Do the changes & restart the service

sudo service telegraf restart
```