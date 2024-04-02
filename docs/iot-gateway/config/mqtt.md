---
layout: docwithnav-gw
title: MQTT Connector Configuration
description: MQTT protocol support for ThingsBoard IoT Gateway
redirect_from: 
  - "/docs/iot-gateway/mqtt/"  
  - "/docs/iot-gateway/resources/mqtt-gui-extension-configuration.json"

---

* TOC
{:toc}

This guide will help you to get familiar with MQTT Connector configuration for ThingsBoard IoT Gateway.
Use [general configuration](/docs/iot-gateway/configuration/) to enable this Connector. 
The purpose of this Connector is to connect to an external MQTT broker and subscribe to data feed from devices. 
The connector is also able to push data to MQTT brokers based on the updates/commands from ThingsBoard. 

This connector is useful when you have local MQTT broker in your facility or corporate network and you would like to push data from this broker to ThingsBoard.

We will describe connector configuration file below.

## Connector configuration: mqtt.json

Connector configuration is a JSON file that contains information about how to connect to external MQTT broker, 
what topics to use when subscribing to data feed and how to process the data. 
Let's review the format of the configuration file using the example below.

<b>Example of MQTT Connector config file.</b>

The example listed below will connect to MQTT broker in a local network deployed on server with IP 192.168.1.100. 
Connector will use basic MQTT auth using username and password. 
Then, connector will subscribe to a list of topics using topic filters from the mapping section. See more info in the description below.    

