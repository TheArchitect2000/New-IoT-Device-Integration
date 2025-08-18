# Bluetooth and Wi-Fi Communication Protocol for Devices

This document explains how to communicate with the Arduino device over **Wi-Fi**/**Bluetooth** using the same protocol.  
The commands and responses are sent as **plain text strings**, and each message follows the same format.

---

## 📋 Quick Cheat Sheet

| Send (Command)           | Purpose                                   | Example Response |
|--------------------------|-------------------------------------------|------------------|
| `Hi`                     | Handshake echo                            | `Hi` |
| `bh:<host>`              | Set MQTT broker host                      | `start\rhost\rok\rend` |
| `bp:<port>`              | Set MQTT broker port                      | `start\rport\rok\rend` |
| `interval:<seconds>`     | Set sending interval (seconds)            | `start\rinterval\rok\rend` |
| `MAC`                    | Get device MAC address                    | `start\rmac\r<MAC_Address>\rend` |
| `INF`                    | Get device info (JSON payload)            | `start\rINF\r{...JSON...}\rend` |
| `scanSSID`               | Scan Wi‑Fi; returns **SSID + RSSI** pairs | `start\rssids\rSSID1\t-65\vSSID2\t-70\v\rend` |
| `deviceType`             | Get device type                           | `start\rtype\r<DeviceType>\rend` |
| `SSID:<ssid>`            | Set target Wi‑Fi SSID                     | `start\rssid\rok\rend` |
| `PASS:<password>`        | Set Wi‑Fi password & trigger connect      | **Later emits:** `start\rpass\rtrue\rend` *or* `start\rpass\rfalse\rend` |
| `reset` / `restart`      | Persist creds & reboot device             | *(device restarts; no payload)* |
| *(unknown command)*      | Error                                     | `start\rerror\rnot-defined\rend` |

> Notes:
> * `PASS:<password>` does **not** echo the password. After the device attempts to connect, it sends a **single boolean result** using the `pass` key.
> * `scanSSID` returns multiple networks as `SSID\tRSSI\v...`. RSSI is in **dBm** (negative numbers; closer to 0 is stronger).

---

## 🔠 Message Structure

All structured responses follow:
```
start\r<key>\r<value>\rend\n
```
- `start` / `end` mark message boundaries.
- `\r` (carriage return) separates fields.
- Some responses include a trailing `\n` newline (as in your code). Keep parsers tolerant to the newline.

---

## 🧭 Commands & Responses

### 1) Handshake
**Send**
```
Hi
```
**Receive**
```
Hi
```

---

### 2) Broker Configuration
**Set Host**
```
bh:broker.example.com
```
**Receive**
```
start\rhost\rok\rend
```

**Set Port**
```
bp:1883
```
**Receive**
```
start\rport\rok\rend
```

**Set Interval (seconds)**
```
interval:60
```
**Receive**
```
start\rinterval\rok\rend
```

---

### 3) Device Info
**Send**
```
INF
```
**Receive** *(JSON is the payload; example below)*
```
start\rINF\r{...JSON...}\rend
```

**Sample JSON payload (reflects your code):**
```json
{
  "Type": "E-CARD",
  "HV": "1.0",
  "FV": "2.3",
  "parameters": [
    { "title": "Temperature", "ui": "text", "unit": "°C" },
    { "title": "Humidity",    "ui": "text", "unit": "%"  },
    { "title": "Button",       "ui": "text", "unit": null  }
  ]
}
```

**Same, wrapped by the protocol:**
```
start\rINF\r{"Type":"E-CARD","HV":"1.0","FV":"2.3","parameters":[{"title":"Temperature","ui":"text","unit":"°C"},{"title":"Humidity","ui":"text","unit":"%"},{"title":"Button","ui":"text","unit":null}]}\rend
```

---

### 4) Network Information
**Get MAC Address**
```
MAC
```
**Receive**
```
start\rmac\r<MAC_Address>\rend
```

**Get Device Type**
```
deviceType
```
**Receive**
```
start\rtype\r<DeviceType>\rend
```

**Scan Wi‑Fi (SSID + RSSI pairs)**
```
scanSSID
```
**Receive (example)**
```
start\rssids\rHomeWiFi\t-42\vCoffeeShop\t-67\vOfficeWiFi\t-70\v\rend
```
Parsing guidance:
- Entries are separated with **vertical tab** `\v`.
- Each entry is `SSID`, a **tab** `\t`, then an **RSSI** integer (dBm).

---

### 5) Wi‑Fi Credentials & Connection
**Set SSID**
```
SSID:MyWiFiNetwork
```
**Receive**
```
start\rssid\rok\rend
```

**Set Password & Trigger Connect**
```
PASS:MySecretPassword
```
**Later Receive (result of connection attempt)**
```
start\rpass\rtrue\rend
```
*or*
```
start\rpass\rfalse\rend
```
> The device attempts to connect after receiving `PASS:`. Once the attempt resolves, it sends one of the above results.

---

### 6) Reset / Restart
**Persist SSID, PASSWORD, interval to storage and reboot**
```
reset
```
*or*
```
restart
```
(also accepts different letter cases, e.g., `RESET`, `Restart`)

---

### 7) Error Handling
If a command is not recognized:
```
start\rerror\rnot-defined\rend
```

---

## 🧪 Example Session
```
Hi                      -> Hi
scanSSID                -> start\rssids\rHomeWiFi\t-42\vCoffeeShop\t-67\v\rend
SSID:HomeWiFi           -> start\rssid\rok\rend
PASS:SuperSecret123     -> (device attempts connect)
                         -> start\rpass\rtrue\rend   # or false
INF                     -> start\rINF\r{"Type":"E-CARD",...}\rend
```

---

## ✅ Implementation Notes
- All messages are **plain text**.
- Keep your client lenient about the final `\n` after `\rend` (some payloads include it).
- RSSI is negative dBm (e.g., `-42` is stronger than `-70`).
- This protocol is identical whether transported over **Bluetooth** or **Wi‑Fi**.
