# Setup of InfluxDB Server with Best Practices. 

### 1. Spin up an EC2 instance/VM and tag it as Influx Server. Also mount a 100 GB volume for data and 50 GB volume for wal directories used by Influxdb. 

### 2. Login to influx server
```
chmod 400 <pem>
ssh -i <pem> username@<hostname>
# set proper hostname of the machine FQDN.eg. influxdb.prod.internal.com
vim /etc/hostname ## enter the hostname in the file
vim /etc/hosts ## make an entry in front of 127.0.0.1 line
sudo hostname  influxdb.prod.internal.com
# Re-login the shell or logout and login
```

### 3. Setup InfluxDB
```
sudo apt-get update

curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release

echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt-get update && sudo apt-get install influxdb

#Stop influxdb as we need to do some configuration changes in it. 
sudo service stop influxdb 
```
InfluxDB maintains two directories:

1. **Data directory**:-  /var/lib/influxdb/data and 

2. **WAL directory** :- /var/lib/influxdb/wal 

### 4. Check if filesystem is created on the unmounted EBS volumes that we have created during the creation of our EC2 instance. 
```
lsblk 

# Suppose the two disk added were named as /dev/nvme1n1 and /dev/nvme2n1

sudo file -s /dev/nvme1n1

sudo file -s /dev/nvme2n1

# If commands in Step 3 return “data” as result create file system on both partitions. 

sudo mkfs -t ext4 /dev/nvme1n1

sudo mkfs -t ext4 /dev/nvme2n1

# Mount the volumes on the partitions. 

mount /dev/nvme1n1 /var/lib/influxdb/data

mount /dev/nvme2n1 /var/lib/influxdb/wal 

# Make an entry in /etc/fstab or /etc/rc.local to persist the mount points after the server restart.
```

### 5. Start Influx DB Service
```
sudo service influxdb start
```

**Note:** To change any configuration before starting the service. Go to the configuration file for Influx. 
```
vim /etc/influxdb/influxdb.conf
```

### 6. Create InfluxDB Database and Set Retention Policies
```
CREATE DATABASE "<database Name>" WITH DURATION <Duration to keep data in InfluxDB> REPLICATION <Replication Factor>

# Eg. CREATE DATABASE "NOAA_water_database" WITH DURATION 3d REPLICATION 1
```
**Note on Retention Policy**:The part of InfluxDB’s data structure that describes for how long InfluxDB keeps data (duration), how many copies of those data are stored in the cluster (replication factor), and the time range covered by shard groups (shard group duration). RPs are unique per database and along with the measurement and tag set define a series.

When you create a database, InfluxDB automatically creates a retention policy called autogen with an infinite duration, a replication factor set to one, and a shard group duration set to seven days. See Database Management for retention policy management.

Retention Policy on Influxdb can be changed multiple times using commands as below(Optional). 
```
CREATE RETENTION POLICY "rp1" ON "db" DURATION 10d REPLICATION 1

ALTER RETENTION POLICY "rp" ON "db" DURATION 30d REPLICATION 1

drop RETENTION POLICY "autogen" ON "db"
```