{% capture mqttConf %}
{
   "broker":{
      "name":"Default Local Broker",
      "host":"127.0.0.1",
      "port":1883,
      "clientId":"ThingsBoard_gateway",
      "version":5,
      "maxMessageNumberPerWorker":10,
      "maxNumberOfWorkers":100,
      "sendDataOnlyOnChange":false,
      "security":{
         "type":"basic",
         "username":"user",
         "password":"password"
      }
   },
   "dataMapping":[
      {
         "topicFilter":"sensor/data",
         "subscriptionQos":1,
         "converter":{
            "type":"json",
            "deviceInfo":{
               "deviceNameExpressionSource":"message",
               "deviceNameExpression":"${serialNumber}",
               "deviceProfileExpressionSource":"message",
               "deviceProfileExpression":"${sensorType}"
            },
            "sendDataOnlyOnChange":false,
            "timeout":60000,
            "attributes":[
               {
                  "type":"string",
                  "key":"model",
                  "value":"${sensorModel}"
               },
               {
                  "type":"string",
                  "key":"${sensorModel}",
                  "value":"on"
               }
            ],
            "timeseries":[
               {
                  "type":"string",
                  "key":"temperature",
                  "value":"${temp}"
               },
               {
                  "type":"double",
                  "key":"humidity",
                  "value":"${hum}"
               },
               {
                  "type":"string",
                  "key":"combine",
                  "value":"${hum}:${temp}"
               }
            ]
         }
      },
      {
         "topicFilter":"sensor/+/data",
         "subscriptionQos":1,
         "converter":{
            "type":"json",
            "deviceInfo":{
               "deviceNameExpressionSource":"topic",
               "deviceNameExpression":"(?<=sensor/)(.*?)(?=/data)",
               "deviceProfileExpressionSource":"constant",
               "deviceProfileExpression":"Thermometer"
            },
            "sendDataOnlyOnChange":false,
            "timeout":60000,
            "attributes":[
               {
                  "type":"string",
                  "key":"model",
                  "value":"${sensorModel}"
               }
            ],
            "timeseries":[
               {
                  "type":"double",
                  "key":"temperature",
                  "value":"${temp}"
               },
               {
                  "type":"string",
                  "key":"humidity",
                  "value":"${hum}"
               }
            ]
         }
      },
      {
         "topicFilter":"sensor/raw_data",
         "subscriptionQos":1,
         "converter":{
            "type":"bytes",
            "deviceInfo":{
               "deviceNameExpressionSource":"message",
               "deviceNameExpression":"[0:4]",
               "deviceProfileExpressionSource":"constant",
               "deviceProfileExpression":"default"
            },
            "sendDataOnlyOnChange":false,
            "timeout":60000,
            "attributes":[
               {
                  "type":"raw",
                  "key":"rawData",
                  "value":"[:]"
               }
            ],
            "timeseries":[
               {
                  "type":"raw",
                  "key":"temp",
                  "value":"[4:]"
               }
            ]
         }
      },
      {
         "topicFilter":"custom/sensors/+",
         "subscriptionQos":1,
         "converter":{
            "type":"custom",
            "extension":"CustomMqttUplinkConverter",
            "cached":true,
            "extensionConfig":{
               "temperature":2,
               "humidity":2,
               "batteryLevel":1
            }
         }
      }
   ],
   "requestsMapping":{
      "connectRequests":[
         {
            "topicFilter":"sensor/connect",
            "deviceInfo":{
               "deviceNameExpressionSource":"message",
               "deviceNameExpression":"${serialNumber}",
               "deviceProfileExpressionSource":"constant",
               "deviceProfileExpression":"Thermometer"
            }
         },
         {
            "topicFilter":"sensor/+/connect",
            "deviceInfo":{
               "deviceNameExpressionSource":"topic",
               "deviceNameExpression":"(?<=sensor/)(.*?)(?=/connect)",
               "deviceProfileExpressionSource":"constant",
               "deviceProfileExpression":"Thermometer"
            }
         }
      ],
      "disconnectRequests":[
         {
            "topicFilter":"sensor/disconnect",
            "deviceInfo":{
               "deviceNameExpressionSource":"message",
               "deviceNameExpression":"${serialNumber}"
            }
         },
         {
            "topicFilter":"sensor/+/disconnect",
            "deviceInfo":{
               "deviceNameExpressionSource":"topic",
               "deviceNameExpression":"(?<=sensor/)(.*?)(?=/connect)"
            }
         }
      ],
      "attributeRequests":[
         {
            "retain":false,
            "topicFilter":"v1/devices/me/attributes/request",
            "deviceInfo":{
               "deviceNameExpressionSource":"message",
               "deviceNameExpression":"${serialNumber}"
            },
            "attributeNameExpressionSource":"message",
            "attributeNameExpression":"${versionAttribute}, ${pduAttribute}",
            "topicExpression":"devices/${deviceName}/attrs",
            "valueExpression":"${attributeKey}: ${attributeValue}"
         }
      ],
      "attributeUpdates":[
         {
            "retain":true,
            "deviceNameFilter":".*",
            "attributeFilter":"firmwareVersion",
            "topicExpression":"sensor/${deviceName}/${attributeKey}",
            "valueExpression":"{\"${attributeKey}\":\"${attributeValue}\"}"
         }
      ],
      "serverSideRpc":[
         {
            "type":"twoWay",
            "deviceNameFilter":".*",
            "methodFilter":"echo",
            "requestTopicExpression":"sensor/${deviceName}/request/${methodName}/${requestId}",
            "responseTopicExpression":"sensor/${deviceName}/response/${methodName}/${requestId}",
            "responseTopicQoS":1,
            "responseTimeout":10000,
            "valueExpression":"${params}"
         },
         {
            "type":"oneWay",
            "deviceNameFilter":".*",
            "methodFilter":"no-reply",
            "requestTopicExpression":"sensor/${deviceName}/request/${methodName}/${requestId}",
            "valueExpression":"${params}"
         }
      ]
   }
}
{% endcapture %}
{% include code-toggle.liquid code=mqttConf params="conf|.copy-code.expandable-20" %}

### Section "broker"

| **Parameter**        | **Default value**        | **Description**                                                                             |
|:---------------------|:-------------------------|---------------------------------------------------------------------------------------------|
| name                 | **Default Local Broker** | Broker name for logs and saving to persistent devices.                                      |
| host                 | **localhost**            | Mqtt broker hostname or ip address.                                                         |
| port                 | **1883**                 | Mqtt port on the broker.                                                                    |
| clientId             | **ThingsBoard_gateway**  | This is the client ID. It must be unique for each session.                                  |
| version              | **5**                    | MQTT protocol version.                                                                      |
| sendDataOnlyOnChange | **false**                | Sending only if data changed from last check, if not – data will be sent after every check. |
| ---                  |                          |                                                                                             |

#### Subsection "security"

Subsection "security" provides configuration for client authorization at Mqtt Broker.
 
{% capture mqttconnectorsecuritytogglespec %}
Basic<small>Recommended</small>%,%accessToken%,%templates/iot-gateway/mqtt-connector-basic-security-config.md%br%
Anonymous<small>No security</small>%,%anonymous%,%templates/iot-gateway/mqtt-connector-anonymous-security-config.md%br%
Certificates<small>For advanced security</small>%,%tls%,%templates/iot-gateway/mqtt-connector-tls-security-config.md{% endcapture %}

