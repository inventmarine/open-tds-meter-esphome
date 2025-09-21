# ESP8266 TDS Water Monitor

A comprehensive ESP8266-based water quality monitoring system using ESPHome. This project monitors Total Dissolved Solids (TDS) in water and integrates with Home Assistant.

## Features

- **Real-time TDS Monitoring**: Continuous measurement of Total Dissolved Solids in water
- **Temperature Compensation**: Automatic temperature compensation for accurate readings
- **WiFi Connectivity**: Wireless data transmission
- **Home Assistant Integration**: Automatic discovery and integration
- **OLED Display**: Real-time display of TDS value and water quality
- **Alert System**: Audio alerts when water quality is poor
- **Water Quality Rating**: Categorizes water quality from Excellent to Unacceptable

## Hardware Requirements

### Main Components
- **ESP8266 Development Board** (NodeMCU V2)
- **Gravity TDS Meter** (Analog TDS sensor)
- **OLED Display** (SSD1306 128x64)
- **Buzzer** for alerts
- **Breadboard and jumper wires**
- **USB cable** for programming and power

## Wiring Diagram

```
ESP8266                  TDS Sensor
-------                  ----------
3.3V     --------------> VCC
GND      --------------> GND
A0       --------------> Analog Output

ESP8266                  OLED Display (I2C)
-------                  -----------------
3.3V     --------------> VCC
GND      --------------> GND
D3       --------------> SDA
D5       --------------> SCL

ESP8266                  Buzzer
-------                  ------
D1       --------------> Positive
GND      --------------> Negative
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
   esphome upload open-tds.yaml --device /dev/cu.SLAB_USBtoUART
   ```
   Or if you have multiple devices connected:
   ```bash
   esphome run open-tds.yaml
   # Then select the appropriate USB port when prompted
   ```

6. **Monitor Logs**
   ```bash
   esphome logs open-tds.yaml
   ```

 ## Build & upload firmware
    `docker run --rm -v "${PWD}":/config -it esphome/esphome run open-tds.yaml --device open-tds.local`

 ## ESPHome Dashboard
    ```
    docker run --rm -p 6052:6052 -v "${PWD}":/config -it esphome/esphome dashboard config/
    open http://localhost:6052/
    ```

## Usage

### Basic Operation
1. Power on the ESP8266
2. The device will automatically connect to WiFi
3. TDS readings will be displayed on the OLED screen
4. The device will be automatically discovered by Home Assistant

### OLED Display Information
- **Top**: "TDS Monitor" title
- **Center**: Current TDS value in ppm
- **Quality Status**: Excellent/Good/Fair/Poor/Unacceptable with color coding
- **Bottom**: IP address and firmware version

### Water Quality Categories
- **Excellent**: < 50 ppm (Green)
- **Good**: 50-150 ppm (Green)
- **Fair**: 150-250 ppm (Yellow)
- **Poor**: 250-350 ppm (Red)
- **Unacceptable**: > 350 ppm (Red)

### Alert System
- The buzzer will sound every 30 seconds when water quality is Poor or Unacceptable

## TDS Sensor Calibration

The TDS calculation uses a polynomial formula with temperature compensation:
```
TDS = (133.42 * V³ - 255.86 * V² + 857.39 * V) * 0.5
```
Where V is the compensated voltage.

## Home Assistant Integration

The device will automatically appear in Home Assistant with the following entities:
- `sensor.tds_sensor` - Current TDS value in ppm
- `sensor.tds_sensor_raw` - Raw voltage reading
- `sensor.tds_sensor_water_quality` - Quality rating (1-5)

## Troubleshooting

### Common Issues

1. **WiFi Connection Failed**
   - Check WiFi credentials in `secrets.yaml`
   - Ensure WiFi network is 2.4GHz (ESP8266 doesn't support 5GHz)
   - Check signal strength

2. **ESPHome Command Not Found**
   - Make sure you've activated the virtual environment
   - Reinstall ESPHome: `pip install esphome`

3. **Upload Failed**
   - Check USB cable connection
   - Try different USB port
   - Verify the correct device port (e.g., /dev/cu.SLAB_USBtoUART)

4. **Inaccurate TDS Readings**
   - Ensure sensor is properly connected to A0 pin
   - Check for stable 3.3V power supply
   - Allow sensor to stabilize in water for 30 seconds

## Project Structure

```
open-tds-monitor/
├── open-tds.yaml           # ESPHome configuration
├── secrets.yaml            # WiFi credentials (keep private)
├── platformio.ini          # Legacy PlatformIO config (not used)
└── README.md               # This file
```

## License

This project is open source. Feel free to modify and distribute according to your needs.

## Contributing

Contributions are welcome! Please feel free to submit issues, feature requests, or pull requests.

## Support

For technical support or questions:
- Check the troubleshooting section above
- Review the ESPHome documentation
- Open an issue in the project repository

---

**Note**: This project is designed for educational and monitoring purposes. For critical applications, ensure proper calibration and validation of readings.