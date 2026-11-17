# Biometric Streaming

A horror-themed on-screen overlay that shows live biometrics: heart rate, skin conductance (GSR) and EEG alpha. It's a single HTML file with a transparent background, so it can sit on top of video in OBS or any tool that accepts a browser source.

**Status: mockup.** All readings are simulated. No sensor hardware is connected yet.

## What it shows

| Widget | Display | Highlights |
|---|---|---|
| Heart rate | BPM, a heart icon that beats at the current rate, 60 s chart | Panel glows red as BPM rises; glitch effect on a sudden jump |
| Skin response | Conductance in µS, 30 s chart | Rapid rises are shaded and labelled with their size, with a **SPIKE** tag |
| Brain · alpha | Relative alpha as a percentage and a gauge, 30 s chart | Static effect when alpha drops |
| Camera (optional) | Webcam preview | Browser preview only |

The style is dark and moody (film grain, scanlines, typewriter labels) but is built to stay readable over bright footage.

## Simulation

The simulator follows realistic timing for each signal:
- Heart rate updates once a second.
- Skin conductance responds about 1.5–2.5 s after a stimulus, peaks a couple of seconds later and recovers slowly.
- Alpha is noisy and dips briefly after a startle.

A **jump scare** (button or `J`) produces a startle response across all three signals. A **tension** slider raises the baseline levels.

## Privacy

Readings exist only in memory and are discarded as they scroll off the charts. Nothing is recorded, logged or sent anywhere. The only thing remembered between sessions is the widget layout, in the browser's local storage. The camera is a live view only and is never recorded.

## Using it

**Preview:** download `biometric-overlay.html` and open it in a browser. A control panel appears in the corner.

**OBS:**
1. Add a **Browser** source, tick **Local file**, and select `biometric-overlay.html`.
2. Set the size to **1920×1080** for landscape or **1080×1920** for vertical. The overlay switches layout to match.
3. The control panel hides itself inside OBS. Right-click the source → **Interact** to use the keyboard shortcuts.

| Key | Action |
|---|---|
| `J` / Space | Jump scare |
| `A` | Random scares on/off |
| `C` | Camera on/off |
| `V` | Switch between landscape and vertical |
| `E` | Edit layout (drag the widgets) |
| `H` | Hide/show the control panel |

**URL options:**
- `?orientation=vertical` or `?orientation=landscape` forces a layout.
- `?controls=1` or `?controls=0` shows or hides the control panel.

## Connecting real sensors (planned)

Every reading goes through one function, so the simulator can be swapped for live data without touching the display code:

```js
ingest('hr', bpm);              // e.g. a heart-rate strap via Pulsoid
ingest('gsr', microsiemens);    // e.g. a Grove GSR sensor on an Arduino
ingest('alpha', relativeAlpha); // e.g. a Muse headband via Mind Monitor (OSC)
```

Serial and OSC sources need a small local bridge that forwards readings over a WebSocket, because browser sources can't read them directly. Notes are at the bottom of the HTML file.

## Caveats

This is for display and entertainment, not a medical or diagnostic tool. Consumer EEG in particular is noisy: blinks, jaw tension and head movement can look like changes in brain activity.