{% include content-toggle.liquid content-toggle-id="mqttConnectorCredentialsConfig" toggle-spec=mqttconnectorsecuritytogglespec %}  

### Section "dataMapping"

This configuration section contains an array of topics that the gateway will subscribe to after connecting to the broker, along with settings about processing incoming messages (converter).

| **Parameter** | **Default value** | **Description**                |
|:--------------|:------------------|--------------------------------|
| topicFilter   | **sensor/data**   | Topic address for subscribing. |
| ---           |                   |                                |

The **topicFilter** supports special symbols: '#' and '+', allowing to subscribe to multiple topics.

Also, MQTT connector supports shared subscriptions. 
To create shared subscription you need to add "**$share/**" as a prefix for topic filter and shared subscription group name.
For example to subscribe to the *my-shared-topic* in group ***my-group-name*** you can set the topic filter to "$share/***my-group-name***/*my-shared-topic*".

Let's assume we would like to subscribe and process the following data from Thermometer devices:

<table>
  <thead>
    <tr>
      <td style="width: 25%"><b>Example Name</b></td><td style="width: 25%"><b>Topic</b></td><td style="width: 25%"><b>Topic Filter</b></td><td style="width: 30%"><b>Payload</b></td><td style="width: 20%"><b>Comments</b></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Example 1</td>
      <td>sensor/data</td>
      <td>sensor/data</td>
      <td>{"serialNumber": "SN-001", "sensorType": "Thermometer", "sensorModel": "T1000", "temp":  42, "hum": 58}</td>
      <td>Device Name is part of the payload</td>
    </tr>
    <tr>
      <td>Example 2</td>
      <td>sensor/SN-001/data</td>
      <td>sensor/+/data</td>
      <td>{"sensorType": "Thermometer", "sensorModel": "T1000", "temp":  42, "hum": 58}</td>
      <td>Device Name is part of the topic</td>
    </tr>
  </tbody>
</table>

In this case the following messages are valid:

Example 1:

```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/data" -m '{"serialNumber": "SN-001", "sensorType": "Thermometer", "sensorModel": "T1000", "temp": 42, "hum": 58}'
```
{: .copy-code}

Example 2:

```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/SN-001/data" -m '{"sensorType": "Thermometer", "sensorModel": "T1000", "temp": 42, "hum": 58}'
```
{: .copy-code}


Now let's review how we can configure JSON converter to parse this data

#### Subsection "converter"
This subsection contains configurations for processing incoming messages. 

The types of MQTT converters are as follows:
1. json -- Default converter
2. raw -- Raw default converter
3. custom -- Custom converter (You can write it yourself, and it will be used to convert incoming data from the broker.) 

{% capture mqttconvertertypespec %}
json<small>Recommended if json will be received in response</small>%,%json%,%templates/iot-gateway/mqtt-converter-json-config.md%br%
bytes<small>Recommended if bytes will be received in response</small>%,%raw%,%templates/iot-gateway/mqtt-converter-bytes-config.md%br%
custom<small>Recommended if bytes or anything else will be received in response</small>%,%custom%,%templates/iot-gateway/mqtt-converter-custom-config.md{% endcapture %}

{% include content-toggle.liquid content-toggle-id="MqttConverterTypeConfig" toggle-spec=mqttconvertertypespec %}


**Note**: You can specify multiple mapping objects inside the array.

Also, you can combine values from MQTT message in attributes, telemetry and serverSideRpc section, for example:
{% highlight json %}
{
  {
      "topicFilter": "sensor/data",
      "converter": {
        "type": "json",
        "deviceInfo":{
            "deviceNameExpressionSource":"message",
            "deviceNameExpression":"${serialNumber}",
            "deviceProfileExpressionSource":"message",
            "deviceProfileExpression":"${sensorType}"
        },
        "timeout": 60000,
        "attributes": [],
        "timeseries": [
          {
            "type": "integer",
            "key": "temperature",
            "value": "${temp}"
          },
          {
            "type": "integer",
            "key": "humidity",
            "value": "${hum}"
          },
          {
            "type": "string",
            "key": "combine",
            "value": "${hum}:${temp}"
          }
        ]
      }
    }
}
{% endhighlight %}

