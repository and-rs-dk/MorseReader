# MorseReader Support

## Quick start

1. Open MorseReader and allow camera access.
2. Select **Classic Morse** or **High-Speed Teletype**.
3. Point the rear camera at the flashing LED.
4. Allow automatic detection to lock onto the light, or tap the LED manually.
5. Keep the camera and transmitter still while receiving.
6. Use **Reset** before starting a new transmission.

## Mode guides

Each mode has its own protocol, timing, transmitter example, and animated test page:

- [Classic Morse protocol and transmitter](support-morse.md)
- [High-Speed Teletype protocol and transmitter](support-ita2.md)

## Troubleshooting

### Nothing is decoded

- Confirm that the transmitter and MorseReader use the same mode.
- Tap the LED manually instead of waiting for automatic detection.
- Move closer and keep the LED away from the edge of the camera view.
- Reduce strong reflections and other flashing lights.
- Keep the phone and transmitting LED stationary.
- Start with the slower timing recommended in the relevant mode guide.

### Characters are wrong or intermittent

- Verify the transmitter timing against the relevant mode guide.
- Use manual LED selection if the selected color channel changes frequently.
- Increase the timing period while diagnosing exposure or camera problems.
- Keep the complete LED brightness range visible without overexposing nearby surfaces.

### Camera permission was denied

Open iOS **Settings → Apps → MorseReader → Camera** and enable camera access.

## Saved logs and diagnostics

**Save Log** writes decoded text and raw symbols to the MorseReader folder in Files under **On My iPhone**.

Diagnostic recordings are created only after explicitly selecting **Record Diagnostic**. They may contain camera video, so review them before sharing. Diagnostic files can be deleted using the Files app.

## Privacy

MorseReader processes camera frames locally. It does not require an account or upload camera data to a decoding service. Saved logs and diagnostic recordings remain under the user’s control unless they choose to share them.

## Contact

When requesting support, include:

- iPhone model and iOS version
- Reader mode
- Dot duration or Manchester half-bit period
- Whether the LED was selected automatically or manually
- A short description of the transmitter hardware
- A diagnostic recording and matching CSV when practical

Do not include credentials, private keys, or confidential device data in a support request.
