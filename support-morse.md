# Classic Morse — Protocol and Transmitter

[Back to MorseReader Support](support.md)

Classic Morse supports letters A–Z, digits 0–9, and spaces. MorseReader estimates the dot duration automatically; a known dot duration can also be entered manually in milliseconds.

## Timing

Let `D` be one dot period:

```text
dot:                    ON  D
dash:                   ON  3D
between marks:          OFF D
between letters:        OFF 3D
between words:          OFF 7D
```

The letter `A` (`.-`) is therefore:

```text
ON D | OFF D | ON 3D
```

Start with `D = 100 ms`. Slower periods are useful for initial setup or difficult optical conditions. The transmitter must not add an intra-character gap after the final mark; the next gap is the complete 3D letter gap or 7D word gap.

If reception begins in the middle of a dash, automatic timing may initially treat it as a dot because one isolated pulse cannot reveal whether it is `D` or `3D`. Enter the known dot duration and press **Apply**, or begin with a complete repeated message.

## Python transmitter example

Replace `set_led()` with the GPIO operation appropriate for the device.

```python
import time

D = 0.100
ALPHABET = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
CODES = """
.- -... -.-. -.. . ..-. --. .... .. .--- -.- .-.. -- -.
--- .--. --.- .-. ... - ..- ...- .-- -..- -.-- --..
----- .---- ..--- ...-- ....- ..... -.... --... ---.. ----.
""".split()
MORSE = dict(zip(ALPHABET, CODES))

def set_led(on: bool) -> None:
    # Example: gpio.write(LED_PIN, 1 if on else 0)
    pass

def level(on: bool, duration: float) -> None:
    set_led(on)
    time.sleep(duration)

def send(message: str) -> None:
    words = message.upper().split()
    for word_number, word in enumerate(words):
        for letter_number, letter in enumerate(word):
            code = MORSE[letter]
            for mark_number, mark in enumerate(code):
                level(True, D if mark == "." else 3 * D)
                if mark_number + 1 < len(code):
                    level(False, D)
            if letter_number + 1 < len(word):
                level(False, 3 * D)
        if word_number + 1 < len(words):
            level(False, 7 * D)
    set_led(False)

send("HELLO 123")
```

## Animated example

Open [the Classic Morse test page](test-morse.html) to view and test the accompanying SVG LED animation.

## Reception tips

- Begin a repeated transmission with a complete dot when possible.
- Keep dot, dash, letter-gap, and word-gap timing proportional to the same `D`.
- Increase `D` if camera exposure, motion, or distance makes pulses intermittent.
- Tap the intended LED if automatic selection chooses a reflection or another light.