Mapping process subscribes to the MQTT topics using **topicFilter** parameter of the mapping object. 
Each message that is published to this topic by other devices or applications is analyzed to extract device name, type and data (attributes and/or timeseries values).
By default, gateway uses Json converter, but it is possible to provide custom converter. See examples in the source code.

{% capture difference %}
**Connector won't pass the '**None**' value from the converter**  
{% endcapture %}
{% include templates/info-banner.md content=difference %}

**Now let’s review an example of sending data from "SN-001" thermometer device.**

Let’s assume MQTT broker is installed locally on your server.

Use terminal to simulate sending message from the device to the MQTT broker:
```bash
mosquitto_pub -h 127.0.0.1 -p 1883 -t "sensor/data" -m '{"serialNumber": "SN-001", "sensorType": "Thermometer", "sensorModel": "T1000", "temp": 42, "hum": 58}'
```
{: .copy-code}

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-message-1.png)
{: refdef}

The device will be created and displayed in ThingsBoard based on the passed parameters.
{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-created-device-1.png)
{: refdef}

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-created-device-2.png)
{: refdef}

### Section "requestsMapping"

This section of the configuration outlines an array that includes all the supported requests for both the gateway and ThingsBoard:
- connect requests;
- disconnect requests;
- attribute requests;
- attribute updates;
- RPC commands;

#### Section "connectRequests"

ThingsBoard allows sending RPC commands and notifications about device attribute updates to the device.
But in order to send them, the platform needs to know if the target device is connected and what gateway or session is used to connect the device at the moment.
If your device is constantly sending telemetry data - ThingsBoard already knows how to push notifications.
If your device just connects to MQTT broker and waits for commands/updates, you need to send a message to the Gateway and inform that device is connected to the broker.
 
**1. Name in a message from broker:**

| **Parameter**                     | **Default value**   | **Description**                                                                                        |
|:----------------------------------|:--------------------|--------------------------------------------------------------------------------------------------------|
| topicFilter                       | **sensor/connect**  | Topic address on the broker, where the broker sends information about new connected devices.           |
| deviceInfo                        |                     | JSON object that describe how to parce device name and device profile.                                 |
| ... deviceNameExpressionSource    | **message**         | Specifies the source from which the device name will be extracted ("message", "topic", "constant").    |
| ... deviceNameExpression          | **${serialNumber}** | Contains the expression used to extract the device name from the specified source.                     |
| ... deviceProfileExpressionSource | **constant**        | Specifies the source from which the device profile will be extracted ("message", "topic", "constant"). |
| ... deviceProfileExpression       | **Thermometer**     | Contains the expression used to extract the device profile from the specified source.                  |
| ---                               |                     |                                                                                                        |

**2. Name in topic address:**

| **Parameter**                     | **Default value**    | **Description**                                                                                        |
|:----------------------------------|:---------------------|--------------------------------------------------------------------------------------------------------|
| topicFilter                       | **sensor/+/connect** | Topic address on the broker, where the broker sends information about new connected devices.           |
| deviceInfo                        |                      | JSON object that describe how to parce device name and device profile.                                 |
| ... deviceNameExpressionSource    | **message**          | Specifies the source from which the device name will be extracted ("message", "topic", "constant").    |
| ... deviceNameExpression          | **${serialNumber}**  | Contains the expression used to extract the device name from the specified source.                     |
| ... deviceProfileExpressionSource | **constant**         | Specifies the source from which the device profile will be extracted ("message", "topic", "constant"). |
| ... deviceProfileExpression       | **Thermometer**      | Contains the expression used to extract the device profile from the specified source.                  |
| ---                               |                      |                                                                                                        |

This section in configuration looks like:
```json
  "connectRequests": [
      {
         "topicFilter": "sensor/connect",
         "deviceInfo": {
            "deviceNameExpressionSource": "message",
            "deviceNameExpression": "${serialNumber}",
            "deviceProfileExpressionSource": "constant",
            "deviceProfileExpression": "Thermometer"
         }
      },
      {
         "topicFilter": "sensor/+/connect",
         "deviceInfo": {
            "deviceNameExpressionSource": "topic",
            "deviceNameExpression": "(?<=sensor/)(.*?)(?=/connect)",
            "deviceProfileExpressionSource": "constant",
            "deviceProfileExpression": "Thermometer"
         }
      }
   ]
```

