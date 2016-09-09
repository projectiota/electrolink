## Function List

| SUBSYS              | FNC                                                 |
|:--------------------|:----------------------------------------------------|
| GPIO                | **pinFunction**(int pinId, int pinFnc)              |
|                     | **pinMode**(int pinId, int pinMode)                 |
|                     | **digitalWrite**(int pinId, int value)              |
|                     | **digitalRead**(int pinId)                          |
| INT                 | **attachInterrupt**(int intId, int pinId, int mode) |
|                     | **detachInterrupt**(int intId)                      |
| ADC                 | **analogRead**(int analogPinID)                     |
|                     | **pulseIn**(int pinId, int level, int timeout)      |
| PWM                 | **pwmStart**(int pwmNb, int period)                 |
|                     | **pwmSet**(int pwmNb, int channel, int highTime)    |
|                     | **pwmStop**(int pwmNb)                              |
| SPI                 | **spiStart**(int spiNb, int divider, int mode)      |
|                     | **spiTransfer**(int spiNb, byte[] data, int rsp)    |
|                     | **spiStop**(int spiNb)                              |
| I2C                 | **i2cStart**()                                      |
|                     | **i2cTransfer**(int addr, byte[] wrData, int rdLen) |
|                     | **i2cStop**()                                       |
| SYSTEM              | **registerWrite**(int regAddr, int val)             |
|                     | **registerRead**(int regAddr)                       |
|                     | **getDeviceInfo**()                                 |



## GPIO

### pinFunction(int pinId, int pinFnc)

Configures pin `pinId` to primary or secondary function. Secondary pin function should be enabled before calling any of the secondary functions (e.g. analog, pwm, spi, i2c).

Parameters:

`int pinId` - pin number (0-33)
`int pinFnc` - function number

By default GPIO is always the primary function. Some pins don't have secondary functions - that is their secondary function is also GPIO.

JSON:
```
{
  "method": "pinFunction",
  "params": [<pinId>, <pinFnc>]
}
```

### pinMode(int pinId, int pinMode)
Configures GPIO pin mode. Pin should be set to GPIO function before configuring its GPIO mode.

Parameters:

`int pinId` - pin number (0-33)

`int pinMode` - pin mode: 0 - input (high Z), 1 - output, 2 - input (pull down), 4 - input (pull up)

JSON:
```
{
  "method": "pinMode",
  "params": [<pinId>, <pinMode>]
}
```

### digitalWrite(int pinId, int value)
Sets GPIO output high or low.

Parameters:

`int pinId` - pin number (0-33)

