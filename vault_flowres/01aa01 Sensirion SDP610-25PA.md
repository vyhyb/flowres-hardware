# Sensirion SDP610-25PA

![[Attachments/01aa01 Sensirion SDP610-25PA.png|300]]

A [[01aa Digital differential pressure sensors|digital differential pressure sensor]], originally used in the setup made for David's thesis.

It is interfaced using I2C protocol, which is rather straight forward on [[04ab RaspberryPi GPIO header utilization|Raspberry Pi SBCs]].

Priced at approx. €40 ([farnell](https://cz.farnell.com/sensirion/sdp610-025pa/pressure-transducer-lin-25-to/dp/2085061)).
## Characteristics

- measurement range: -25 to 25 Pa
- zero-point accuracy: 0.1 Pa
- accuracy: 3% of reading

## Minimal code example
Given the [Claude AI](https://claude.ai/). Should be debugged for later use. Consult also with the other response in [[01aa02 Sensirion SDP810-125PA]] which gives some more functions for a better cleanup.
```python
import smbus
import time

class SDP610:
    # Default I2C address for SDP610
    DEFAULT_ADDRESS = 0x40
    
    # Commands
    TRIGGER_MEASUREMENT = 0xF1
    SOFT_RESET = 0xFE
    
    def __init__(self, bus_num=1, address=DEFAULT_ADDRESS):
        """Initialize the SDP610 sensor."""
        self.bus = smbus.SMBus(bus_num)
        self.address = address
        
        # Reset the sensor at startup
        self.reset()
        time.sleep(0.1)  # Wait for reset to complete
    
    def reset(self):
        """Perform a soft reset of the sensor."""
        try:
            self.bus.write_byte(self.address, self.SOFT_RESET)
            return True
        except Exception as e:
            print(f"Error resetting sensor: {e}")
            return False
    
    def read_pressure(self):
        """Read differential pressure value in Pascal."""
        try:
            # Trigger measurement
            self.bus.write_byte(self.address, self.TRIGGER_MEASUREMENT)
            time.sleep(0.1)  # Wait for measurement to complete
            
            # Read 3 bytes (2 for pressure, 1 for CRC)
            data = self.bus.read_i2c_block_data(self.address, 0, 3)
            
            # Convert the data
            raw_pressure = (data[0] << 8) | data[1]
            
            # For SDP610-25PA, scale factor is 240
            # See datasheet for details
            pressure_pa = raw_pressure / 240.0
            
            return pressure_pa
        except Exception as e:
            print(f"Error reading pressure: {e}")
            return None

# Example usage
if __name__ == "__main__":
    # Initialize sensor
    sensor = SDP610()
    
    try:
        while True:
            pressure = sensor.read_pressure()
            if pressure is not None:
                print(f"Differential Pressure: {pressure:.2f} Pa")
            time.sleep(1)
    except KeyboardInterrupt:
        print("Measurement stopped by user")
```

## Datasheet

[[Attachments/datasheets/SDP600.pdf|SDP6xx]]