In this case the following messages are valid:

```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/connect" -m '{"serialNumber":"SN-001"}'
```
{: .copy-code}
```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/SN-001/connect" -m ''
```
{: .copy-code}

**Now let’s review an example.**

Use a terminal to simulate sending a message from the device to the MQTT broker:

```bash
mosquitto_pub -h 127.0.0.1 -p 1883 -t "sensor/connect" -m '{"serialNumber": "SN-001"}'
```
{: .copy-code}

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-message-connect.png)
{: refdef}

Your ThingsBoard instance will get information from the broker about last connecting time of the device. You can see this information under the "Server attributes" scope in the "Attributes" tab.

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-connect-device.png)
{: refdef}

#### Section "disconnectRequest"

This configuration section is optional.  
Configuration, provided in this section will be used to get information from the broker about disconnecting device.  
If your device just disconnects from MQTT broker and waits for commands/updates, you need to send a message to the Gateway and inform it that device is disconnected from the broker.
 
**1. Name in a message from broker:**

| **Parameter**                  | **Default value**     | **Description**                                                                                     |
|:-------------------------------|:----------------------|-----------------------------------------------------------------------------------------------------|
| topicFilter                    | **sensor/disconnect** | Topic address on the broker, where the broker sends information about disconnected devices.         |
| deviceInfo                     |                       | JSON object that describe how to parce device name and device profile.                              |
| ... deviceNameExpressionSource | **message**           | Specifies the source from which the device name will be extracted ("message", "topic", "constant"). |
| ... deviceNameExpression       | **${serialNumber}**   | Contains the expression used to extract the device name from the specified source.                  |
| ---                            |                       |                                                                                                     |

**2. Name in topic address:**

| **Parameter**                  | **Default value**                    | **Description**                                                                                     |
|:-------------------------------|:-------------------------------------|-----------------------------------------------------------------------------------------------------|
| topicFilter                    | **sensor/+/disconnect**              | Topic address on the broker, where the broker sends information about disconnected devices.         |
| deviceInfo                     |                                      | JSON object that describe how to parce device name and device profile.                              |
| ... deviceNameExpressionSource | **topic**                            | Specifies the source from which the device name will be extracted ("message", "topic", "constant"). |
| ... deviceNameExpression       | **(?<=sensor\/)(.\*?)(?=\/connect)** | Contains the expression used to extract the device name from the specified source.                  |
| ---                            |                                      |                                                                                                     |

This section in configuration file looks like:  

```json
   "disconnectRequests": [
      {
         "topicFilter": "sensor/disconnect",
         "deviceInfo": {
            "deviceNameExpressionSource":"message",
            "deviceNameExpression":"${serialNumber}"
         }
      },
      {
         "topicFilter": "sensor/+/disconnect",
         "deviceInfo": {
            "deviceNameExpressionSource": "topic",
            "deviceNameExpression": "(?<=sensor/)(.*?)(?=/connect)"
         }
      }
   ]
```

In this case the following messages are valid:

```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/disconnect" -m '{"serialNumber":"SN-001"}'
```
{: .copy-code}
```bash
mosquitto_pub -h YOUR_MQTT_BROKER_HOST -p YOUR_MQTT_BROKER_PORT -t "sensor/SN-001/disconnect" -m ''
```
{: .copy-code}

**Now let’s review an example.** 

Use a terminal to simulate sending a message from the device to MQTT broker:

```bash
mosquitto_pub -h 127.0.0.1 -p 1883 -t "sensor/disconnect" -m '{"serialNumber": "SN-001"}'
```
{: .copy-code}

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-message-disconnect.png)
{: refdef}

Your ThingsBoard instance will get information from the broker about last disconnecting time of the device. You can see this information under the "Server attributes" scope in the "Attributes" tab.

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-disconnect-device.png)
{: refdef}

#### Section "attributeRequests"

This configuration section is optional.

In order to request client-side or shared device attributes to ThingsBoard server node, Gateway allows sending 
attribute requests.

