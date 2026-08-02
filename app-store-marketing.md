# MorseReader

## App Store metadata draft

**Subtitle (30 characters maximum)**

```text
Read Morse and ITA2 LEDs
```

**Promotional text (170 characters maximum)**

```text
Turn an iPhone camera into an optical diagnostic receiver for Classic Morse and Manchester-encoded ITA2 transmitted by a blinking status LED.
```

**Keywords (100 characters maximum, comma-separated)**

```text
morse,ITA2,teletype,LED,decoder,embedded,microcontroller,diagnostics,Arduino,ESP32
```

## Read device diagnostics from a blinking LED

MorseReader turns an iPhone camera into an optical diagnostic receiver. Point it at a flashing status LED and translate the signal into readable text—without a serial cable, network connection, radio, or additional receiver hardware.

It is designed for microcontrollers, embedded Linux systems, prototypes, field equipment, and devices where a single LED may be the only available output.

## Two related communication modes

### Classic Morse

Decode traditional Morse code containing letters A–Z and digits 0–9. MorseReader estimates the dot duration automatically, with an optional manual timing override for difficult signals.

### High-Speed Teletype

Decode Manchester-encoded ITA2, the five-bit teleprinter successor to Morse. This mode provides deterministic symbol boundaries and supports the conventional LTRS and FIGS alphabets.

The optical protocol uses:

- IEEE Manchester encoding: `ON→OFF = 0`, `OFF→ON = 1`
- Five ITA2 bits, least-significant bit first
- A guarded `2T OFF / 4T ON / 2T OFF` LTRS marker
- A guarded `2T OFF / 6T ON / 2T OFF` FIGS marker
- Periodic markers for recovery after missed transitions
- No ordinary ITA2 LTRS or FIGS shift characters on the wire

Start with a half-bit period of `T = 100 ms` for reliable setup. A well-lit, stable 60 FPS capture can use `T = 50 ms` for higher speed.

## Built for hardware work

MorseReader is useful for:

- Microcontroller diagnostics
- Arduino, ESP32, RP2040, and similar projects
- Embedded Linux devices and single-board computers
- Boot and power-on self-test messages
- Error codes and operating modes
- Sensor readings and controller state
- Equipment without an accessible debug connector
- Field diagnosis where electrical isolation matters

A device might transmit:

```text
PID1 RUN SP500 PV487 ER13 OUT42
```

The transmitting firmware controls an ordinary LED. MorseReader performs the optical analysis and decoding on the iPhone.

## Simple transmitter pseudocode

```text
T = 100 milliseconds

emit_sync(alphabet):
    LED OFF for 2T
    LED ON for 4T if alphabet is LTRS, otherwise 6T
    LED OFF for 2T

emit_bit(bit):
    if bit is 0: LED ON for T, then OFF for T
    if bit is 1: LED OFF for T, then ON for T

emit_character(ita2_code):
    for bit_number from 0 through 4:
        emit_bit((ita2_code >> bit_number) AND 1)
```

Send a sync marker at startup, whenever the alphabet changes, and periodically—once every eight symbols is recommended.

## Camera-first acquisition

MorseReader can search for a compact flashing light automatically. When several lights or reflections are visible, tap the intended LED for a precise manual selection.

The app displays decoded text, raw symbols or bits, signal quality, selected color channel, timing, and current LTRS/FIGS state. Received text can be saved and shared as a log.

## Private by design

Camera analysis and decoding happen on the device. MorseReader has no cloud decoding service and does not require an account. Diagnostic video is saved only when the user explicitly starts a diagnostic recording.

## Practical limitations

Optical decoding depends on LED brightness, ambient light, focus, exposure, camera stability, transmission speed, and distance. Automatic detection can be confused by displays, reflections, or multiple flashing objects; manual LED selection is available for those situations.

MorseReader is a diagnostic aid. Do not use it as the sole channel for safety-critical control or alarms.
