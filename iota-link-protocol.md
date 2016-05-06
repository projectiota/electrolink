
## GPIO

### setFunction(int pinId, int fnc)

Configures pin `pinId` to primary or secondary function. Secondary pin function should be enabled before calling any of the secondary functions (e.g. analog, pwm, spi, i2c).

Parameters:

`int pinId` - pin number (0-33)
`int fnc` - function number

By default GPIO is always the primary function. Some pins don't have secondary functions - that is their secondary function is also GPIO.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "setPrimary",
  "params": [<pinId>, <fnc>],
  "id": <msgId>
}
```

### pinMode(int pinId, int pinMode)
Configures GPIO pin mode. Pin should be set to GPIO function before configuring its GPIO mode.

Parameters:

`int pinId` - pin number (0-33)

`int pinMode` - pin mode: 0 - input (high Z), 1 - output, 2 - input (pull down), 4 - input (pull up)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pinMode",
  "params": [<pinId>, <pinMode>],
  "id": <msgId>
}
```

### digitalWrite(int pinId, int value)
Sets GPIO output high or low.

Parameters:

`int pinId` - pin number (0-33)

`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalWrite",
  "params": [<pinId>, <value>],
  "id": <UUID>
}
```

### digitalRead (int pinId)
Reads digital state of the GPIO pin.

Parameters:

`int pinId` - pin number (0-33)

Returns message `digitalRead(int pinId, int value)`, where the value is 0 (LOW state) or 1 (HIGH state)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<pinId>],
  "id": <msgId>
}
```

### attachInterrupt(int interruptId, int pinId, int mode)
Attaches GPIO interrupt service to the specified pin. There is a total of 8 interrupts which can be attached to any pin. Interrupts can be configured to trigger at HIGH or LOW state or at RISING, FALLING or both edges. When the interrupt is triggered, device sends message `interrupt(int interruptId, int event)`, where event has the same meaning as mode.

Parameters:

`int interruptId` - interrupt number (0-7)

`int pinId` - pin number (0-33)

`int mode` - interrupt trigger mode: 0-LEVEL LOW, 1-LEVEL HIGH, 2-EDGE CHANGE, 3-EDGE RISE, 4-EDGE FALL.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<interruptId>, <pinId>, <mode>],
  "id": <msgId>
}
```

### detachInterrupt(int interruptId)
Detaches (disables) specified GPIO interrupt.

Parameters:

`int interruptId` - interrupt number (0-7)

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "detachInterrupt",
  "params": [<interruptId>],
  "id": <msgId>
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
  "id": <msgId>
}
```

## pulseIn (int pinId, int level, int timeout) [09]

Measures pulse duration on a pin. Is compatible with Arduino command pulseIn (http://arduino.cc/en/Reference/PulseIn).

Parameters:

`int pinId` - pin number (0-33)

`int level` - level of the pulse (0 - LOW level, 1 - HIGH)

`int timeout` - number of microseconds to wait until timeout

Returns message pulseIn(int pulseWidth) [09], where the pulseWidth is 0 if no pulse was detected during timeout period or pulse duration in microseconds.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pulseIn",
  "params": [<pinId>, <level>, <timeout>],
  "id": <msgId>
}
```

## PWM
### pwmStart(int pwmNb, int period)

Enables and configures PWM module. Each PWM module has 3 channels. PWM signal period (and frequency) is the same for all channels in one PWM module, but the signal high time (and duty cycle) can be different. PWM channels that are in the same module are synchronized.

Parameters:

`int pwmNb` - PWM module selection

`int period` - PWM signal period in microseconds. 16 bit value (1-65536) for PWM0 and 32bit value for PWM1.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pwmStart",
  "params": [<pwmNb>, <period>],
  "id": <msgId>
}
```


### pwmSet(int pwmNb, int channel, int high_time)

Sets the high time of the PWM channel for a selected PWM module.

Parameters:

`int pwmNb` - PWM module selection

`int channel` - PWM channel number (0-2)

`int high_time` - PWM signal high time in microseconds. 16 bit value (0-65535) for PWM0 and 32bit value for PWM1. This value should be less than PWM period or undefined behavior might occur.

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pwmSet",
  "params": [<pwmNb>, <channlel>, <high_time>],
  "id": <msgId>
}
```

### pwmStop(int pwmNb)

Stops the PWM module.

Parameters:

`int pwmNb` - PWM module selction

JSON:
```json
{
  "jsonrpc": "2.0",
  "method": "pwmStop",
  "params": [<pwmNb>],
  "id": <msgId>
}
```


## SPI
### spiStart(int spiNb, int divider, int mode)

Starts and configures SPI module. This function configure device to master mode. Slave mode is not supported.

Parameters:

`int spiNb` - SPI module selection

`int divider` - SCK signal frequency divider value (1-256). The primary SCK signal frequency is 2MHz.

`int mode` - standart SPI mode number (0-3) which determines clock polarity and phase (http://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus#Mode_numbers)

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "spiStart",
  "params": [<spiNb>, <divider>, <mode>],
  "id": <msgId>
}
```

### spiTx(int spiNb, byte[] data, int rsp)

Executes SPI transaction. User should manually (using GPIO functions) drive slave select line low when executing the transaction. Depending on respond value, the device can send back the data that it received from the slave by sending message `spiTx(byte[] slaveData)`.

Parameters:

`int spiNb` - SPI module selection

`byte[] data` - bytes which will be sent to the slave.

`int rsp` - value which tells if device should send back slave data: 0 - do not send, 1 - send back slave data.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "spiTx",
  "params": [<spiNb>, <data>, <rsp>],
  "id": <msgId>
}
```

### spiStop(int spiNb)

Disables the SPI module.

`int spiNb` - SPI module selection

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "spiStop",
  "params": [<spiNb>],
  "id": <msgId>
}
```

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