| **Parameter**                  | **Default value**                      | **Description**                                                                                     |
|:-------------------------------|:---------------------------------------|-----------------------------------------------------------------------------------------------------|
| retain                         | **false**                              | If set to true, the message will be set as the "last known good"/retained message for the topic.    |
| topicFilter                    | **v1/devices/me/attributes/request**   | Topic for attribute request.                                                                        |
| deviceInfo                     |                                        | JSON object that describe how to parce device name and device profile.                              |
| ... deviceNameExpressionSource | **message**                            | Specifies the source from which the device name will be extracted ("message", "topic", "constant"). |
| ... deviceNameExpression       | **${serialNumber}**                    | Contains the expression used to extract the device name from the specified source.                  |
| attributeNameJsonExpression    | **${versionAttribute}**                | JSON-path expression, for looking the attribute name in topicFilter message.                        |
| topicExpression                | **devices/${deviceName}/attrs**        | JSON-path expression, for formatting reply topic.                                                   |
| valueExpression                | **${attributeKey}: ${attributeValue}** | Message that will be sent to topic from topicExpression.                                            |
| ---                            |                                        |                                                                                                     |

This section in configuration file looks like:
```json
   "attributeRequests": [
      {
         "retain": false,
         "topicFilter": "v1/devices/me/attributes/request",
         "deviceInfo": {
            "deviceNameExpressionSource": "message",
            "deviceNameExpression": "${serialNumber}"
         },
         "attributeNameExpressionSource": "message",
         "attributeNameExpression": "${versionAttribute}",
         "topicExpression": "devices/${deviceName}/attrs",
         "valueExpression": "${attributeKey}: ${attributeValue}"
      }
   ]
```

Also, you can request multiple attributes at once. Simply add one more JSON-path to 
attributeNameExpression parameter. For example, we want to request two shared attributes in one request, our config 
will look like:
```json
   "attributeRequests": [
     {
       "retain": false,
       "topicFilter": "v1/devices/me/attributes/request",
       "deviceInfo": {
          "deviceNameExpressionSource": "message",
          "deviceNameExpression": "${serialNumber}"
       },
       "attributeNameJsonExpression": "${versionAttribute}, ${pduAttribute}",
       "topicExpression": "devices/${deviceName}/attrs",
       "valueExpression": "${attributeKey}: ${attributeValue}"
     }
   ]
```

#### Section "attributeUpdates"

This configuration section is optional.  
ThingsBoard allows to provision device attributes and fetch some of them from the device application.
You can treat this as a remote configuration for devices. Your devices are able to request shared attributes from ThingsBoard.
See [user guide](/docs/user-guide/attributes/) for more details.

The "**attributeRequests**" configuration allows configuring the format of the corresponding attribute request and response messages. 

| **Parameter**    | **Default value**                                   | **Description**                                                                                  |
|:-----------------|:----------------------------------------------------|--------------------------------------------------------------------------------------------------|
| retain           | **false**                                           | If set to true, the message will be set as the "last known good"/retained message for the topic. |
| deviceNameFilter | **.\***                                             | Regular expression device name filter, used to determine, which function to execute.             |
| attributeFilter  | **uploadFrequency**                                 | Regular expression attribute name filter, used to determine, which function to execute.          |
| topicExpression  | **sensor/${deviceName}/${attributeKey}**            | JSON-path expression used for creating topic address to send a message.                          |
| valueExpression  | **{\\"${attributeKey}\\":\\"${attributeValue}\\"}** | JSON-path expression used for creating the message data that will send to topic.                 |
| ---              |                                                     |                                                                                                  |

This section in configuration file looks like:  

```json
  "attributeUpdates": [
    {
      "retain": false,
      "deviceNameFilter": ".*",
      "attributeFilter": "uploadFrequency",
      "topicExpression": "sensor/${deviceName}/${attributeKey}",
      "valueExpression": "{\"${attributeKey}\":\"${attributeValue}\"}"
    }
  ]
```

**Let's look at an example.**

Run the command below to start the *mosquitto_sub* client, subscribing to the topic "sensor/SN-001/firmwareVersion" of the local broker. Start waiting for new messages from ThingsBoard server to broker.

```bash
mosquitto_sub -t sensor/SN-001/firmwareVersion
```
{: .copy-code}

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-mosquitto-sub-wait-1.png)
{: refdef}

Update device attribute value on the ThingsBoard server following these steps:
- Open the "Devices" page;
- Click on your device and navigate to the "Attributes" tab;
- Choose "Shared attributes" scope and click on the "pencil" icon next to *"firmwareVersion"* attribute.

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-update-attribute-1.png)
{: refdef}

