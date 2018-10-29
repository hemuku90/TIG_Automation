# HAWK-Client Setup
**Installation document for HAWK-Server**

# Installation

###### Requirements
    * Package may require root or administrator privileges in order to complete successfully.
    * Python 2.X or 3.X

###### Execution Step 
    we will create a sample file for run like a binary hawk which will call ansible in background

###### Configuration
    Brief Configuration setup on local or ansible host server(which i have termed as HAWK SDK) need to be updated as per OS 

# Input 
All server level config are managed by input.yaml file. 

###### telegraf

| name | description | type | default | required |
| ------ | ------ | ------ | ------ | ------ |
| install  | install telegraf | boolean | true | yes
| influx_endpoint | endpoint for influxdb | string | 127.0.0.1 | yes


Note : Similarly we have to create for all.