# Elasticsearch and Kibana Installation on Ubuntu 16.04

### 1. Requirements
For this tutorial to work, there are a couple of requirements:

- An Ubuntu 16.04 Server
- A user with sudo privileges

### 2. Update the system and install necessary packages
```
sudo apt update && apt -y upgrade

sudo apt install apt-transport-https software-properties-common wget
```

### 3. Install Oracle Java JDK via PPA
We will use the PPA repository maintained by the Webupd8 Team. The install script will ask you to accept the license agreement and it will download the Java archive file from the Oracle download page and set everything up for you.

To add the Webupd8 Team PPA repository, run the following commands on your server:
```
sudo add-apt-repository ppa:webupd8team/java

sudo apt update

#You can now install JDK8 with the following command:

sudo apt install oracle-java8-installer -y

#To check if everything is set correctly, run:

java -version
and you should see something like the following:

java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```
### 4. Install and configure Elasticsearch
We will install Elasticsearch using the package manager from the Elastic repository.
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list

sudo apt update

sudo apt install elasticsearch

Once the installation is completed, open the elasticsearch.yml file and restrict the remote access to the Elasticsearch instance:

sudo vim /etc/elasticsearch/elasticsearch.yml

network.host: localhost
Start the Elasticsearch service and set it to automatically start on boot:

sudo systemctl restart elasticsearch

sudo systemctl enable elasticsearch
```

### 5. Install and configure Kibana
Same as Elasticsearch, we will install Kibana using the package manager from the Elastic repository.
```
sudo apt install kibana

# Once the installation is completed, open the kibana.yml file and restrict the remote access to the Kibana instance:

sudo vim /etc/kibana/kibana.yml

server.host: "localhost"

# Start the Elasticsearch service and set it to start automatically on boot:

sudo systemctl restart kibana
sudo systemctl enable kibana

#Kibana will now run on localhost on port 5601
```

### 6. Install and configure Nginx as a reverse proxy
We will use Nginx as a reverse proxy to access Kibana from the public IP address. To install Nginx, run:
```
sudo apt-get install nginx -y

#Create a basic authentication file with the OpenSSL command:

echo "admin:$(openssl passwd -apr1 YourStrongPassword)" | sudo tee -a /etc/nginx/htpasswd.kibana

# Note: always use a strong password.


sudo rm /etc/nginx/sites-enabled/default
and create a virtual host configuration file for our Kibana instance:

sudo vim /etc/nginx/sites-available/kibana

server {
    listen 80 default_server;
    server_name _;
    
    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/htpasswd.kibana;
 
    location / {
        proxy_pass http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# Activate the server block by creating a symbolic link:

sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana

# Test the Nginx configuration and restart nginx:

sudo nginx -t
sudo service nginx restart
```

That’s it. You have successfully installed the ELK Stack on your Ubuntu 16.04.
