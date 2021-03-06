# Introduction
The program uses the [MQTT client library](https://github.com/knolleary/pubsubclient) from Nick O'Leary (knolleary).
You have to adapt the server (broker) name and the port if needed in the rows:

```
#define MQTT_SERVER "ds218p"
#define MQTT_PORT 1883
```

# MQTT topic
The topic consists for reads of the root name (MQTT_PREFIX) and function (e.g. MHI-AC-Ctrl/Power)

For writes it consists of the root name (MQTT_PREFIX), the prefix for set commands (MQTT_SET_PREFIX) and function (e.g. MHI-AC-Ctrl/set/Power)

You can change MQTT_PREFIX and MQTT_SET_PREFIX in the program, per default it is

```
#define MQTT_PREFIX "MHI-AC-Ctrl/"
#define MQTT_SET_PREFIX "set/"
```

With the default prefixes, the following data topics/messages are available (prefix is not listed)
They are only published when there is a change of the message.

topic|r/w|value|comment
---|---|---|---
Power|r/w|"On", "Off"|
Mode|r/w|"Auto", "Dry", "Cool", "Fan" or "Heat"
Tsetpoint|r/w|18 ... 30|Target room temperature (integer) in °C
Fan|r/w|1 ... 4|Fan level
Vanes|r/w|1,2,3,4,"Swing"|Vanes up/down position
Troom|r|0 ... 35|Room temperature (float) in °C.
Toutdoor|r|-23.5 ... 40.25|Outdoor temperature (float) in °C
Errorcode|r|0 .. 255|error code (unsigned int)

Additionally, the following control topics/messages are available:

topic | r/w| value |comment
---|---|---|---
connected|r|0, 1|ESP8266 connection status to broker
synced|r|0, 1|synchronization to SPI rx frame
raw|r||raw data *1)
cmd_received|r|"o.k.", "unknown command" or "invalid parameter"|feedback for last set command

*1) The raw data are a byte array of 43 bytes. The following table shows the structure. The raw data are only published when there is change of the data:

byte |comment
---|---
[0]|frame count high byte
[1]|frame count low byte
[2:21]|rx SPI frame (MOSI) - 20 bytes
[22:41]|tx SPI frame (MISO) - 20 bytes
[42]|number of identical MOSI frames

Publishing of the raw data is per default commented out to reduce broker's work load.