- Change firmware version value from "1.1" to "1.2". Then click "Update" button.

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-update-attribute-2.png)
{: refdef}

The firmware version has been updated to "1.2".

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-update-attribute-3.png)
{: refdef}

Broker received new message from the ThingsBoard server about updating attribute "FirmwareVersion" to "1.2".

{:refdef: style="text-align: center;"}
![image](/images/gateway/mqtt-mosquitto-sub-get-1.png)
{: refdef}

#### Server side RPC commands

ThingsBoard allows sending [RPC commands](/docs/user-guide/rpc/) to the device that is connected to ThingsBoard directly or via Gateway.
 
Configuration, provided in this section is used for sending RPC requests from ThingsBoard to device.

| **Parameter**           | **Default value**                                            | **Description**                                                                                                                                |
|:------------------------|:-------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| deviceNameFilter        | **.\***                                                      | Regular expression device name filter, is used to determine, which function to execute.                                                        |
| methodFilter            | **echo**                                                     | Regular expression method name filter, is used to determine, which function to execute.                                                        |
| requestTopicExpression  | **sensor/${deviceName}/request/${methodName}/${requestId}**  | JSON-path expression, is used for creating topic address to send RPC request.                                                                  |
| responseTopicExpression | **sensor/${deviceName}/response/${methodName}/${requestId}** | JSON-path expression, is used for creating topic address to subscribe for response message.                                                    |
| responseTimeout         | **10000**                                                    | Value in milliseconds. If there is no response within this period after sending the request, gateway will unsubscribe from the response topic. |
| valueExpression         | **${params}**                                                | JSON-path expression, is used for creating data for sending to broker.                                                                         |
| ---                     |                                                              |                                                                                                                                                |

{% capture methodFilterOptions %}
There are 2 options for RPC request:  
1. **With a response** -- If the configuration includes a responseTopicExpression, the gateway will attempt to subscribe to it and wait for a response.
2. **Without a response** -- If the configuration does not include a responseTopicExpression, the gateway will simply send the message without waiting for a response.
{% endcapture %}
{% include templates/info-banner.md content=methodFilterOptions %}

This section in configuration file looks like:  

```json
  "serverSideRpc": [
    {
      "deviceNameFilter": ".*",
      "methodFilter": "echo",
      "requestTopicExpression": "sensor/${deviceName}/request/${methodName}/${requestId}",
      "responseTopicExpression": "sensor/${deviceName}/response/${methodName}/${requestId}",
      "responseTimeout": 10000,
      "valueExpression": "${params}"
    },
    {
      "deviceNameFilter": ".*",
      "methodFilter": "no-reply",
      "requestTopicExpression": "sensor/${deviceName}/request/${methodName}/${requestId}",
      "valueExpression": "${params.hum}::${params.temp}"
    }
  ]
```

You can use **deviceNameFilter** and **methodFilter** to apply different mapping rules for various devices/methods.
Once Gateway receives RPC request from the server to the device, it will publish the corresponding message based on **requestTopicExpression** and **valueExpression**.
In case you expect a reply to the request from the device, you should also specify **responseTopicExpression** and **responseTimeout**. 
The Gateway will subscribe to the "response" topic and wait for a device reply until "responseTimeout" is reached (in milliseconds).

Here is an example of an RPC request (rpc-request.json) that needs to be sent from the server:

```json
{
  "method": "echo",
  "params": {
    "message": "Hello!"
  }
}
```


Also, every telemetry and attribute parameter has built-in GET and SET RPC methods out of the box, so you don’t need to configure
it manually. To use them, make sure you set all the required parameters (in the case of MQTT Connector, these are the following:
**requestTopicExpression**, **responseTopicExpression**, **responseTimeout**, **valueExpression**). 
See [the guide](/docs/iot-gateway/guides/how-to-use-get-set-rpc-methods).


## Next steps

Explore guides related to main ThingsBoard features:

 - [Data Visualization](/docs/user-guide/visualization/) - how to visualize collected data.
 - [Device attributes](/docs/user-guide/attributes/) - how to use device attributes.
 - [Telemetry data collection](/docs/user-guide/telemetry/) - how to collect telemetry data.
 - [Using RPC capabilities](/docs/user-guide/rpc/) - how to send commands to/from devices.
 - [Rule Engine](/docs/user-guide/rule-engine/) - how to use rule engine to analyze data from devices.