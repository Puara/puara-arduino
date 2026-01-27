# Puara Module for Arduino

**Société des Arts Technologiques (SAT)**  
**Input Devices and Music Interaction Laboratory (IDMIL), McGill University**

## Overview

Puara Module Manager facilitates embedded system development by providing a set of pre-defined modules that manage filesystem, web server, and network connections so users can focus on prototyping the rest of their system.

## How It Works

When initiating the program, the module manager will try to connect to the WiFi Network (SSID) defined in `config.json`. 

If you want the board to connect to a specific WiFi network, modify the `wifiSSID` and `wifiPSK` values in `config.json` with your network name and password respectively and then build/upload the filesystem. 

After the board connects to an external SSID, it will also create its own WiFi Access Point **(STA-AP mode)**. 

If the process cannot connect to a valid SSID, it will still create its own WiFi Access Point **(AP mode)** to which users may connect and communicate with the board.

User may modify/add custom values in `settings.json` and access them in their program at any moment by using the **puara.getVarText("name")** and/or **puara.getVarNumber("name")** for text or number fields respectively; make sure to respect the JSON *name/value* pairing. 

User may modify said values via the web server settings page and the defined values will persist even after shutting down/rebooting the system. 
This is very useful if you wish to have easily configurable variables without having to rebuild/reflash your entire system.

To access the web server, connect to the same network/SSID as the board is connected to, or connect to the board's WiFi access point, and enter the board's IP address in any web browser. 

User may also type the network name followed by `.local` in the browser's address bar. Default network name is `device`_`id` (see `config.json file`) : **Puara_001**. Hence type `puara_001.local` in the browser's address bar to access web server pages.

## Dependencies

These Arduino examples are developed for Arduino IDE 2.0. You may build and upload code using Arduino utilities however the filesystem must be uploaded using the Arduino IDE extension [Arduino-LittleFS-Upload](https://github.com/earlephilhower/arduino-littlefs-upload).

## Examples

This repository includes five Arduino example sketches that demonstrate different use cases and functionalities. 
All examples are identical to the PlatformIO templates available in [puara-module-templates](https://github.com/Puara/puara-module-templates).

Each example includes a `data/` folder containing configuration files (`config.json`, `settings.json`) and web interface files (HTML and CSS). 

**After building and uploading the firmware to your board, you must also upload the filesystem** using the [arduino-littlefs-upload](https://github.com/earlephilhower/arduino-littlefs-upload):

1. Make sure the arduino-littlefs-upload tool is [installed](https://github.com/earlephilhower/arduino-littlefs-upload?tab=readme-ov-file#installation)
2. Press **[Ctrl] + [Shift] + [P]** (Windows/Linux) or **[⌘] + [Shift] + [P]** (macOS) to open the Command Palette in the Arduino IDE
3. Select **"Upload LittleFS to Pico/ESP8266/ESP32"**


### 1. Basic Example

**File**: `examples/basic/basic.ino`

A minimal example demonstrating core Puara Module functionality. This example:
- Initializes the Puara module manager
- Reads custom settings from the JSON configuration files
- Outputs dummy sensor data to the serial monitor
- Demonstrates how to access custom configuration variables using `getVarText()` and `getVarNumber()`

This is the best starting point for learning how to use the Puara framework.

### 2. OSC-Send Example

**File**: `examples/OSC-Send/OSC-Send.ino`

Demonstrates how to set up a basic OSC transmitter. This example:
- Sends dummy sensor data as OSC messages to a specified IP address and port
- Configurable OSC IP/port via the web interface without rebuilding/reflashing
- Shows how to use the `onSettingsChanged()` callback to update parameters dynamically
- Includes example code for reading analog sensors and digital signals

**Note**: Please refer to [CNMAT's OSC repository](https://github.com/CNMAT/OSC) on GitHub for more details on OSC.

### 3. OSC-Receive Example

**File**: `examples/OSC-Receive/OSC-Receive.ino`

Demonstrates how to receive and process OSC messages. This example:
- Listens for incoming OSC messages on a configurable UDP port
- Parses OSC messages and extracts data from them
- Demonstrates example processing of float values to control device outputs (e.g., LED brightness)
- Shows how to use the `onSettingsChanged()` callback for dynamic configuration

The example expects a float between [0,1] on the OSC address `/led/brightness` with the format: `/led/brightness f 0.34`

**Note**: Please refer to [CNMAT's OSC repository](https://github.com/CNMAT/OSC) on GitHub for more details on OSC.

### 4. OSC-Duplex Example

**File**: `examples/OSC-Duplex/OSC-Duplex.ino`

Combines both OSC-Send and OSC-Receive functionality in a single sketch. This example:
- Sends dummy sensor data to a remote OSC address
- Simultaneously receives OSC messages from remote sources
- Demonstrates full duplex OSC communication patterns
- Useful for bidirectional device communication scenarios

**Note**: Please refer to [CNMAT's OSC repository](https://github.com/CNMAT/OSC) on GitHub for more details on OSC.

### 5. BLE Advertising Example

**File**: `examples/ble-advertising/ble-advertising.ino`

Demonstrates BLE (Bluetooth Low Energy) advertising without requiring device connections. This template:
- Uses BLE advertising to broadcast device information
- Encodes sensor data as CBOR payloads in BLE manufacturer data packets
- Broadcasts at configurable frequency (default 50Hz)
- Works seamlessly with the [BLE-CBOR-to-OSC script](https://gitlab.com/sat-mtl/collaborations/2024-iot/ble-cbor-to-osc)

**Key Features**:
- **Broadcast without connection**: Receive data from hundreds of BLE devices simultaneously without establishing individual connections
- **Range**: Tested with ~120 devices in a 0-150 metre range with updates every 500ms
- **CBOR encoding**: Efficient binary encoding for minimal payload
- **Device identification**: Each device can be assigned a unique ID via `config.json` for easy tracking

**Use Case**: Ideal for IoT scenarios where you need to monitor many sensor devices simultaneously (e.g., distributed sensor networks, interactive installations) and is well-suited for lower data-rate transmission.

#### Getting Started with BLE Advertising

1. **Build and Upload**: Build and upload the firmware to your ESP32 board
2. **Configure Device**: Optionally modify the device name and ID in `config.json`
3. **Upload Filesystem**: Upload the data folder using the LittleFS upload tool
4. **Run BLE-CBOR-to-OSC Script**: 
   - Clone the [BLE-CBOR-to-OSC repository](https://gitlab.com/sat-mtl/collaborations/2024-iot/ble-cbor-to-osc)
   - Create a Python virtual environment : `python -m venv venv`
   - Activate the venv : `source ./venv/bin/activate`
   - Install Python dependencies: `pip install -r requirements.txt`
   - Run: `python ble-cbor-to-osc.py`
5. **Receive OSC Messages**: The script forwards BLE advertising data as OSC messages to `127.0.0.1:9001` (configurable)

#### BLE Configuration

You can customize the BLE advertising behavior:
- **Frequency**: Modify `target_frequency` variable (default 50Hz)
- **Sensor Data**: Replace dummy `sensor1` and `sensor2` with actual pin readings

For more information, see the [BLE-CBOR-to-OSC script documentation](https://gitlab.com/sat-mtl/collaborations/2024-iot/ble-cbor-to-osc).

## How to use

Use the Arduino IDE 2.0 or VS Code with the PlatformIO extension to work with these examples. The examples are designed to work on ESP32-based boards.

For detailed information about the Puara Module API and source code, refer to the [puara-module documentation](https://github.com/Puara/puara-module).


## Licensing

The code in this project is licensed under the MIT license, unless otherwise specified within the file.
