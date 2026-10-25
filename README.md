Abstract

Water management is an important challenge in field irrigation, where fixed watering schedules can waste water or leave crops under-irrigated as soil conditions change. This report presents the design of a prototype Long-Range Field Irrigation Network combining an ESP32 field node, capacitive soil-moisture sensor, SX1278-based LoRa radio link, ESP32 gateway, relay-switched 6 V DC mini pump, and Blynk IoT dashboard. The field node periodically samples the sensor and transmits a moisture estimate over a sub-GHz LoRa link. The gateway validates received packets, applies threshold-based automatic control with hysteresis, switches the pump through a relay interface, and publishes measurements and status to Blynk over Wi-Fi. The architecture separates local sensing and radio communication from cloud connectivity and actuation, allowing the field node to operate without a direct Wi-Fi connection. This paper documents the architecture, interfaces, calibration equations, control logic, test methodology, performance metrics, safety considerations, and limitations. No measured radio range, water-saving percentage, response time, or accuracy is claimed because controlled experimental measurements have not been supplied. A repeatable evaluation plan and blank result tables are included.

Keywords—smart irrigation, ESP32, LoRa, SX1278, soil-moisture sensing, IoT, Blynk, relay control.

I. Introduction

Irrigation decisions are often made from fixed schedules or visual inspection. These approaches may be inefficient when soil moisture varies with rainfall, soil texture, crop stage, shade, and drainage. A sensor-assisted system observes field conditions and uses a controller to run a pump when the measured condition indicates a need for irrigation.

Wireless sensor networks can monitor remote plots without long sensor cables. Wi-Fi is useful at a gateway with internet access, but it may not cover a distant field node. LoRa modulation supports low-data-rate, long-range telemetry and is appropriate for small packets such as a moisture estimate. Actual range depends on antenna design, local regulations, terrain, vegetation, interference, radio settings, and line of sight; it must be measured rather than assumed.

The proposed prototype uses two ESP32 boards. ESP32-1 reads a capacitive soil-moisture sensor and transmits a numeric estimate through an SX1278 RA-02 module. ESP32-2 receives packets, decides whether the pump should run, controls a relay, and communicates with Blynk over Wi-Fi. The pump is powered by a separate regulated 6 V supply rather than an ESP32 GPIO.

A. Objectives

• Read and calibrate soil-moisture sensor output.

• Transmit field readings to a gateway using point-to-point LoRa.

• Implement automatic irrigation using two thresholds to reduce relay chatter.

• Provide manual control, mode selection, moisture display, pump status, and RSSI through Blynk.

• Use a separate pump supply and correctly rated relay contacts.

• Define reproducible tests without inventing results.

B. Scope and Contributions

II. Technical Background and Related Work

Sensor-based irrigation systems generally combine a sensor, embedded controller, wireless link, and actuator. For low-data-rate telemetry, dependable delivery and safe behavior when packets are missing are more important than throughput. LoRa uses chirp spread-spectrum modulation and permits trade-offs among data rate, robustness, and airtime through spreading factor, bandwidth, and coding rate. A larger spreading factor can improve link robustness but increases airtime.
This project uses point-to-point LoRa radio packets, not LoRaWAN. LoRaWAN additionally requires a protocol stack and network infrastructure. The distinction matters because a pair of SX1278 radios configured with a LoRa library does not, by itself, form a LoRaWAN network.



The contribution is a modular integration of sensing, LoRa telemetry, local actuation, and cloud monitoring. The field node does not require Wi-Fi. The gateway can make local control decisions from received data. The current demonstration packet is a decimal moisture value; it does not include encryption, authentication, sequence counters, flow feedback, or industrial safety certification. These limitations are stated explicitly.


