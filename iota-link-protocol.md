
## GPIO

### setPrimary(int pinID) [01] and setSecondary(int pinID) [02]

Configures pin pinID to primary or secondary function. Secondary pin function should be enabled before calling any of the secondary functions (e.g. analog, pwm, spi, i2c).

Parameters: int pinID - pin number (0-33)

By default GPIO is always the primary function. Some pins don't have secondary functions - that is their secondary function is also GPIO.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "setPrimary",
  "params": [<pinId>],
  "id": 1
}
```

```json
{
  "jsonrpc": "2.0",
  "method": "setSecondary",
  "params": [<pinId>],
  "id": 2
}
```

### pinMode(int pinID, int pinMode) [3]
Configures GPIO pin mode. Pin should be set to GPIO function before configuring its GPIO mode.

Parameters:

`int pinID` - pin number (0-33)

`int pinMode` - pin mode: 0 - input (high Z), 1 - output, 2 - input (pull down), 4 - input (pull up)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pinMode",
  "params": [<pinId>, <pinMode>],
  "id": 3
}
```

### digitalWrite(int pinID, int value) [4]
Sets GPIO output high or low.

Parameters:

`int pinID` - pin number (0-33)

`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalWrite",
  "params": [<pinId>, <value>],
  "id": 4
}
```

### digitalRead (int pinID) [5]
Reads digital state of the GPIO pin.

Parameters:

`int pinID` - pin number (0-33)

Returns message `digitalRead(int pinID, int value)`, where the value is 0 (LOW state) or 1 (HIGH state)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<pinId>],
  "id": 5
}
```

### attachInterrupt(int interruptID, int pinID, int mode) [6]
Attaches GPIO interrupt service to the specified pin. There is a total of 8 interrupts which can be attached to any pin. Interrupts can be configured to trigger at HIGH or LOW state or at RISING, FALLING or both edges. When the interrupt is triggered, device sends message interrupt(int interruptID, int event) [8], where event has the same meaning as mode.

Parameters:

`int interruptID` - interrupt number (0-7)

`int pinID` - pin number (0-33)

`int mode` - interrupt trigger mode: 0-LEVEL LOW, 1-LEVEL HIGH, 2-EDGE CHANGE, 3-EDGE RISE, 4-EDGE FALL.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<interruptID>, <pinID>, <mode>],
  "id": 6
}
```

### detachInterrupt(int interruptID) [7]
Detaches (disables) specified GPIO interrupt.

Parameters:

`int interruptID` - interrupt number (0-7)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "detachInterrupt",
  "params": [<interruptID>],
  "id": 7
}
```

## Analog (ADC)
### analogRead(int analogPinID) [10]

Parameters:

`int analogPinID` - analog pin number (0-7), corresponds to ADC0-ADC7 pins.

Returns message where value is 10 bit ADC read value.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "analogRead",
  "params": [<analogPinID>],
  "id": 6
}
```

## pulseIn (int pinID, int level, int timeout) [09]

Measures pulse duration on a pin. Is compatible with Arduino command pulseIn (http://arduino.cc/en/Reference/PulseIn).

Parameters:

`int pinID` - pin number (0-33)

`int level` - level of the pulse (0 - LOW level, 1 - HIGH)

`int timeout` - number of microseconds to wait until timeout

Returns message pulseIn(int pulseWidth) [09], where the pulseWidth is 0 if no pulse was detected during timeout period or pulse duration in microseconds.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<pinID>, <level>, <timeout>],
  "id": 9
}
```

## PWM
### pwm0_begin(int period) [50] and pwm1_begin(int period) [60]

Enables and configures PWM module. Each PWM module has 3 channels. PWM signal period (and frequency) is the same for all channels in one PWM module, but the signal high time (and duty cycle) can be different. PWM channels that are in the same module are synchronized.

Parameters:

`int period` - PWM signal period in microseconds. 16 bit value (1-65536) for PWM0 and 32bit value for PWM1.

### pwm0_set(int channel, int high_time) [51] and pwm1_set(int channel, int high_time) [61]

Sets the high time of the PWM channel.

Parameters:

`int channel` - PWM channel number (0-2)

`int high_time` - PWM signal high time in microseconds. 16 bit value (0-65535) for PWM0 and 32bit value for PWM1. This value should be less than PWM period or undefined behavior might occur.

### pwm0_end() [52] and pwm1_end() [62]

Stops the PWM module.

## SPI
### spi0_begin(int divider, int mode) [20] and spi1_begin(int divider, int mode) [30]

Starts and configures SPI module. This function configure device to master mode. Slave mode is not supported.

Parameters:

`int divider` - SCK signal frequency divider value (1-256). The primary SCK signal frequency is 2MHz.

`int mode` - standart SPI mode number (0-3) which determines clock polarity and phase (http://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus#Mode_numbers)

### spi0_trans(byte[] data, int respond) [21] and spi1_trans(byte[] data, int respond) [31]

Executes SPI transaction. User should manually (using GPIO functions) drive slave select line low when executing the transaction. Depending on respond value, the device can send back the data that it received from the slave by sending message spi0_trans(byte[] slaveData) [21].

Parameters:

`byte[] data` - bytes which will be sent to the slave.

`int respond` - value which tells if device should send back slave data: 0 - do not send, 1 - send back slave data.

### spi0_end() [22] and spi1_end() [32]

Disables the SPI module.

## I2C
### i2c_begin() [40]

Initializes I2C module.

### i2c_trans(int address, byte[] writeData, int readLength) [41]

Executes I2C MASTER WRITE and MASTER READ transactions. The data received during MASTER READ transaction is sent back to the host via i2c_trans(int address, byte[] receivedData) message. If error occurs during transactions i2c_trans(int address, int errorCode) [41] is sent back to the host.

Parameters:

`int address` - slave device address.

`byte[] writeData` - byte array which will be sent to the slave during MASTER WRITE. If blob size is 0 MASTER WRITE transaction is skipped.

`int readLength` - number of bytes to read during MASTER READ transaction. If readLength is 0 MASTER READ transaction is not executed.

### i2c_end() [42]

Disables I2C module.

## System
### registerWrite(int registerAddress, int value) [100]

Writes a value directly to the device register located at registerAddress.

### registerRead(int registerAddress) [101]

Reads a register value. Responds with registerRead(int registerAddress, int registerValue) [101] message.

### getDeviceInfo() [255]

Returns device information. Responds with getDeviceInfo(int firmwareVersion, byte[] UID, int lpcPartNumber, int lpcBootCodeVersion) [255] message.

`int firmwareVersion` - [31:24] bits are device (firmware) type and is 0x55 (ASCII 'U') for UPER board, [23:16] bits are firmware major version and [15:0] bits are firmware minor version.

`byte[] UID` - 16 byte long Unique IDentifier, which is read from the host device (LPC microcontroller).

`int lpcPartNumber` - device part number which is read from the host device (LPC microcontroller).

`int lpcBootCodeVersion` - device bootload code version which is read from the host device (LPC microcontroller).
