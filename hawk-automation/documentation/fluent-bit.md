# Fluent-bit Agent installation on Ubuntu 16.04

Fluent-bit is the log forwarder agent which will push the log files from server to elasticsearch.

### 1. Installation
```
wget -qO - https://packages.fluentbit.io/fluentbit.key | sudo apt-key add -

echo "deb https://packages.fluentbit.io/ubuntu/xenial xenial main" >> /etc/apt/sources.list
apt-get update

apt-get install td-agent-bit -y

service td-agent-bit status
```

### 2. Configuration for fluent-bit
Fluent bit configuration file is stored at

/etc/fluent-bit/fluent-bit.conf

or 

/etc/td-agent-bit/td-agent-bit.conf

The configuration file for fluent-bit is stored at this [link](../configurations/fluent-bit.conf).