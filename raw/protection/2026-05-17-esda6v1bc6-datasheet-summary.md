---
source: raw/esda6v1bc6.pdf
collected: 2026-05-17
published: 2004-11-04
---

# ESDA6V1BC6 Datasheet Summary

## Description
Monolithic array designed to protect up to 4 lines in a bidirectional way against ESD transients. Particularly adapted to the protection of symmetrical signals.

## Features
- 4 Bidirectional Transil functions.
- Stand-off voltage range: ± 5V.
- Peak pulse power (8/20µs): 80W.
- Low leakage current: < 1µA.
- Package: SOT23-6L.

## Electrical Characteristics (Tamb = 25°C)
- **VBR (Breakdown Voltage):** Min 6.1V, Max 8V (@ IR = 1mA).
- **VCL (Clamping Voltage):** Max 1.35V (@ IPP = 3A, tp = 2.5µs).
- **C (Capacitance):** Typ 20pF (@ 0V bias, 1MHz).

## Pinout (SOT23-6L)
1. I/O 1
2. GND
3. I/O 2
4. I/O 3
5. GND
6. I/O 4

## Layout Guidelines
- Place as near as possible to the input terminals or connectors.
- Minimize path length between ESD suppressor and protected device.
- Minimize conductive loops.
- Return path to ground should be as short as possible.