`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```
{
  "method": "digitalWrite"
  "params": [<pinId>, <value>]
}
```

### digitalRead (int pinId)
Reads digital state of the GPIO pin.

Parameters:

`int pinId` - pin number (0-33)

Returns message `digitalRead(int pinId, int value)`, where the value is 0 (LOW state) or 1 (HIGH state)

JSON:
```
{
  "method": "digitalRead",
  "params": [<pinId>]
}
```

### attachInterrupt(int intId, int pinId, int mode)
Attaches GPIO interrupt service to the specified pin. There is a total of 8 interrupts which can be attached to any pin. Interrupts can be configured to trigger at HIGH or LOW state or at RISING, FALLING or both edges. When the interrupt is triggered, device sends message `interrupt(int interruptId, int event)`, where event has the same meaning as mode.

Parameters:

`int intId` - interrupt number (0-7)

`int pinId` - pin number (0-33)

`int mode` - interrupt trigger mode: 0-LEVEL LOW, 1-LEVEL HIGH, 2-EDGE CHANGE, 3-EDGE RISE, 4-EDGE FALL.

JSON:
```
{
  "method": "attachInterrupt",
  "params": [<intId>, <pinId>, <mode>]
}
```

### detachInterrupt(int intId)
Detaches (disables) specified GPIO interrupt.

Parameters:

`int intId` - interrupt number (0-7)

JSON:
```
{
  "method": "detachInterrupt",
  "params": [<intId>]
}
```

## Analog (ADC)
### `[70]` analogRead(int analogPinID)

Parameters:

`int analogPinID` - analog pin number (0-7), corresponds to ADC0-ADC7 pins.

Returns message where value is 10 bit ADC read value.

JSON:
```
{
  "fnc": 70,
  "params": [<analogPinID>],
  "rto": <replyTopic>
}
```

### pulseIn(int pinId, int level, int timeout)

Measures pulse duration on a pin. Is compatible with Arduino command pulseIn (http://arduino.cc/en/Reference/PulseIn).

Parameters:

`int pinId` - pin number (0-33)

`int level` - level of the pulse (0 - LOW level, 1 - HIGH)

`int timeout` - number of microseconds to wait until timeout

Returns message pulseIn(int pulseWidth) [09], where the pulseWidth is 0 if no pulse was detected during timeout period or pulse duration in microseconds.

JSON:
```
{
  "method": "pulseIn",
  "params": [<pinId>, <level>, <timeout>]
}
```

## PWM
### pwmStart(int pwmNb, int period)

Enables and configures PWM module. Each PWM module has 3 channels. PWM signal period (and frequency) is the same for all channels in one PWM module, but the signal high time (and duty cycle) can be different. PWM channels that are in the same module are synchronized.

Parameters:

`int pwmNb` - PWM module selection

`int period` - PWM signal period in microseconds. 16 bit value (1-65536) for PWM0 and 32bit value for PWM1.

JSON:
```
{
  "method": "pwmStart",
  "params": [<pwmNb>, <period>]
}
```


### pwmSet(int pwmNb, int channel, int highTime)

Sets the high time of the PWM channel for a selected PWM module.

Parameters:

`int pwmNb` - PWM module selection

`int channel` - PWM channel number (0-2)

`int highTime` - PWM signal high time in microseconds. 16 bit value (0-65535) for PWM0 and 32bit value for PWM1. This value should be less than PWM period or undefined behavior might occur.

JSON:
```
{
  "method": "pwmSet",
  "params": [<pwmNb>, <channlel>, <highTime>]
}
```

### pwmStop(int pwmNb)

Stops the PWM module.

Parameters:

`int pwmNb` - PWM module selction

JSON:
```
{
  "method": "pwmStop",
  "params": [<pwmNb>]
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
  "method": "spiStart",
  "params": [<spiNb>, <divider>, <mode>]
}
```

### spiTransfer(int spiNb, byte[] data, int rsp)

Executes SPI transaction. User should manually (using GPIO functions) drive slave select line low when executing the transaction. Depending on respond value, the device can send back the data that it received from the slave by sending message `spiTxRx(byte[] slaveData)`.

Parameters:

`int spiNb` - SPI module selection

`byte[] data` - bytes which will be sent to the slave.

`int rsp` - value which tells if device should send back slave data: 0 - do not send, 1 - send back slave data.

JSON:
```
{
  "method": "spiTransfer",
  "params": [<spiNb>, <data>, <rsp>]
}
```

### spiStop(int spiNb)

Disables the SPI module.

`int spiNb` - SPI module selection

JSON:
```
{
  "method": "spiStop",
  "params": [<spiNb>]
}
```

## I2C
### i2cStart()

Initializes I2C module.

JSON:
```
{
  "method": "i2cStart",
  "params": []
}
```

### i2cTransfer(int addr, byte[] wrData, int rdLen)

Executes I2C MASTER WRITE and MASTER READ transactions. The data received during MASTER READ transaction is sent back to the host via `i2cTxRx(int addr, byte[] rcvData)` message. If error occurs during transactions `i2cTxRx(int addr, int errorCode)` is sent back to the host.

Parameters:

`int address` - slave device address.

`byte[] wrData` - byte array which will be sent to the slave during MASTER WRITE. If blob size is 0 MASTER WRITE transaction is skipped.

`int rdLen` - number of bytes to read during MASTER READ transaction. If readLength is 0 MASTER READ transaction is not executed.

JSON:
```
{
  "method": "i2cTransfer",
  "params": [<addr>, <wrData>, <rdLen>]
}
```

### i2cStop()

Disables I2C module.

JSON:
```
{
  "method": "i2cStop",
  "params": []
}
```

## System
### registerWrite(int regAddr, int val)

Writes a value directly to the device register located at regAddr address.

`int regAddr` - register address

`int val` - value to write

JSON:
```
{
  "method": "registerWrite",
  "params": [<regAddr>, <val>]
}
```

### registerRead(int regAddr)

Reads a register value. Responds with registerRead(int registerAddress, int registerValue) [101] message.

JSON:
```
{
  "method": "registerRead",
  "params": [<regAddr>]
}
```

### getDeviceInfo()

Returns device information. Responds with getDeviceInfo(int firmwareVersion, byte[] UID, int partNumber, int bootloaderVersion) message.

Return values:

`int firmwareVersion` - [31:24] bits are device (firmware) type and is 0x55 (ASCII 'U') for UPER board, [23:16] bits are firmware major version and [15:0] bits are firmware minor version.

`byte[] UID` - 16 byte long Unique IDentifier, which is read from the device

`int partNumber` - device part number which is read from the device

`int bootloaderVersion` - device bootloader code version which is read from the device

JSON:
```
{
  "method": "getDeviceInfo",
  "params": []
}
```
