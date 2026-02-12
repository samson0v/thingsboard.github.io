* TOC
{:toc}

### Overview

The [MicroPython Client SDK](https://github.com/thingsboard/thingsboard-micropython-client-sdk) is a software
development kit for client-side integration of your MicroPython projects. It allows you to connect your MicroPython 
devices to ThingsBoard using MQTT protocol and send telemetry data, attributes, and receive RPC calls. The SDK 
provides a simple and easy-to-use API for connecting to ThingsBoard and sending data, making it easier for developers 
to integrate their MicroPython devices with the platform.

MicroPython Client SDK supports the following features:

- Connecting to ThingsBoard using MQTT protocol.
- Sending attributes to ThingsBoard.
- Sending telemetry data to ThingsBoard.
- Receiving RPC calls from ThingsBoard.
- Request client and shared attributes from ThingsBoard.
- Subscribing to attribute updates from ThingsBoard.
- Device claiming.
- Device provisioning.

### Installation

To install the MicroPython Client SDK, you can use the
[mip](https://docs.micropython.org/en/latest/reference/packages.html) package manager. Run the following command in
the REPL or in your code:

```python
import mip

mip.install('github:thingsboard/thingsboard-micropython-client-sdk')
```

It is recommended to use the following code snippet to make sure that the SDK is installed and imported correctly,
and also to not install the SDK every time you run your code:

```python
try:
    from thingsboard_sdk.tb_device_mqtt import TBDeviceMqttClient

    print("thingsboard-micropython-client-sdk package already installed.")
except ImportError:
    print("Installing thingsboard-micropython-client-sdk package...")
    mip.install('github:thingsboard/thingsboard-micropython-client-sdk')
    from thingsboard_sdk.tb_device_mqtt import TBDeviceMqttClient
```

### Methods

#### Introduction

The MicroPython Client SDK has a `TBDeviceMqttClient` class that provides methods for connecting to ThingsBoard and
sending data.

This class is designed to be simple to use and easy to understand for developers who are new to MicroPython or
ThingsBoard.

#### connect

Connects to ThingsBoard using MQTT protocol. This method should be called before sending any data to ThingsBoard.
Credentials for connecting to ThingsBoard should be provided when creating an instance of the `TBDeviceMqttClient` 
class. When you call the `connect` method, `self.connected` property of the client will be set to `True`.

**Method Syntax**

`client.connect(timeout=10)`

**Arguments**

| **Arguments** | **Default value** | **Description**                                           |
|:--------------|-------------------|:----------------------------------------------------------|
| timeout       | **10**            | (Optional) Time to establish a connection to ThingsBoard. |
| ---           |                   |                                                           |

**Example usage**

```python
# Default connecting
client.connect()

# Connecting with custom timeout
client.connect(timeout=20)

# Connecting with waiting for connection result
result = client.connect(timeout=20)
```

#### disconnect

Disconnects from ThingsBoard. This method can be called after connecting to ThingsBoard. It is recommended to call 
this method when you no longer need to send data to ThingsBoard or when you want to free up resources. After calling 
this method, you will need to call the [connect](/docs/reference/micropython-client-sdk/#connect) method again to send 
data to ThingsBoard. When you call the `disconnect` method, `self.connected` property of the client will be set 
to `False`.

**Method Syntax**

`client.disconnect()`

**Example usage**

```python
client.connect()

# some tasks with ThingsBoard

client.disconnect()
```

#### send_attributes

Sends attributes to ThingsBoard. This method can be called after connecting to ThingsBoard. Method supports sending
attributes in key-value pairs.

**Method Syntax**

`client.send_attributes(data)`

| **Arguments** | **Description**                                                 |
|:--------------|:----------------------------------------------------------------|
| data          | (Required) Data that will be sent as attributes to ThingsBoard. |
| ---           |                                                                 |

**Example usage**

```python
attributes = {"sensorModel": "DHT-22", "attribute_2": "value"}
client.send_attributes(attributes)
```

#### send_telemetry

Sends telemetry data to ThingsBoard. This method can be called after connecting to ThingsBoard. Method supports 
sending telemetry data in different formats, key-value pairs, and lists. Also, it supports sending telemetry data 
grouped by timestamp, which is useful for sending historical data to ThingsBoard.

**Method Syntax**

`client.send_telemetry(data)`

**Arguments**

| **Arguments** | **Description**                                                  |
|:--------------|:-----------------------------------------------------------------|
| data          | (Required) Data that will be sent as a telemetry to ThingsBoard. |
| ---           |                                                                  |

**Example usage**

```python
# Sending telemetry data in regular dictionary format
telemetry = {"temperature": 25.5, "humidity": 60}
client.send_telemetry(telemetry)

# Sending telemetry data grouped by timestamp
from time import time
telemetry = [{"ts": 1451649600000, "values": {"temperature": 42.2, "humidity": 71}},
             {"ts": 1451649601000, "values": {"temperature": 42.3, "humidity": 72}}]
client.send_telemetry(telemetry)
```

#### request_attributes

Requests client and shared attributes from ThingsBoard. This method can be called after connecting to ThingsBoard.
Method supports requesting both client and shared attributes. You can specify which attributes you want to request by 
providing a list of attribute keys. If requested attributes are received from ThingsBoard, the provided callback 
function will be called with the result. If requested attributes are not found on ThingsBoard, the callback function 
will be called with empty result.

**Method Syntax**

`client.request_attributes(client_keys=None, shared_keys=None, callback=None)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                                                                                                                       |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| client_keys   | (Optional) List of client attribute keys to request from ThingsBoard.                                                                                                                                                                                                 |
| shared_keys   | (Optional) List of shared attribute keys to request from ThingsBoard.                                                                                                                                                                                                 |
| callback      | (Optional) Callback function that will be called when the requested attributes are received from ThingsBoard. The callback function should accept two arguments: `result` and `exception`, which will contain the requested attributes.                               |
| ---           |                                                                                                                                                                                                                                                                       |

**Example usage**

```python
def on_attributes_change(result, exception=None):
    # This is a callback function that will be called when client receive the response from the server
    if exception is not None:
        print("Exception: " + str(exception))
    else:
        print(result)


client.request_attributes(client_keys=["atr1", "atr2"], callback=on_attributes_change)
```

#### claim_device

Claims a device on ThingsBoard. This method can be called after connecting to ThingsBoard. Method supports claiming a 
device using a claim code. After claiming a device, it will be associated with the account that owns the claim code. 
This method is useful when you want to allow users to claim their devices on ThingsBoard without providing them with 
access to the ThingsBoard platform.

**Method Syntax**

`client.claim_device(secret_key, duration_ms=None)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                            |
|:--------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| secret_key    | (Required) Claim code that will be used to claim the device on ThingsBoard.                                                                                                |
| duration_ms   | (Optional) Duration in milliseconds for which the claim code will be valid. If not provided, the claim code will be valid indefinitely until it is used to claim a device. |
| ---           |                                                                                                                                                                            |

**Example usage**

```python
# Claiming a device with a claim code that will be valid indefinitely until it is used to claim a device
client.claim_device("my_claim_code")

# Claiming a device with a claim code that will be valid for 60 seconds
client.claim_device("my_claim_code", duration_ms=60000)
```

#### subscribe_to_attribute

Subscribes to attribute update from ThingsBoard. This method can be called after connecting to ThingsBoard. Method 
supports subscribing to shared attribute update. You can specify which shared attribute you want to subscribe to by 
providing an attribute key. If subscribed attribute is updated on ThingsBoard, the provided callback function will be 
called with the result.

Method will return a subscription ID that can be used to unsubscribe from attribute updates using 
the [unsubscribe_from_attribute](/docs/reference/micropython-client-sdk/#unsubscribe_from_attribute) method.

**Method Syntax**

`client.subscribe_to_attribute(key, callback)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                                                                                    |
|:--------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| key           | (Required) Shared attribute key to subscribe to for updates from ThingsBoard.                                                                                                                                                      |
| callback      | (Required) Callback function that will be called when the subscribed attribute is updated on ThingsBoard. The callback function should accept two arguments: `result` and `*args`, which will contain the updated attribute value. |
| ---           |                                                                                                                                                                                                                                    |

**Example usage**

```python
def callback(result, *args):
    print("Received data: %r", result)


sub_id = client.subscribe_to_attribute("frequency", callback)
```

#### subscribe_to_all_attributes

Subscribes to all attribute updates from ThingsBoard. This method can be called after connecting to ThingsBoard. 
Method supports subscribing to all shared attribute updates. If any shared attribute is updated on ThingsBoard, 
the provided callback function will be called with the result.

Method will return a subscription ID that can be used to unsubscribe from attribute updates using 
the [unsubscribe_from_attribute](/docs/reference/micropython-client-sdk/#unsubscribe_from_attribute) method.

**Method Syntax**

`client.subscribe_to_all_attributes(callback)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                                                                                        |
|:--------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| callback      | (Required) Callback function that will be called when any shared attribute is updated on ThingsBoard. The callback function should accept two arguments: `result` and `*args`, which will contain the updated attribute key and value. |
| ---           |                                                                                                                                                                                                                                        |

**Example usage**

```python
def callback(result, *args):
    print("Received data: %r", result)


sub_id = client.subscribe_to_all_attributes(callback)
```

#### unsubscribe_from_attribute

Unsubscribes from attribute updates from ThingsBoard. This method can be called after connecting to ThingsBoard. Method
supports unsubscribing from shared attribute updates using the subscription ID that was returned when subscribing to
attribute updates using the [subscribe_to_attribute](/docs/reference/micropython-client-sdk/#subscribe_to_attribute) 
or [subscribe_to_all_attributes](/docs/reference/micropython-client-sdk/#subscribe_to_all_attributes) methods.

**Method Syntax**

`client.unsubscribe_from_attribute(subscription_id)`

**Arguments**

| **Arguments**   | **Description**                                                                                                                                                                                                                                                                                      |
|:----------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| subscription_id | (Required) Subscription ID that was returned when subscribing to attribute updates using the [subscribe_to_attribute](/docs/reference/micropython-client-sdk/#subscribe_to_attribute) or [subscribe_to_all_attributes](/docs/reference/micropython-client-sdk/#subscribe_to_all_attributes) methods. |
| ---             |                                                                                                                                                                                                                                                                                                      |

**Example usage**

```python
# Subscribing to attribute updates
def callback(result, *args):
    print("Received data: %r", result)

sub_id = client.subscribe_to_attribute("frequency", callback)
# Unsubscribing from attribute updates
client.unsubscribe_from_attribute(sub_id)
```

#### set_server_side_rpc_request_handler

Sets a handler for server-side RPC requests from ThingsBoard. This method can be called after connecting to 
ThingsBoard. If a server-side RPC request is received from ThingsBoard, the provided handler function will be called 
with the result. The handler function should accept two arguments: `request_id` and `request_body`, which will contain 
the ID of the received RPC request and the data of the received RPC request, respectively.

**Method Syntax**

`client.set_server_side_rpc_request_handler(handler)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                                                                                                                                                               |
|:--------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| handler       | (Required) Handler function that will be called when a server-side RPC request is received from ThingsBoard. The handler function should accept two arguments: `request_id` and `request_body`, which will contain the ID of the received RPC request and the data of the received RPC request, respectively. |
| ---           |                                                                                                                                                                                                                                                                                                               |

**Example usage**

```python
def handler(request_id, request_body):
    print("Received RPC request with ID: %s and body: %r", request_id, request_body)

client.set_server_side_rpc_request_handler(handler)
```

#### send_rpc_reply

Sends a reply to a server-side RPC request from ThingsBoard. This method should be called in the handler function that 
is set using the 
[set_server_side_rpc_request_handler](/docs/reference/micropython-client-sdk/#set_server_side_rpc_request_handler) 
method when a server-side RPC request is received from ThingsBoard. The `request_id` argument should be the ID of the 
received RPC request, and the `response` argument should be the data that will be sent as a reply to the received RPC 
request.

**Method Syntax**

`client.send_rpc_reply(request_id, response)`

**Arguments**

| **Arguments** | **Description**                                                                                                                                                                                                                                                                                                                                                                  |
|:--------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| request_id    | (Required) ID of the received RPC request that will be used to send a reply to the received RPC request. This ID is provided as an argument to the handler function that is set using the [set_server_side_rpc_request_handler](/docs/reference/micropython-client-sdk/#set_server_side_rpc_request_handler) method when a server-side RPC request is received from ThingsBoard. |
| response      | (Required) Data that will be sent as a reply to the received RPC request.                                                                                                                                                                                                                                                                                                        |
| ---           |                                                                                                                                                                                                                                                                                                                                                                                  |

**Example usage**

```python
def handler(request_id, request_body):
    print("Received RPC request with ID: %s and body: %r", request_id, request_body)
    client.send_rpc_reply(request_id, {"status": "success"})
   
client.set_server_side_rpc_request_handler(handler)
```

#### get_provision_request

Static method that forming provision request for device provisioning by input arguments. The returned provision 
request should be sent to ThingsBoard using the [provision](/docs/reference/micropython-client-sdk/#provision) method. 
Using in pair with [provision](/docs/reference/micropython-client-sdk/#provision) method.

**Method Syntax**

`TBDeviceMqttClient.get_provision_request(provision_key, provision_secret)`

**Arguments**

| **Arguments**    | **Description**                                                                                                                                            |
|:-----------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| provision_key    | (Required) Provision key that will be used for device provisioning on ThingsBoard.                                                                         |
| provision_secret | (Required) Provision secret that will be used for device provisioning on ThingsBoard.                                                                      |
| device_name      | (Optional) Device name that will be used for device provisioning on ThingsBoard. If not provided, the device name will be the same as the provision key.   |
| access_token     | (Optional) Access token that will be used for device provisioning on ThingsBoard.                                                                          |
| client_id        | (Optional) Client ID that will be used for device provisioning on ThingsBoard.                                                                             |
| username         | (Optional) Username that will be used for device provisioning on ThingsBoard.                                                                              |
| password         | (Optional) Password that will be used for device provisioning on ThingsBoard.                                                                              |
| hash             | (Optional) Hash that will be used for device provisioning on ThingsBoard.                                                                                  |
| gateway          | (Optional) Flag that indicates whether the provision request is for a gateway device. If not provided, the provision request will be for a regular device. |
| ---              |                                                                                                                                                            |

**Example usage**

```python
# Forming provision request with required arguments
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret")

# Forming provision request with specified device name
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret", device_name="My Device")

# Forming provision request with specified access token
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret", access_token="my_access_token")

# Forming provision request with specified client ID, username, and password
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret", client_id="my_client_id", username="my_username", password="my_password")

# Forming provision request for a gateway device
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret", gateway=True)
```

#### provision

Sends a provision request to ThingsBoard for device provisioning. The `provision_request` argument should be the 
provision request that is formed using the 
[get_provision_request](/docs/reference/micropython-client-sdk/#get_provision_request) static method. If the provision 
request is successful, the device will be provisioned on ThingsBoard and associated with the account that owns the 
provision key and provision secret. If the provision request is not successful, an exception will be raised.

**Method Syntax**

`client.provision(host, port, provision_request)`

**Arguments**

| **Arguments**     | **Description**                                                                                                                                                                |
|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| host              | (Required) Host of the ThingsBoard server to which the provision request will be sent.                                                                                         |
| port              | (Required) Port of the ThingsBoard server to which the provision request will be sent.                                                                                         |
| provision_request | (Required) Provision request that will be sent to ThingsBoard for device provisioning. The provision request should be formed using the `get_provision_request` static method. |
| ---               |                                                                                                                                                                                |

**Example usage**

```python
# Forming provision request
provision_request = TBDeviceMqttClient.get_provision_request("my_provision_key", "my_provision_secret")
# Sending provision request to ThingsBoard for device provisioning
provisioned_credentials = client.provision("thingsboard.server.com", 1883, provision_request)
```

### Concepts

#### Introduction

In this section, we will go over the core concepts of the MicroPython Client SDK, such as connecting to ThingsBoard, 
sending and receiving data. Understanding these concepts will help you to use the MicroPython Client SDK effectively 
and efficiently in your projects.

Let's review the main concepts of the MicroPython Client SDK using the following code example:

```python
import time
import network
from thingsboard_sdk.tb_device_mqtt import TBDeviceMqttClient

# Enabling WLAN interface
wlan = network.WLAN(network.STA_IF)
wlan.active(True)

# Establishing connection to the Wi-Fi
if not wlan.isconnected():
    print("Connecting to network...")
    wlan.connect("WIFI_SSID", "WIFI_PASSWORD")
    while not wlan.isconnected():
        pass

print("Connected! Network config:", wlan.ifconfig())


# This callback will be called when an RPC request is received from ThingsBoard.
def on_server_side_rpc_request(request_id, request_body):
    # request_id: numeric id from the MQTT topic
    # request_body: decoded JSON dict, typically {"method": "...", "params": ...}
    print("[RPC] id:", request_id, "body:", request_body)

    client.send_rpc_reply(request_id, "ok")


# Initialising client to communicate with ThingsBoard
client = TBDeviceMqttClient("THINGSBOARD_HOST", port=1883, access_token="ACCESS_TOKEN")
# Register the server-side RPC callback before the main loop
client.set_server_side_rpc_request_handler(on_server_side_rpc_request)
# Connect to ThingsBoard
client.connect()


def safe_check_msg():
    """
    Non-blocking MQTT poll.
    """
    try:
        # non-blocking check
        client.check_for_msg()
        return True
    except OSError as e:
        print("[MQTT] check_msg OSError:", e)
    except Exception as e:
        print("[MQTT] check_msg error:", e)
    return False


# Main loop (non-blocking)
while True:
    # Non-blocking: poll for incoming MQTT packets, then continue doing other work
    safe_check_msg()
    client.send_telemetry({"CPU", 12.0})
    time.sleep_ms(50)
```
{:.copy-code.expandable-15}

#### Connecting to ThingsBoard

To connect to ThingsBoard using the MicroPython Client SDK, you need to create an instance of the `TBDeviceMqttClient` 
class and call the `connect` method. When creating an instance of the `TBDeviceMqttClient` class, you need to provide 
credentials for connecting to ThingsBoard, such as the host, port, and access token. After calling the `connect` 
method, you will be connected and can start sending data. We recommend the following minimal code snippet to use:

```python
import network
from thingsboard_sdk.tb_device_mqtt import TBDeviceMqttClient

wlan = network.WLAN(network.STA_IF)
wlan.active(True)

# Establishing connection to the Wi-Fi
if not wlan.isconnected():
    print('Connecting to network...')
    wlan.connect("YOUR_SSID", "YOUR_PASSWORD")
    while not wlan.isconnected():
        pass

print('Connected! Network config:', wlan.ifconfig())

client = TBDeviceMqttClient(host="thingsboard.cloud", port=1883, access_token="YOUR_ACCESS_TOKEN")
client.connect()

while True:
    # some tasks with ThingsBoard
```

Before communicating with the cloud, the device must bridge the gap between the hardware and the network:

- The network module initializes the Wi-Fi chip. We use STA_IF (Station Interface) to connect the device to an existing
  access point.
- The `TBDeviceMqttClient` is the primary class. It requires ThingsBoard host and a unique Access Token generated in the
  ThingsBoard device page.

#### Handling Server-Side RPC

Remote Procedure Calls (RPC) allow ThingsBoard to send commands to your device (e.g., "Turn on the LED" or "Reset").
To handle these commands, you need to set a handler function using
the [set_server_side_rpc_request_handler](/docs/reference/micropython-client-sdk/#set_server_side_rpc_request_handler)
method. This handler will be called whenever a server-side RPC request is received from ThingsBoard. The handler
function should accept two arguments: `request_id` and `request_body`, which will contain the ID of the received RPC
request and the data of the received RPC request, respectively.

```python
# This callback will be called when an RPC request is received from ThingsBoard.
def on_server_side_rpc_request(request_id, request_body):
    # request_id: numeric id from the MQTT topic
    # request_body: decoded JSON dict, typically {"method": "...", "params": ...}
    print("[RPC] id:", request_id, "body:", request_body)

    client.send_rpc_reply(request_id, "ok")

client.set_server_side_rpc_request_handler(on_server_side_rpc_request)
```

In the SDK, we don't "wait" for a command, instead we provide a callback function (`on_server_side_rpc_request`):

- Tell the client which function to run when a message arrives using `set_server_side_rpc_request_handler()`.
- When a command arrives, the SDK automatically passes the request_id (used for the reply) and the request_body (the
  command data) to your function.
- The `send_rpc_reply` method is important. It informs the server that the command was received and processed
  successfully.

#### The Non-Blocking Loop

The most critical part of the SDK implementation is the main loop. In MicroPython, if you use `time.sleep()`, the 
device is effectively "blind" to incoming messages during that pause.

`client.check_for_msg()` method is the "heartbeat" of your communication:

- It checks the MQTT buffer for incoming RPCs or configuration updates.
- By wrapping it in a safe_check_msg() function, we ensure that if a network hiccup occurs (an OSError), the script
  doesn't crash—it, but simply logs the error and tries again in the next cycle.

#### Telemetry and Data Flow

The SDK provides methods to send telemetry data to ThingsBoard. You can use the `send_telemetry` method to send
data in various formats, including key-value pairs and lists. The SDK also supports sending telemetry data grouped by 
timestamp, which is useful for sending historical data to ThingsBoard.

```python
# Main loop (non-blocking)
while True:
    # Non-blocking: poll for incoming MQTT packets, then continue doing other work
    safe_check_msg()
    client.send_telemetry({"CPU", 12.0})
    time.sleep_ms(50)
```

By placing this inside the main loop, the device continuously streams its state. Because we use a non-blocking approach,
the device can simultaneously send telemetry and receive RPC commands without one interrupting the other.

### Examples

You can find more examples of using the MicroPython Client SDK in
the [examples](https://github.com/thingsboard/thingsboard-micropython-client-sdk/tree/main/examples) directory of the
[thingsboard-micropython-client-sdk](https://github.com/thingsboard/thingsboard-micropython-client-sdk) repository on
GitHub.

### Troubleshooting

- **Mip installation failed with OSError: -202**

    This error can occur when the device is not connected to the internet or when there are issues with the network 
    connection. To resolve this issue, make sure that your device is connected to the internet and that there are no 
    issues with the network connection. You can also try restarting your device and running the installation command again.
    
    Recommended firstly to establish a connection to the internet and then run the installation command:
    
    ```python
    import network
    import mip
    
    # Enabling WLAN interface
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    
    # Establishing connection to the Wi-Fi
    if not wlan.isconnected():
        print('Connecting to network...')
        wlan.connect("YOUR_WIFI_SSID", "YOUR_WIFI_PASSWORD")
        while not wlan.isconnected():
            pass
    
    mip.install('github:thingsboard/thingsboard-micropython-client-sdk')
    ```
