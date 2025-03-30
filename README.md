# IoT Device Integration with Fides Innova
This guide explains the process of integrating your IoT devices with the **Fides Innova** platform, allowing you to send, monitor, and manage your device data efficiently.

- An IoT device capable of HTTP/API and MQTT communication.

- Contact the IoT node (e.g. https://panel.zksensor.tech) administrator and request adding your device type to the devices.json file on the IoT server. Please refer to the iot-server GitHub respository to learn how to add a new device type to the IoT server (https://github.com/TheArchitect2000/iot-server?tab=readme-ov-file#b4-device-configuration-file).

- Sign up on the IoT server to create a user new account.

-  Register your device on the IoT server using the following API. You first need to obtain an access token, and then, do the registeration.
```
API URL: https://panel.zksensor.tech/app/v1/user/credential
POST body format: {"email": "string", "password": "string"}
API respnse: obtain the accessToekn from the received json; ["data"]["tokens"]["accessToken"]

API URL: https://panel.zksensor.tech/app/v1/device/insert
POST body format: {
  "deviceName": "string",
  "deviceType": "string",
  "mac": "string",
  "hardwareVersion": int32_t,
  "firmwareVersion": int32_t,
  "parameters": [
    "string"
  ],
  "isShared": bool,
  "location": "string",
  "geometry": "string"
}
API respnse: 
```

- Sample of POST body JSON payload for 'insert' API call:
```
{
    "deviceName": "Device Name",         // String
    "deviceType": "Device Type",         // String
    "mac": "Device base64 MAC address",  // String (base64 encoded)
    "hardwareVersion": 1,                // int32_t (example value)
    "firmwareVersion": 1,                // int32_t (example value)
    "parameters": [
        {"title": "Temperature", "UI": "text", "unit": "C"},
        {"title": "Humidity",    "UI": "text", "unit": "%"},
        {"title": "Time",        "UI": "text", "unit": 0}
    ],
    "isShared": false                    // boolean
}
```

- See `install_device.cpp` in this repository as an example of the implementation of the API POST request using C++ for Siemens IOT2050 device.

- For the node's info use the following:
`nodes.json`
```
[{"Name":"zkSensor","MQTT":"panel.zksensor.tech","API":"panel.zksensor.tech/app","Default": "true"},
{"Name":"EnergyWiseNetwork","MQTT":"panel.energywisenetwork.com","API":"panel.energywisenetwork.com/app"},
{"Name":"Developer","MQTT":"developer.fidesinnova.io","API":"developer.fidesinnova.io/app"},
{"Name":"TrustLearn","MQTT":"panel.trustlearn.xyz","API":"panel.trustlearn.xyz/app"},
{"Name":"MotionCertified","MQTT":"panel.motioncertified.online","API":"panel.motioncertified.online/app"}]
```

### 4. Connect to Fides Innova MQTT and Send Data
Once registered, your device can publish data via MQTT. Use the following connection details:
- **Broker:** `panel.NODE_URL` (Make sure to replace the `NODE_URL`)
- **Port:** `8883` for TLS
- **Topic Format:** The device MAC address in base64

### 5. Monitor Your Data
Access your device data via:
- **Web App:** Fides Innova Dashboard (e.g. https://panel.zksensor.tech)
- **Mobile App:** Available on [Google Play](https://play.google.com/store/apps/details?id=io.fidesinnova.front) / [App Store](https://apps.apple.com/ca/app/fidesinnova/id6477492757)

## Troubleshooting
- Ensure your device has internet access.

- Verify MQTT credentials and topic structure.

- Check API responses for errors.


## Support
For further assistance, contact:
📧 Email: info@fidesinnova.io
