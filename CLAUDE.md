# MIDI Patchbay

Client-side Web MIDI tool: patch bay, keyboard and monitor. Published with GitHub Pages from the
root of `main`.

- Everything lives in `index.html` (inline CSS and JS, no build, nothing fetched at runtime). Keep it
  that way. The one embedded dependency is the MP3 encoder: the last ~300 lines are lamejs 1.2.1's
  unmodified `lame.min.js` (LGPL-3.0) in `<script type="text/plain" id="lame-src">`, so read the file
  with a line limit. Don't edit that block; replace it whole to upgrade.
- Serve locally with `python3 -m http.server` from this folder; Web MIDI needs a secure context, and
  localhost counts.
- Routing: `transform()` applies a route's filters. Note-offs are matched to their note-on through
  `heldByRoute`, so channel or transpose changes never strand a note; keep that when changing it.
- Mappings (`cfg.maps`, see `/* mappings */`) run after the route's channel filter and before its
  type filters; their output is pushed ahead of the original message and ignores the type filters.
  Values are in 0-127 (bend is scaled), and swapped range ends invert.
- Route and keyboard settings persist in `localStorage` under `midi-patchbay:v1`, keyed by port
  name (port ids aren't stable across sessions). Wrap storage access in try/catch.
- Without devices, test by replacing `navigator.requestMIDIAccess` with a mock that returns
  `{inputs, outputs}` Maps before pressing Enable MIDI.
- Recorder (see `/* audio recorder */`): the armed graph is source → `rec.bus` → the `tap` worklet
  (meter and WAV samples, source inlined as `TAP_SRC`) and `rec.mix` (the video's sound). It's muted
  on its way to the output, so it can't feed back. Keep voice processing off in the constraints.
  Long takes spill into Blobs (`SPILL_BYTES`); don't hold them as one array.
- MP3 (the default) encodes in a worker (`MP3_WORKER`, `#lame-src`) so it can't disturb MIDI timing.
  `recContextRate()` keeps MP3 at 44.1 or 48 kHz, because lower rates have no 320 kbps and the
  encoder's resampler is uneven; changing format while armed reopens the source for that reason.
- Normalize: audio takes hold raw floats until the stop, then `normalizeTake()` applies the gain and
  writes the format. Videos use Opus (`VIDEO_TYPES_OPUS`), and `setOpusGain()` patches the header's
  output gain in the first chunk: `OpusHead`+16 little-endian in WebM, `dOps`+12 big-endian in MP4.
- Record video (`vid`, `view`): `drawView()` paints a canvas this page owns, and that's why it needs
  no screen sharing, the same way Schwung's mirror records its own canvas. It's 1280 wide in every
  `VIEW_SHAPES` entry (heights must stay even for H.264). A worker keeps the time, because timers and
  animation frames slow down in a background tab. `noteViewRoute()` feeds the cable lights and
  pulses from `dispatch()`, skipping clock so they show real traffic. `buildViewLayout()` is cached
  until `routing` is replaced. Note trails are diffed each frame from `kbHeld` and `inNotes`. Bitrates
  live in `VIEW_SHAPES`; check new visuals still look clean at them.
- Tab or system audio is a source for Record audio only. It's the one thing here that uses
  `getDisplayMedia`.
- Test the recorder by replacing `getUserMedia` and `enumerateDevices` with mocks
  built from oscillators (`createMediaStreamDestination`).
