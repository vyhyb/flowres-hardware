# Sensirion SDP810-125PA

![[Attachments/01aa02 Sensirion SDP810-125PA.png|300]]

A [[01aa Digital differential pressure sensors|digital differential pressure sensor]], successor of the [[01aa01 Sensirion SDP610-25PA]].

It is interfaced using I2C protocol, which is rather straight forward on [[04ab RaspberryPi GPIO header utilization|Raspberry Pi SBCs]].

Priced at approx. €30 ([farnell](https://cz.farnell.com/sensirion/sdp810-125pa/pressure-sensor-diff-125pa-digital/dp/2886665)).

## Characteristics

- measurement range: -125 to 125 Pa
- zero-point accuracy: 0.08 Pa
- accuracy: 3% of reading

This means it is a **cheaper**, more or **similarly accurate** product compared to [[01aa01 Sensirion SDP610-25PA]], which is also suitable for a **wider pressure range**.

## Minimal code example

Given the [Claude AI](https://claude.ai/). Should be debugged for later use.

```python
import smbus
import time

class SDP810:
    # Default I2C address for SDP810
    DEFAULT_ADDRESS = 0x25  # Note: SDP810 default address is 0x25 (may vary by variant)
    
    # Commands
    START_CONTINUOUS_MEASUREMENT = 0x3603
    STOP_MEASUREMENT = 0x3FF9
    SOFT_RESET = 0x0006
    
    def __init__(self, bus_num=1, address=DEFAULT_ADDRESS):
        """Initialize the SDP810 sensor."""
        self.bus = smbus.SMBus(bus_num)
        self.address = address
        
        # Reset the sensor at startup
        self.reset()
        time.sleep(0.1)  # Wait for reset to complete
        
        # Start continuous measurement
        self.start_measurement()
        time.sleep(0.1)  # Wait for command to take effect
    
    def reset(self):
        """Perform a soft reset of the sensor."""
        try:
            self.bus.write_i2c_block_data(self.address, 0x00, [(self.SOFT_RESET >> 8) & 0xFF, self.SOFT_RESET & 0xFF])
            return True
        except Exception as e:
            print(f"Error resetting sensor: {e}")
            return False
    
    def start_measurement(self):
        """Start continuous measurement mode."""
        try:
            self.bus.write_i2c_block_data(self.address, 0x36, [0x03])
            return True
        except Exception as e:
            print(f"Error starting measurement: {e}")
            return False
    
    def stop_measurement(self):
        """Stop continuous measurement mode."""
        try:
            self.bus.write_i2c_block_data(self.address, 0x3F, [0xF9])
            return True
        except Exception as e:
            print(f"Error stopping measurement: {e}")
            return False
    
    def read_pressure(self):
        """Read differential pressure value in Pascal."""
        try:
            # Read 3 bytes (2 for pressure, 1 for CRC)
            data = self.bus.read_i2c_block_data(self.address, 0x00, 3)
            
            # Convert the data
            raw_pressure = (data[0] << 8) | data[1]
            
            # For SDP810-125Pa, scale factor is 60
            # See datasheet for details
            pressure_pa = raw_pressure / 60.0
            
            return pressure_pa
        except Exception as e:
            print(f"Error reading pressure: {e}")
            return None
    
    def __del__(self):
        """Clean up when object is deleted."""
        try:
            self.stop_measurement()
        except:
            pass

# Example usage
if __name__ == "__main__":
    # Initialize sensor
    sensor = SDP810()
    
    try:
        while True:
            pressure = sensor.read_pressure()
            if pressure is not None:
                print(f"Differential Pressure: {pressure:.2f} Pa")
            time.sleep(1)
    except KeyboardInterrupt:
        print("Measurement stopped by user")
        sensor.stop_measurement()
```

## Datasheet

[[Attachments/datasheets/SDP800.pdf|SDP810]]


