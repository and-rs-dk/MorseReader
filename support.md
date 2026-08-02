# MorseReader Support

## Quick start

1. Open MorseReader and allow camera access.
2. Select **Classic Morse** or **High-Speed Teletype**.
3. Point the rear camera at the flashing LED.
4. Allow automatic detection to lock onto the light, or tap the LED manually.
5. Keep the camera and transmitter still while receiving.
6. Use **Reset** before starting a new transmission.

For initial High-Speed Teletype tests, use `T = 100 ms`. After reliable decoding is established, try `T = 75 ms` and then `T = 50 ms` with 60 FPS capture.

## Troubleshooting

### Nothing is decoded

- Confirm the correct reader mode is selected.
- Tap the LED manually instead of waiting for automatic detection.
- Move closer and keep the LED away from the edge of the camera view.
- Reduce strong reflections and other flashing lights.
- Increase the transmitter timing to `T = 100 ms`.
- In High-Speed Teletype mode, transmit a valid sync marker before data.
- Ensure the transmitter uses IEEE Manchester polarity: `ON→OFF = 0`, `OFF→ON = 1`.

### LTRS and FIGS change unexpectedly

- Place a `2T OFF` guard before every sync marker.
- Use exactly `4T ON` for LTRS and `6T ON` for FIGS.
- Follow the marker with `2T OFF` before the first data half-bit.
- Send another sync marker whenever the alphabet changes.
- Resynchronize periodically; every eight symbols is recommended.
- Use manual LED selection if the signal-quality color indicator changes frequently.

### Characters are wrong or intermittent

- Verify that ITA2 bits are transmitted least-significant bit first.
- Do not transmit ordinary ITA2 shift codes 27 or 31. Use sync markers instead.
- Confirm that every Manchester bit contains two halves of equal duration.
- Avoid using a half-bit shorter than 50 ms with a 60 FPS camera.
- Try 100 ms while diagnosing timing or exposure problems.
- Keep the phone and transmitting LED stationary.

### Classic Morse starts with incorrect timing

The first complete pulse is initially treated as a dot because a single pulse cannot distinguish a slow dot from a fast dash. If capture starts in the middle of a dash, enter the known dot duration manually and press **Apply**.

### Camera permission was denied

Open iOS **Settings → Apps → MorseReader → Camera** and enable camera access.

## High-Speed Teletype protocol

Let `T` be the Manchester half-bit period.

```text
LTRS sync:  OFF 2T | ON 4T | OFF 2T
FIGS sync:  OFF 2T | ON 6T | OFF 2T

data bit 0: ON T   | OFF T
data bit 1: OFF T  | ON T
```

After a sync marker:

1. Reset the five-bit symbol boundary.
2. Select the LTRS or FIGS table indicated by the marker.
3. Transmit five-bit ITA2 values least-significant bit first.
4. Do not send the normal ITA2 LTRS or FIGS shift values.

Recommended marker placement:

- At the beginning of every transmission
- Whenever the alphabet changes
- After approximately eight data symbols

Example stream:

```text
LTRS-SYNC  P I D
FIGS-SYNC  1 SPACE
LTRS-SYNC  R U N
```

## Python transmitter example

Replace `set_led()` with the GPIO operation appropriate for the device.

```python
import time

T = 0.100

def set_led(on: bool) -> None:
    # Example: gpio.write(LED_PIN, 1 if on else 0)
    pass

def level(on: bool, duration: float) -> None:
    set_led(on)
    time.sleep(duration)

def sync(figures: bool) -> None:
    level(False, 2 * T)
    level(True, (6 if figures else 4) * T)
    level(False, 2 * T)

def bit(value: int) -> None:
    # IEEE Manchester: 0 = ON/OFF, 1 = OFF/ON
    level(value == 0, T)
    level(value == 1, T)

def ita2(code: int) -> None:
    for bit_number in range(5):
        bit((code >> bit_number) & 1)

# LTRS P, I, D: 0x16, 0x06, 0x09
sync(figures=False)
for code in (0x16, 0x06, 0x09):
    ita2(code)

set_led(False)
```

## Small C transmitter example

The example assumes `led_write()` and `delay_ms()` are supplied by the platform.

```c
#include <stdbool.h>
#include <stdint.h>

enum { T_MS = 100 };

void led_write(bool on);
void delay_ms(uint32_t milliseconds);

static void level(bool on, uint32_t duration_ms) {
    led_write(on);
    delay_ms(duration_ms);
}

static void ita2_sync(bool figures) {
    level(false, 2 * T_MS);
    level(true, (figures ? 6 : 4) * T_MS);
    level(false, 2 * T_MS);
}

static void manchester_bit(bool bit) {
    level(!bit, T_MS);  /* 0 starts ON; 1 starts OFF */
    level(bit, T_MS);
}

static void ita2_send(uint8_t code) {
    for (unsigned bit = 0; bit < 5; ++bit) {
        manchester_bit((code >> bit) & 1u);
    }
}

void send_pid(void) {
    ita2_sync(false);   /* Select LTRS and reset framing. */
    ita2_send(0x16);    /* P */
    ita2_send(0x06);    /* I */
    ita2_send(0x09);    /* D */
    led_write(false);
}
```

## Classic Morse timing

For a dot duration `D`:

```text
dot:               ON D
dash:              ON 3D
within-character:  OFF D
between letters:   OFF 3D
between words:     OFF 7D
```

## Saved logs and diagnostics

**Save Log** writes decoded text and raw symbols to the MorseReader folder in Files under **On My iPhone**.

Diagnostic recordings are created only after explicitly selecting **Record Diagnostic**. They may contain camera video, so review them before sharing. Diagnostic files can be deleted using the Files app.

## Privacy

MorseReader processes camera frames locally. It does not require an account or upload camera data to a decoding service. Saved logs and diagnostic recordings remain under the user’s control on the device unless they choose to share them.

## Contact

When requesting support, include:

- iPhone model and iOS version
- Reader mode
- Dot duration or Manchester half-bit period
- Whether the LED was selected automatically or manually
- A short description of the transmitter hardware
- A diagnostic recording and matching CSV when practical

Do not include credentials, private keys, or confidential device data in a support request.
