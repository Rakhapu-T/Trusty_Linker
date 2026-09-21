# Dshot ESCs
- Dshot is an alternative ESC protocol that has several advantages over PWM or OneShot.
- It has reduced latency.
- It has increased robustness via a checksum.
- No need for ESC calibratoin as the protocol uses digital encoding.
- Telemetry feedback is available/supported on some ESCs.
- Can reverse motor spin directions via commands when needed (rather than physically moving wires/re-soldering).
- Other useful commands are supported.

# Links
1. PX4 reference page: https://docs.px4.io/main/en/peripherals/dshot#esc-telemetry
2. Configuration: https://docs.px4.io/main/en/config/actuators
3. ESC Commands: https://docs.px4.io/main/en/modules/modules_driver#dshot
4. ESC Telemetry: https://docs.px4.io/main/en/peripherals/dshot#esc-telemetry
5. Bidirectional DShot (Telemetry): https://docs.px4.io/main/en/peripherals/dshot#bidirectional-dshot-telemetry
