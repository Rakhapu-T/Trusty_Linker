# Overview
- Enabled firmware for the ESP32 modules, and this is probably the cheapest way to communicate with a drone & ground station.
- Allows for fully transparent serial to wifi pass-through with variable packet size.
- There exits DroneBridge for both ESP32 and Raspberry pi (Note: These are different projects)
- Drone Bridge for ESP32:
	  - Easier to setup more robust and polished (than raspberry pi equivalent).
	  - Power output is limited to 20dBm by the hardware (Follows Wi-Fi standard).
	  - Currently does not support video or radio control!
	  - Range is limited to ~1km using ESP-NOW.

![[DroneBridge_ESP_Overview.png]]

# Features (ESP32)
- Bidirectionall
	  - serial-to-WiFi
	  - serial-to-ESP-NOW link
	  - serial-to-BLE in release v2.2+
- Support for MAVLink, MSP, LTM or any other payload using the transparent option
- Affordable
- up to 150m+ range using standard WiFi
- Up to 1km of range using ESP-NOW or Wi-Fi LR Module - sender & reciever must be ESP32 with LR-Mode enabled.
- Fully encrypted in all modes, including ESP-NOW broadcasts secured using AES-GCM 256-bit!
- Weight: < 8g
- Supported by: **QGroundControl**, Mission Planner, mwptools, impload etc.
- Easy to set up: Power connection + UART connection to flight controller.
- Fully configurable through an easy to use web interface
- Parsing of LTM & MSPv2 for a more reliable connection and less packet loss.
- Parsing of MAVLink with the injection of Radio Status packets for the display of RSSI in the GCS
- Fully transparent telemetry downlink option for continuous streams.
- Reliable, low-latency
- UDP & TCP connections are possible. Automatic UDP unicast of messages to port 14550 to all connnected devices/stations. Allows additional clients to register for UDP. The client must send a packet with a length > 0 to the UDP port of ESP32.

# User Interface (Typical):
![[DroneBridge_UI.png]]

# Quickstart
Link: https://dronebridge.gitbook.io/docs/dronebridge-for-esp32/quick-start