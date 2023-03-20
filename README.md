# Industrial Moisture Meter

A moisture meter for (remote) measurement of wood.

Many probes can be monitored from a central location.

Suitable for use in drying kilns.

Optional (plug-in) hot wire type Anemometer for measuring air flow in kiln.

# Design

## Requirements
  * Operating temperatures: 0°C - 85°C (higher temperatures causes in less accurate results at low moisture content).
  * Resistances between 50 kΩ (Philipine Mahogany @ 25% MC) to 700 GΩ (Pine/Spruce @ 7% MC) must be covered.

## Hardware design

High resistance measurement (20kΩ - 1TΩ) using logarithmic amplifier.

  * Hardware Elements
    * Logarithmic Amplifier.
      Using 3-4 FET-input Operational Amplifiers, and matched BJT transistors in feedback.
      * Resistance and current measurements (assuming 3V reference): 10kΩ to 1 TΩ resistance resulting in currents of 3e-12 to 3e-4 A (3 pA to 300 µA), spanning 8 decades.
      * reference resistance: 10 MΩ.
      * Temperature compensation using reference resistance logarithm variance of approx -2mV/°K to compensate for temperature related change.
    * MCU (low power, if battery operated)
      Typically ARM Cortex M0, e.g. STM32G0, STM32L0, RP2040
    * ADC
      Use either MCU built-in ADC or external ADC depending on resolition and linearity.
      LogAmp output varies by approx 60mV/decade and -2mV/°K. With 8 decades and a temperature range of -10°C - 85°C, a LogAmp output variance of 8*60 mV + 95*2 mV must be covered.
      * Log Amp output ranges
        * Reference LogAmp (10MΩ@3V, -10..85°C): 625..425 mV (580 mV @20°C)
        * Measurement LogAmp (10kΩ..1TΩ, -10..85°C): 150..790 mV (280..760 mV @20°C)
      * ADC resolution required to provide 0.1% MC resolution:
        * Douglas Pine (@25°C): 22GΩ @7% MC, 500kΩ @25% MC => 5 decades => 300 mV LogAmp output variance.
        * mV/%MC = 300/18 mV => 17mV/%MC => 1.7mV/0.1%MC
        * Assuming a total ADC range of 0..1000mV a 10-bit ADC resolution will be sufficient to provide a 0.1% MC resolution.
    * Probes (nail-type) and probe interface (plug/terminal block), e.g. Aviation Connectors (GX12 or GX16) using cores with individual shield connected to guard voltages (3V/0V).
  * Remote communication
    * Software update, configuration and log file access via USB MSD/MTP
    * Wired
      Powered via interface cable, galvanically isolated to prevent ground current to impact low-current input
      * Galvanically isolated signals
      * Interface
        * Isolated RS485 multidrop, Modbus ASCII protocol
        * Isolated I2C multidrop
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

### Components

