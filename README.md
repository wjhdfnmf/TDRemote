# TDRemote

An audio-reactive **TouchDesigner** visualizer ("RGB Visuals") that you play live from a phone or tablet with **TouchOSC**. Every fader, button and XY pad on the controller sends an OSC message to TouchDesigner, which switches scenes, shapes the audio EQ, drives the visuals, and saves or loads presets. TouchDesigner also sends the current state back, so the controller always shows the right values after a scene change or preset load.

<p align="center">
  <img src="images/IMG_6335.PNG" width="18%" alt="Main tab">
  <img src="images/IMG_6336.PNG" width="18%" alt="OSC tab">
  <img src="images/IMG_6337.PNG" width="18%" alt="Audio mapping tab">
  <img src="images/IMG_6339.PNG" width="18%" alt="EQ tab">
  <img src="images/IMG_6340.PNG" width="18%" alt="Settings tab">
</p>

## Files

| File | What it is |
|---|---|
| `visualizer2.290.toe` | The TouchDesigner project: visual scenes, audio input/output, 16-band EQ, logo and movie layers |
| `touchOSC.tosc` | The TouchOSC controller layout (five tabs, shown above) |
| `dat_oscin1_callbacks.py` | Callbacks for the OSC In DAT: handles every incoming control message, plus preset save/load and syncing state back to the controller |
| `dat_execute1.py` | Execute DAT: resets one-shot switches (scene fade, load triggers, refresh) at the end of each frame |

The two `.py` files are already inside the `.toe` project. They're also kept here as plain files so they're easy to read and diff.

## Setup

1. **TouchDesigner:** open `visualizer2.290.toe`.
   - Choose your audio input and output devices.
   - Set the OSC In port and the OSC Out IP/port to match your phone or tablet.
2. **TouchOSC:** open `touchOSC.tosc` on the phone or tablet.
   - Add a connection with the computer's IP and the OSC In port from step 1.
   - Set the receive port to the OSC Out port from step 1.
3. Both devices must be on the same network. On the controller's settings tab, press **Refresh Controller** to pull the current state from TouchDesigner.

## Controller tabs

### Main
- **Scene select:** pick a scene directly, or step through scenes with ▲ / ▼. When autoload is on, the outgoing scene's settings are saved and the new scene's settings are restored.
- **Profiles:** five preset slots per scene. Slot 0 is the default and can only be written while **Overwrite Defaults** is on. **Save** stores the current scene's faders, XY and modes in the selected slot, and **Load** restores them.
- **Faders 1–4, XY pad and mode buttons:** the live parameters each scene reacts to. Mode 6 is **dance mode**, which locks out the other controls while it's on.
- **Gain, Clip and Show Clip / Show Logo:** input gain, which movie clip plays, and the clip and logo overlays.

### OSC (LFOs)
Five LFOs, one each for Fader 1–4 and the XY pad, each with its own **Max**, **Min** and **Rate**. The grid below picks the waveform/state for each LFO.

### 1 (audio mapping)
Live meters of the audio analysis channels: `signalAvg`, `signalHigh`, `signalMid`, `signalLow`, `freqHigh`, `freqLow`, `volume` and `volNormal`. The grids under them choose which channel modulates each of the five LFO slots (map A and map B).

### EQ
- **Volume, Dry/Wet and Mute** for the audio output.
- **16-band graphic EQ** from 25 Hz to 22 kHz.
- **Bass ±, Mids ± and Treble ±** shift whole groups of bands at once (bands 1–5, 6–11 and 12–16).
- **Center EQ** recenters the curve, and **Shift EQ Down** sets the step size for the bass/mids/treble shifts.
- The step buttons set the EQ power range.

### 2 (settings)
- **Refresh Controller** resyncs every control from TouchDesigner.
- **Overwrite Defaults**, **Disable Autoload** and **Debug** (prints each incoming OSC address in the TouchDesigner textport).
- **Oscillator:** enable the LFOs, include LFO settings in scene save/load, or reset them.
- **Output resolution:** 4K, 2K, QHD, HD, 2S (1440×1440) and HDS (1080×1080). The logo is repositioned to suit each one.
- **Perform Window** opens the output window, and the selector below it chooses the monitor.

## OSC address reference

| Address | Effect |
|---|---|
| `/up`, `/down`, `/radio` | Scene step / direct scene select |
| `/fader1`–`/fader4`, `/xy` | Live scene parameters |
| `/mode1`–`/mode6` | Mode toggles (`/mode6` = dance mode) |
| `/gain`, `/clip`, `/showclip`, `/showlogo` | Input gain, clip select, overlays |
| `/profile`, `/save`, `/load` | Preset slot, save, load |
| `/eq_grid/1`–`/eq_grid/16` | Individual EQ bands |
| `/eq_volume`, `/eq_drywet`, `/eq_mute`, `/eq_power` | Output volume, dry/wet, mute, EQ range |
| `/eq_bass+`, `/eq_bass-`, `/eq_mids+`, `/eq_mids-`, `/eq_treble+`, `/eq_treble-`, `/eq_center`, `/eq_shiftdown` | EQ group shifts and recenter |
| `/osc_param1`–`/osc_param3` (`/1`–`/5`) | LFO rate, min and max for each of the five slots |
| `/osc_state1`–`/osc_state5` (`/n`) | LFO waveform/state per slot |
| `/osc_mapA_1`–`/osc_mapA_5`, `/osc_mapB_1`–`/osc_mapB_5` | Audio channel mapping per LFO slot |
| `/enable_oscillator`, `/saveload_oscillator`, `/reset_oscillator` | LFO enable, include in presets, reset |
| `/res_4K`, `/res_2K`, `/res_QD`, `/res_HD`, `/res_2S`, `/res_HS` | Output resolution |
| `/overwrite_defaults`, `/disable_autoload`, `/debug`, `/monitor`, `/perform`, `/refresh_controller` | Settings |

## License

See [LICENCE](LICENCE).
