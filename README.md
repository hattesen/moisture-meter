# Industrial Moisture Meter

A moisture meter for (remote) measurement of wood.

Many probes can be monitored from a central location.

Suitable for use in drying kilns.

Optional (plug-in) hot wire type Anemometer for measuring air flow in kiln.

# Design

* Requirements
  * Operating temperatures: 0°C - 85°C (higher temperatures causes in less accurate results at low moisture content).
* Hardware design
  High resistance measurement (20kΩ - 1TΩ) using logarithmic amplifier.
  * Hardware Elements
    * MCU (low power, if battery operated)
      Typically ARM Cortex M0, e.g. STM32G0, STM32L0, RP2040
    * ADC (18+ bits, or 16 bits with PGA)
    * High impedance logarithmic amplifier using 3-4 FET Operational Amplifiers
    * Probes (nail-type) and probe interface (plug/terminal block)
  * Remote communication
    * Software update, configuration and log file access via USB MSD.
    * Wired
      Powered via interface cable, galvanically isolated to prevent ground current to impact low-current input
      * Galvanically isolated signals
      * Interface
        * RS485 (multidrop)
        * I2C (multidrop)
        * OneWire (multidrop)
    * Wireless
      Powered via battery (rechargable or replacable AA cells).
      * WiFi (AP and/or client)
        Web Server with HTML and REST API interfaces
      * Bluetooth (BLE)
        * Possibly supported Bluetooth profiles
          Generic profiles NOT requiring custom apps to be developed.
          * Service Discovery Application Profile (SDAP)
          * [Health_Device_Profile_(HDP)](https://en.wikipedia.org/wiki/List_of_Bluetooth_profiles#Health_Device_Profile_(HDP))
          * Human Interface Device Profile (HID)
          * LAN Access Profile (LAP) (as client, not server)
          * Serial Port Profile (SPP)
          * [OBject EXchange (OBEX)](https://en.wikipedia.org/wiki/OBject_EXchange)
* Firmware design
  * Remote communication
    All communication is initiated by remote system.
    * Configuration
      * Calibration values
      * Material Type (Species)
    * Input
      * Calibration of temperature, log.amp, anemometer
    * Output
      * Probe resistance (and metadata)
      * Temperature
      * Moisture Content (MC%) of material.
        Requires prior configuration of material. Dependent of temperature.
      * Air Velocity (m/s, f/m, km/h)
      * Power supply (battery) state

        
