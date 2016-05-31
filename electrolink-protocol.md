## Function Codes

| SUBSYS              | FNC                                                 | CODE     |
|:--------------------|:----------------------------------------------------|:---------|
| GPIO                | **pinFunction**(int pinId, int pinFnc)              | 0x01     |
|                     | **pinMode**(int pinId, int pinMode)                 | 0x02     |
|                     | **digitalWrite**(int pinId, int value)              | 0x03     |
|                     | **digitalRead**(int pinId)                          | 0x04     |
| INT                 | **attachInterrupt**(int intId, int pinId, int mode) | 0x05     |
|                     | detachInterrupt(int intId)                          | 0x06     |
|                     | pinFunction(int pinId, int pinFnc)                  | 0x07     |
|                     | pinFunction(int pinId, int pinFnc)                  | 0x08     |


## GPIO

### `[0x01]` pinFunction(int pinId, int pinFnc)

Configures pin `pinId` to primary or secondary function. Secondary pin function should be enabled before calling any of the secondary functions (e.g. analog, pwm, spi, i2c).

Parameters:

`int pinId` - pin number (0-33)
`int pinFnc` - function number

By default GPIO is always the primary function. Some pins don't have secondary functions - that is their secondary function is also GPIO.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "pinFunction",
  "params": [<pinId>, <pinFnc>],
  "id": <msgId>
}
```

### `[0x02]` pinMode(int pinId, int pinMode)
Configures GPIO pin mode. Pin should be set to GPIO function before configuring its GPIO mode.

Parameters:

`int pinId` - pin number (0-33)

`int pinMode` - pin mode: 0 - input (high Z), 1 - output, 2 - input (pull down), 4 - input (pull up)

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "pinMode",
  "params": [<pinId>, <pinMode>],
  "id": <msgId>
}
```

### `[0x03]`digitalWrite(int pinId, int value)
Sets GPIO output high or low.

Parameters:

`int pinId` - pin number (0-33)

`int value` - digital output value: 0 - LOW, 1 - HIGH

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "digitalWrite",
  "params": [<pinId>, <value>],
  "id": <UUID>
}
```

### `[0x04]` digitalRead (int pinId)
Reads digital state of the GPIO pin.

Parameters:

`int pinId` - pin number (0-33)

Returns message `digitalRead(int pinId, int value)`, where the value is 0 (LOW state) or 1 (HIGH state)

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<pinId>],
  "id": <msgId>
}
```

### `[0x05]` attachInterrupt(int intId, int pinId, int mode)
Attaches GPIO interrupt service to the specified pin. There is a total of 8 interrupts which can be attached to any pin. Interrupts can be configured to trigger at HIGH or LOW state or at RISING, FALLING or both edges. When the interrupt is triggered, device sends message `interrupt(int interruptId, int event)`, where event has the same meaning as mode.

Parameters:

`int intId` - interrupt number (0-7)

`int pinId` - pin number (0-33)

`int mode` - interrupt trigger mode: 0-LEVEL LOW, 1-LEVEL HIGH, 2-EDGE CHANGE, 3-EDGE RISE, 4-EDGE FALL.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "digitalRead",
  "params": [<intId>, <pinId>, <mode>],
  "id": <msgId>
}
```

### `[0x06]` detachInterrupt(int intId)
Detaches (disables) specified GPIO interrupt.

Parameters:

`int intId` - interrupt number (0-7)

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "detachInterrupt",
  "params": [<intId>],
  "id": <msgId>
}
```

## Analog (ADC)
### analogRead(int analogPinID)

Parameters:

`int analogPinID` - analog pin number (0-7), corresponds to ADC0-ADC7 pins.

Returns message where value is 10 bit ADC read value.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "analogRead",
  "params": [<analogPinID>],
  "id": <msgId>
}
```

## pulseIn (int pinId, int level, int timeout)

Measures pulse duration on a pin. Is compatible with Arduino command pulseIn (http://arduino.cc/en/Reference/PulseIn).

Parameters:

`int pinId` - pin number (0-33)

`int level` - level of the pulse (0 - LOW level, 1 - HIGH)

`int timeout` - number of microseconds to wait until timeout

Returns message pulseIn(int pulseWidth) [09], where the pulseWidth is 0 if no pulse was detected during timeout period or pulse duration in microseconds.

JSON:
```
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
```
{
  "jsonrpc": "2.0",
  "method": "pwmStart",
  "params": [<pwmNb>, <period>],
  "id": <msgId>
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
  "jsonrpc": "2.0",
  "method": "pwmSet",
  "params": [<pwmNb>, <channlel>, <highTime>],
  "id": <msgId>
}
```

### pwmStop(int pwmNb)

Stops the PWM module.

Parameters:

`int pwmNb` - PWM module selction

JSON:
```
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

### spiTransfer(int spiNb, byte[] data, int rsp)

Executes SPI transaction. User should manually (using GPIO functions) drive slave select line low when executing the transaction. Depending on respond value, the device can send back the data that it received from the slave by sending message `spiTxRx(byte[] slaveData)`.

Parameters:

`int spiNb` - SPI module selection

`byte[] data` - bytes which will be sent to the slave.

`int rsp` - value which tells if device should send back slave data: 0 - do not send, 1 - send back slave data.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "spiTransfer",
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
### i2cStart()

Initializes I2C module.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "i2cStart",
  "params": [],
  "id": <msgId>
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
  "jsonrpc": "2.0",
  "method": "i2cTransfer",
  "params": [<addr>, <wrData>, <rdLen>],
  "id": <msgId>
}
```

### i2cStop()

Disables I2C module.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "i2cStop",
  "params": [],
  "id": <msgId>
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
  "jsonrpc": "2.0",
  "method": "registerWrite",
  "params": [<regAddr>, <val>],
  "id": <msgId>
}
```

### registerRead(int regAddr)

Reads a register value. Responds with registerRead(int registerAddress, int registerValue) [101] message.

JSON:
```
{
  "jsonrpc": "2.0",
  "method": "registerRead",
  "params": [<regAddr>],
  "id": <msgId>
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
  "jsonrpc": "2.0",
  "method": "regRead",
  "params": [],
  "id": <msgId>
}
```
