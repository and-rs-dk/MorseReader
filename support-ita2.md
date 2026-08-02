# High-Speed Teletype — Protocol and Transmitter

[Back to MorseReader Support](support.md)

High-Speed Teletype uses Manchester-encoded five-bit ITA2. Let `T` be one Manchester half-bit period.

## Timing and framing

```text
LTRS sync:  OFF 2T | ON 4T | OFF 2T
FIGS sync:  OFF 2T | ON 6T | OFF 2T

data bit 0: ON T   | OFF T
data bit 1: OFF T  | ON T
```

After a sync marker:

1. Reset the five-bit symbol boundary.
2. Select the LTRS or FIGS table indicated by the marker.
3. Transmit each five-bit ITA2 value least-significant bit first.
4. Do not transmit ordinary ITA2 shift values 27 or 31.

Send a marker at the beginning, whenever the alphabet changes, and periodically—after approximately eight symbols is recommended.

```text
LTRS-SYNC  P I D
FIGS-SYNC  1 SPACE
LTRS-SYNC  R U N
```

Start with `T = 100 ms`. After reliable decoding is established, try `T = 75 ms` and then `T = 50 ms` with 60 FPS capture. Avoid periods below 50 ms.

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

def manchester_bit(bit: int) -> None:
    # IEEE Manchester: 0 = ON/OFF, 1 = OFF/ON
    level(bit == 0, T)
    level(bit == 1, T)

def ita2(code: int) -> None:
    for bit_number in range(5):
        manchester_bit((code >> bit_number) & 1)

# LTRS P, I, D: 0x16, 0x06, 0x09
sync(figures=False)
for code in (0x16, 0x06, 0x09):
    ita2(code)
set_led(False)
```

## Animated example

Open [the Manchester/ITA2 test page](test-ita2.html) to view and test the accompanying SVG LED animation.

## Reception tips

- Confirm IEEE Manchester polarity: `ON→OFF = 0`, `OFF→ON = 1`.
- Keep both halves of every data bit the same duration.
- Always include the complete 2T OFF guard on both sides of a sync marker.
- Use a new marker for every alphabet change and periodically for recovery.
- Tap the intended LED if the selected color channel changes frequently.
