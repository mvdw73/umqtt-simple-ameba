# umqtt-simple-ameba

umqtt-simple ported to the Realtek Ameba platform

This is a port of the micropython MQTT Simple implementation for the Realtek Ameba Platform

Originally this came from https://raw.githubusercontent.com/RuiSantosdotme/ESP-MicroPython/master/code/MQTT/umqttsimple.py, but I'm not sure if this is the original source.

## Rationale

This exists becasue the Ameba socket API follows the CPython API more closely than the ESP micropython socket API. Specifically, the ameba API has `send` and `recv` rather than `write` and `read` in the more widely-used ESP micropython socket.

Also, the mechanism for connecting is different in the Ameba device. There is no `getaddrinfo` function, but the `connect` function takes two parameters being a server address and port. The server address can be a FQDN or an IP address. 

## But why didn't we use a more recent base

In short, because Ameba. The Ameba socket port is fairly incomplete, and doesn't have setblocking functionality.

Also, the ameba port is a fair bit behind the upstream micropython, so doesn't at the time of writing have asyncio, so we can't use Peter Hinch's excellent mqtt_as implementation.

## Known Issues

Since there's no `setblocking` method in the ameba socket class, the `wait_msg` function doesn't block. This means in the real application you'll have to poll the subscribe method continually to check for messages. Yes, I know it sucks. The long term solution will be for Realtek to have their micropython port sync'ed with upstream and use asyncio.

## Usage

Connect to your wifi, and ensure the broker is accessible.

```python

# Connect to Network
from wireless import WLAN

ssid = "ssid"
pwd = "password"

wifi = WLAN(mode=WLAN.STA)
wifi.connect(ssid=ssid, pswd = pwd)

# MQTT Stuff
import simple

# Connect:
m = simple.MQTTClient("name", "brokername")
m.connect()

# Publish a message:
m.publish(b"test", b"message")

# Subscribe:
m.set_callback(print)
m.subscribe(b"test")
# Returns immediately; prints the message if on the broker

```