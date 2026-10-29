# MIDI Patchbay

A MIDI keyboard, patch bay and monitor that runs in the browser. Connect MIDI devices to your
computer, open the page, press **Enable MIDI**, and route any input to any output.

- **Patch bay:** a grid of every MIDI input (rows) against every output (columns), plus the
  on-screen keyboard as a source and the monitor as a destination. Click a square to connect.
  Each route can filter by input channel, remap to another channel, transpose, and pass or block
  notes, CC, program change, pitch bend, aftertouch, clock/sync and SysEx. Changing a route while
  notes are held never leaves them stuck.
- **Mappings:** each connection can also turn one value into another, for example note velocity →
  CC 74, CC 21 → CC 1, pitch bend → a CC, or velocity → velocity for a velocity curve. Press
  **Learn** and move a control (or play a note) to pick the source. Each mapping has an input and
  output range for scaling and inverting, and a live readout of the last value. Mapped messages go
  out before the note they came from, so the synth has the value when the note starts. With
  "Also send the original" off, the source message is replaced (on by default for note sources, so
  the notes still play).
- **Keyboard:** mouse, multi-touch or computer keys (`A`–`'`, sharps on `W E T Y U O P`,
  `Z`/`X` octave, `C`/`V` velocity), with channel, velocity, sustain, program change, pitch bend
  and mod wheel. Notes arriving from MIDI inputs light up on it.
- **Monitor:** a decoded log of everything routed to it, switched off by default.
- **Panic:** sends All Notes Off and releases sustain on every output.

Routes and keyboard settings are stored in the browser (`localStorage`), keyed by device name, so
they come back when the same devices are plugged in.

## Browser support

Web MIDI works in Chrome, Edge, Opera and Firefox on desktop and Android. Safari and every iOS
browser lack it; there the keyboard and monitor still work. Pages must be served over HTTPS (or
localhost), which GitHub Pages does.

## Running and publishing

Everything is in `index.html`, with no build step or dependencies. Open it from any static server,
for example `python3 -m http.server`. To publish, enable GitHub Pages for the repository with
**Deploy from a branch**, `main`, folder `/ (root)`.