* Log OpAmp.  
  Dual, ultra low input bias current (<1pA), low input offset
  * [HTCSEMI LMV358ARZ, 1pA bias, 0.5mV offset, $0.05](https://datasheet.lcsc.com/lcsc/2201141700_HTCSEMI-LMV358ARZ_C2928823.pdf)
  * [3PEAK LMV358A-SR, 1pA bias, 1mV offset, $0.07](https://datasheet.lcsc.com/lcsc/1811071714_3PEAK-LMV358A-SR_C98461.pdf)
  * [3PEAK LMV324 **QUAD**, 1pA bias, 0.1mV offset, $0.16](https://datasheet.lcsc.com/lcsc/1811071512_3PEAK-LMV324TP-SR_C90298.pdf) 
  * [HGSEMI LMV324M/TR **QUAD**, 1pA bias, 0.8 mV offset, $0.11](https://datasheet.lcsc.com/lcsc/1811021633_HGSEMI-LMV324M-TR_C316672.pdf)
  * [TI TLV8811/TLV8812 (single/dual), 0.1pA bias, 0.1mV offset, $0.36](https://www.ti.com/lit/gpn/TLV8811)
  * [GS8052, 1pA bias, 2mV offset, $0.23](https://datasheet.lcsc.com/lcsc/2206101816_Gainsil-GS8052-SR_C157722.pdf)
  * [RS8752XK, 1pA bias, 1.5mV offset, $0.24](https://datasheet.lcsc.com/lcsc/2202251930_Jiangsu-RUNIC-Tech-RS8752XK_C236994.pdf)
  * [TI LMV342IDR, 1pA bias, 6fA bias offset, 0.25mV offset, $0.32](https://www.ti.com/lit/ds/symlink/lmv342.pdf)
  * [TI LMV344IDR **QUAD**, 1pA bias, 6fA bias offset, 0.25mV offset, $0.48](https://www.ti.com/lit/ds/symlink/lmv344.pdf)
  * [Microchip MCP6022T, 1pA bias, 0.5mV offset, $0.68](https://datasheet.lcsc.com/lcsc/1809191930_Microchip-Tech-MCP6022T-I-SN_C57639.pdf)
  * [3PEAK TPH2502-SR, 0.3pA bias, 50uV offset, $0.69](https://datasheet.lcsc.com/lcsc/1810010114_3PEAK-TPH2502-SR_C118223.pdf)
  * [TI TLV9004SIRTER **quad**, 5pA bias, 0.4mV offset, $0.25](https://www.ti.com/lit/ds/symlink/tlv9002.pdf)
  * [TI TLV9002SIDGSR, 5pA bias, 0.4mV offset, $0.27](https://www.ti.com/lit/ds/symlink/tlv9002.pdf)
  * [TI LPV542, 1pA bias, 3mV offset, $0.92](https://www.ti.com/lit/gpn/LPV542)
  * [TI LMV797, 1pA bias, 1.35 mV offset, $1.11](https://www.ti.com/lit/gpn/LMV797)
  * [TI LMC6442, 50fA bias, 3mV offset, $1.50](https://www.ti.com/lit/gpn/LMC6442)
  * [TI LMC662C, 2fA bias, 1mV offset, $1.30](https://www.ti.com/lit/ds/symlink/lmc662.pdf)
  * [TI OPA2365AIDR, 200fA bias, 0.1mV offset, $1.55](https://www.ti.com/lit/ds/symlink/opa2365.pdf)
  * [TI LMC6062, 100fA bias, 0.35mV offset, $2.00](https://www.ti.com/lit/gpn/LMC6062)
* Amplifier OpAmp.  
  Dual, amplifies Log output for ADC (approx x 3)
  * [HTCSEMI LMV358ARZ, 1pA bias, 0.5mV offset, $0.05](https://datasheet.lcsc.com/lcsc/2201141700_HTCSEMI-LMV358ARZ_C2928823.pdf)
  * [TI LMV358AIDR, 1 mV offset, $0.17 (alt src $0.05)](https://www.ti.com/lit/gpn/LMV358A)
* Dual matched BJT (NPN)
  * [Diodes DMMT3904W, $0.06](https://datasheet.lcsc.com/lcsc/1808280024_Diodes-Incorporated-DMMT3904W-7-F_C155305.pdf)
  * [Nexperia BCM847BS, $0.17](https://datasheet.lcsc.com/lcsc/2007030434_Nexperia-BCM847BS-135_C549556.pdf)
* Voltage Inverter, charge pump, for OpAmp negative supply
  - [HGSEMI TC7660, 10 kHz, 30 mA, $0.11](https://files.icx2.com/UploadFiles/eb57ece3-2819-4c1f-9a14-3404d5f8554f/docs/20221013154811_2975.pdf)
  - [ME7660CS1G, <=10 kHz, 30 mA, $0.25](https://datasheet.lcsc.com/lcsc/1811151444_MICRONE-Nanjing-Micro-One-Elec-ME7660CS1G_C88402.pdf)
  - [TI LM2664, 160 kHz, $0.44](https://datasheet.lcsc.com/lcsc/1809051020_Texas-Instruments-LM2664M6X-NOPB_C108573.pdf)
  - [TI TL7660CDR, 10 kHz, $0.53 **also voltage doubler**](https://datasheet.lcsc.com/lcsc/1810181612_Texas-Instruments-TL7660CDR_C130206.pdf)
  - [SGM3204, 950 kHz, 200 mA, $0.55](https://datasheet.lcsc.com/lcsc/1811091924_SGMICRO-SGM3204YN6G-TR_C194031.pdf)
  - [TI TPS60403, 20-250 kHz, 60 mA, $0.55](https://www.ti.com/lit/ds/symlink/tps60400.pdf)
  - [TI LM27762, 2 MHz, 250 mA, $0.81 **Positive and Negative Output Integrated Charge Pump Plus LDO**](https://datasheet.lcsc.com/lcsc/2001031722_Texas-Instruments-LM27762DSSR_C473398.pdf)
 
* Input protection, ESD
  * Sense input  
    Protected by back-to-back diodes GND, as OpAmp will keep IN- == IN+ == GND.  
    See article [Ultra-low Leakage ESD Protection Achieving 10.5 fA Leakage](moisture-meter/documentation/Ultra-low Leakage ESD Protection Achieving 10.5 fA Leakage - Hall \(ISCAS, 2021\).pdf) and [ADA4530-1 datasheet schematic, figure 99](https://www.analog.com/media/en/technical-documentation/data-sheets/ada4530-1.pdf)
    * Low leakage back-to-back dual diode
      * [Nexperia BAV199, Ir: 3pA@75V@25°C](https://datasheet.lcsc.com/lcsc/2107272240_Nexperia-BAV199-235_C549304.pdf)
      * [Nexperia BAS416, Ir: 3pA@75V@25°C](https://assets.nexperia.com/documents/data-sheet/BAS416.pdf)
  * Low leakage TVS diode
    * [Applied Power AR3321P1S](https://datasheet.lcsc.com/lcsc/2205171716_Applied-Power-AR3321P1S_C3002757.pdf)
    * [Nexperia PTVS3V3D1BAL](https://datasheet.lcsc.com/lcsc/2006111312_Nexperia-PTVS3V3D1BALYL_C553419.pdf)
* TBD Isolated Power Supply (cabled connection)
* TBD Isolated IO for cabled host communication (RS485)
  
## Firmware design
  * Remote communication
    All communication is initiated by remote system.
    * Protocol(s)
      * RS485 (wired)
        * Modbus
        * [SCPI](https://en.wikipedia.org/wiki/Standard_Commands_for_Programmable_Instruments)
      * I2C (wired): Register convention
      * WiFi (wireless)
        * HTTP/REST (JSON)
        * [LXI](https://en.wikipedia.org/wiki/LAN_eXtensions_for_Instrumentation) using [SCPI](https://en.wikipedia.org/wiki/Standard_Commands_for_Programmable_Instruments)
      * Bluetooth (wireless): [OBEX](https://en.wikipedia.org/wiki/OBject_EXchange)
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

## Resources

* [Picoammeter Design](https://www.eevblog.com/forum/projects/picoammeter-design/msg790045/#msg790045)

