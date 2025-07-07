# Device Installation for Raspberry Pi (C++)
This guide explains how to compile and run the C++ program to register a device with a Fides Innova server using a Raspberry Pi.
## Install Dependencies
Install the required library:
```
sudo apt update
sudo apt install libcurl4-openssl-dev
```
## Compile the Code
Use `g++` with C++17 support and link `libcurl`:
```
g++ -std=c++17 install_device.cpp -o install_device -lcurl
```
## Adding a Node
To add another node during the installation process, add the new node entry to the `nodes.json` file in this folder.

Here’s a sample entry for the JSON:
```
{
    "Name": "YOUR_NODE_NAME",
    "MQTT": "panel.YOUR_NODE_URL",
    "API": "panel.YOUR_NODE_URL/app"
}
```

## Run the Program
The program retrieves the MAC address, which requires root privileges:
```
sudo ./install_device
```
### Expected Workflow
1. The program lists available nodes from `nodes.json`.

2. Select a node and provide login credentials (email & password).

3. If successful, it registers the device and saves the broker URL in `broker_host.txt`.
