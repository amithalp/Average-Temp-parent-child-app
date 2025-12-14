# Average Temperatures (Parent + Child App)

A Hubitat app that creates **multiple virtual temperature devices**, each representing a **weighted and optionally smoothed average** of one or more real temperature sensors.

This implementation uses a **Parent / Child app architecture**:
- One **Parent app** for centralized management
- Multiple **Child app instances**, each producing **one averaged virtual temperature device**

---

## Features

- ✅ Create **multiple averaged temperature devices** from a single parent app
- ✅ Select any number of temperature sensors per averager
- ✅ Per-sensor **weight** and **offset**
- ✅ Optional **running average** (smoothing over N events)
- ✅ Update suppression when the value doesn’t meaningfully change
- ✅ Optional **throttle** to reduce hub load
- ✅ Centralized logging control (parent → children)
- ✅ Uses **hub temperature scale** (°C / °F) safely
- ✅ Compatible with Rules, Dashboards, and automations

---

## Architecture Overview

Average Temperatures (Parent App)
├─ Average Living Room (Child App) → Virtual Temp Device
├─ Average Bedrooms (Child App) → Virtual Temp Device
└─ Average Outside (Child App) → Virtual Temp Device


Each **Child App**:
- Subscribes directly to its selected temperature sensors
- Computes a weighted average
- Updates its own **Virtual Temperature Sensor** device

---

## Installation

### 1. Add the Apps
1. In Hubitat UI → **Apps Code**
2. Add the **Parent App** code
3. Add the **Child App** code
4. Save both

### 2. Install the Parent App
1. Go to **Apps → Add User App**
2. Select **Average Temperatures (Parent)**

### 3. Create Averagers
1. Open the Parent app
2. Click **Add a new temperature averager**
3. Repeat for each averaged temperature you want

---

## Child App Configuration

### Basic Settings
- **Name this temperature averager**  
  Used as:
  - Child app name
  - Virtual temperature device name

- **Select Temperature Sensors**  
  One or more devices with `TemperatureMeasurement` capability

### Per-Sensor Controls
For each selected sensor:
- **Weight**  
  Multiplier applied to the sensor value  
  (default: `1.0`)
- **Offset**  
  Value added before weighting  
  (useful for calibration)

### Running Average
- **Running average window (events)**  
  - `1` = disabled (default)
  - `N > 1` = smooth average over last N computed values

---

## Performance & Logging

### Update Controls
- **Delta Threshold**  
  Minimum temperature change required before updating the virtual device  
  (default: `0.1°`)

- **Minimum Update Seconds**  
  Throttle updates to at most once every N seconds  
  (`0` = no throttle)

### Logging
- Logging can be **centrally controlled** from the Parent app
- Child apps can either:
  - Inherit parent logging settings, or
  - Override locally

Available options:
- Info logs
- Debug logs

---

## Temperature Units (°C / °F)

- The app **always uses the hub’s temperature scale**
- No conversion is performed inside the app
- The built-in **Virtual Temperature Sensor** preference is intentionally ignored

This matches Hubitat’s own reference implementation and avoids unit mismatch issues.

---

## Behavior Notes

- If all sensors temporarily report `null`, the app:
  - Skips updates
  - Waits for valid readings
  - Does **not** push `0.0`
- Virtual devices are only updated when:
  - The computed value changes by at least the configured threshold
  - And throttle limits allow it

---

## Uninstall Behavior

- When a Child App is removed:
  - The corresponding virtual device is deleted
  - If deletion fails (device still used in Rules/Dashboards), a warning is logged

---

## Recommended Use Cases

- Room-level average temperature
- Multi-sensor HVAC control
- Filtering noisy sensors
- Sensor fusion with calibration offsets

---

## Known Limitations

- Virtual Temperature Sensor driver preferences (°C/°F) cannot be reliably set programmatically
- Each averager still subscribes to its sensors (no shared aggregation)

---

## Credits

- Original concept and code: **Bruce Ravenel**
- Parent/Child architecture, stability, and performance improvements: **updated version**

---

## License

Use freely within Hubitat environments. Modify and redistribute as needed.
