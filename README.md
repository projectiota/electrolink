## Electrolink

`Electrolink` is a protocol for controlling contrainde MCU over the network.

It defines a set of functions implemented by the MCU firmware and exposed via:
- JSON RPC (in case of MQTT or WS protocols)
- RESTful API (in case of CoAP or HTTP protocols)

Since hosting whole WS or HTTP server would be heavy for controined devices, it is expected that either CoAP or MQTT protocols will be used.

Protocol specification can be found in [electrolinklink-protocol.md](https://github.com/projectiota/electrolink/blob/master/electrolinklink-protocol.md) file

## License
[Apache-2.0](https://github.com/projectiota/electrolink/blob/master/LICENSE)
