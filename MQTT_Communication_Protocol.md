# MQTT Message Format Guide

This document defines the JSON structure and topic naming used for sending messages over **MQTTS**.

---

## 🔐 Transport

- Devices should **use MQTTS (MQTT over TLS)** whenever possible.
- Some constrained devices (with limited CPU, RAM, or storage) may be unable to perform certificate verification.
- In such cases, the device is allowed to **fall back to an unsecured MQTTS connection**.

---

## 📡 MQTT Topic

- Each device publishes to a topic named after its **Base64-encoded MAC address**.  
- Example:  
  - MAC: `84:CC:A8:12:34:56`  
  - Base64: `"ODQ6Q0M6QTg6MTI6MzQ6NTY="`  
  - MQTT Topic:  
    ```
    ODQ6Q0M6QTg6MTI6MzQ6NTY=
    ```

---

## 📦 Message Payload (JSON)

Each published MQTT message must follow this format:

```json
{
  "from": "Base64(MAC_Address)",
  "to": "bh",
  "wifiName": "SSID",
  "deviceIP": "192.168.x.x",
  "data": {
    "FV": "firmware_version",
    "HV": "hardware_version",
    "Temperature": 25.3,
    "Humidity": 60.5,
    "Button": 1
  }
}
```
---

## 📝 Field Descriptions
### Top-level fields

- **from:** 
Base64-encoded MAC address of the device.
Example: MAC 84:CC:A8:12:34:56 → Base64 "hMyoEjQW"

- **to:** 
Destination identifier (e.g., "bh" for the base hub).

- **wifiName:** 
The SSID of the connected Wi-Fi network.

- **deviceIP:** 
The current IP address of the device in string format.

- **Data object:** 

  - **FV (Firmware Version):** 
The version of the device firmware.

  - **HV (Hardware Version):** 
The version or revision of the device hardware.

  - **Temperature:** 
Predicted temperature value (float, rounded).

  - **Humidity:** 
Predicted humidity value (float, rounded).

  - **Button:** 
  Status of the button:

    - 0 → Released

    - 1 → Pressed

---
## ✅ Example of Published Message

- **MQTT Topic:**
```
ODQ6Q0M6QTg6MTI6MzQ6NTY=
```

- **MQTT Payload:**
```json
{
  "from": "ODQ6Q0M6QTg6MTI6MzQ6NTY=",
  "to": "panel.example.com",
  "wifiName": "HomeNetwork",
  "deviceIP": "192.168.1.45",
  "data": {
    "FV": "1.0.2",
    "HV": "4.5",
    "Temperature": 24.7,
    "Humidity": 58.0,
    "Button": 0
  }
}
```
