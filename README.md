# ESP8266 TDS Water Monitor

A TDS meter using ESPHome. It monitors Total Dissolved Solids (TDS) in water and integrates with Home Assistant.
All parts sourced from AliExpress/Amazon/eBay.



## Features

- **Real-time TDS Monitoring**: Continuous measurement of Total Dissolved Solids in water
- **Temperature Compensation**: Automatic temperature compensation for accurate readings
- **WiFi Connectivity**: Wireless data transmission
- **Home Assistant Integration**: Automatic discovery and integration
- **OLED Display**: Real-time display of TDS value and water quality

## Hardware Requirements

### Main Components
- **ESP32 Development Board** 
- **TDS Sensor** (Analog TDS sensor x2) - Choose one:
  - [DFRobot Gravity Analog TDS Sensor](https://www.dfrobot.com/product-1662.html) (SKU: SEN0244)
  - [Generic TDS Meter Module](https://www.aliexpress.com/item/1005001698342637.html) - Compatible alternative
- **OLED Display** - 0.96" 128x64 I2C:
  - [SSD1306 OLED Display](https://www.aliexpress.com/item/1005007614149117.html) 
  - Any SSD1306-based 128x64 I2C OLED display module

## Wiring Diagram

```
ESP32                    TDS Sensor 1
-----                    ------------
3.3V/5V  --------------> VCC
GND      --------------> GND
GPIO33   --------------> Analog Output

ESP32                    TDS Sensor 2
-----                    ------------
3.3V/5V  --------------> VCC
GND      --------------> GND
GPIO32   --------------> Analog Output

ESP32                    OLED Display (I2C)
-----                    -----------------
3.3V     --------------> VCC
GND      --------------> GND
GPIO21   --------------> SDA
GPIO22   --------------> SCL

```

## Software Setup

### Prerequisites
- Python 3.x
- ESPHome

### Installation Steps

1. **Clone or download this project**
   ```bash
   git clone <repository-url>
   cd open-tds-monitor
   ```

2. **Set up Python virtual environment**
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On macOS/Linux
   ```

3. **Install ESPHome**
   ```bash
   pip install esphome
   ```

4. **Configure WiFi Settings**
   Edit `secrets.yaml` and update the WiFi credentials:
   ```yaml
   wifi_ssid: "YOUR_WIFI_SSID"
   wifi_password: "YOUR_WIFI_PASSWORD"
   wifi_hotspot_pwd: "12345678"  # Must be at least 8 characters
   ```

5. **Upload the Code**
   ```bash
   esphome compile open-tds.yaml
   esphome upload open-tds.yaml --device /dev/cu.SLAB_USBtoUART
   ```
   Or if you have multiple devices connected:
   ```bash
   esphome run open-tds.yaml
   # Then select the appropriate USB port when prompted
   ```

6. **Monitor Logs (Deployment)**
   ```bash   
   source venv/bin/activate && esphome logs open-tds.yaml --device open-tds.local
   ```

## Using Docker
Alternativelly, one can use ESPHome in Docker to develop and test 
 1.  **Build & upload firmware**
    `docker run --rm -v "${PWD}":/config -it esphome/esphome run open-tds.yaml --device open-tds.local`

 2. **ESPHome Dashboard**
    ```
    docker run --rm -p 6052:6052 -v "${PWD}":/config -it esphome/esphome dashboard config/
    open http://localhost:6052/
    ```

## Usage

### Calibration Instructions

The TDS sensor requires calibration for accurate readings. The system supports calibration using a reference TDS solution.

#### What You'll Need
- A calibration solution with known TDS value (e.g 90 ppm standard solution)
- Clean the sensor probe before calibration
- Allow the sensor to stabilize in the solution for 2-3 minutes

#### Calibration Steps

1. **Via Home Assistant**:
   - Navigate to your device in Home Assistant
   - Find the calibration controls:
     - `TDS Cal Reference - Sensor 1 (ppm)` - Set to your solution's TDS value for sensor 1
     - `TDS Cal Reference - Sensor 2 (ppm)` - Set to your solution's TDS value for sensor 2
   - Place the selected sensor in your calibration solution
   - Wait 2-3 minutes for readings to stabilize
   - Click the `Calibrate TDS Sensor 1` or `Calibrate TDS Sensor 2` button
   - The system will calculate and apply the new K-value to the selected sensor

2. **Via Web Interface**:
   - Access the device's web interface at `http://open-tds.local`
   - Use the same calibration controls as above

3. **Understanding K-Value**:
   - The K-value is a calibration coefficient that compensates for sensor variations
   - Default K-value is 1.0
   - After calibration, each sensor maintains its own K-value
   - The K-value is saved and persists through power cycles
   - You can reset to default calibration using the `Reset TDS Calibration` button

#### Calibration Tips
- Calibrate when first setting up the sensors
- Recalibrate if readings seem inaccurate
- Use a calibration solution close to your expected measurement range
- Common calibration solutions:
  - 342 ppm (KCl solution)
  - 1413 ppm (NaCl solution)
  - 2764 ppm (High range solution)
- Clean sensors with distilled water before and after calibration

## Home Assistant Integration

The device will automatically appear in Home Assistant with the following entities:
- `sensor.tds_sensor_1` - TDS value for sensor 1 in ppm
- `sensor.tds_sensor_2` - TDS value for sensor 2 in ppm
- `sensor.tds_sensor_1_voltage` - Voltage reading for sensor 1
- `sensor.tds_sensor_2_voltage` - Voltage reading for sensor 2
- `sensor.tds_k_value_sensor_1` - Calibration K-value for sensor 1
- `sensor.tds_k_value_sensor_2` - Calibration K-value for sensor 2
- Various calibration and diagnostic controls

