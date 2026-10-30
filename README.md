# MIDI Patchbay

A MIDI keyboard, patch bay and monitor that runs in the browser. Connect MIDI devices to your
computer, open the page, press **Enable MIDI**, and route any input to any output.

- **Patch bay:** a grid of every MIDI input (rows) against every output (columns), plus the
  on-screen keyboard as a source and the monitor as a destination. Click a square to connect.
  Each route can filter by input channel, remap to another channel, transpose, and pass or block
  notes, CC, program change, pitch bend, aftertouch, clock/sync and SysEx. Changing a route while
  notes are held never leaves them stuck. These settings live in the **Route settings** list below the
  grid, which starts collapsed.
- **Mappings:** each connection can also turn one value into another, for example note velocity →
  CC 74, CC 21 → CC 1, pitch bend → a CC, or velocity → velocity for a velocity curve. Press
  **Learn** and move a control (or play a note) to pick the source. Each mapping has an input and
  output range for scaling and inverting, and a live readout of the last value. Mapped messages go
  out before the note they came from, so the synth has the value when the note starts. With
  "Also send the original" off, the source message is replaced (on by default for note sources, so
  the notes still play).
- **Keyboard:** mouse, multi-touch or computer keys (`A`–`'`, sharps on `W E T Y U O P`,
  `Z`/`X` octave, `C`/`V` velocity), with channel, velocity, sustain, program change, pitch bend
  and mod wheel. Notes arriving from MIDI inputs light up on it. Eight knobs send CC on the
  keyboard's channel, each with its own CC number: drag, or use the arrow keys, and double-click to
  reset. They play into the patch bay like the keys, so route the keyboard somewhere to hear them.
- **Record audio:** pick an audio input, or tab/system audio, and record it with voice processing
  off, to a 320 kbps MP3 (the default, at 44.1 or 48 kHz) or a 24-bit WAV at the source's sample
  rate. **Arm** shows the level before you start. Takes can be played, saved or deleted; they stay
  in the tab until saved. Tab or system audio needs the browser's sharing prompt.
- **Record video:** a video drawn by the page itself, with no screen sharing: your routes as cables
  that light up and carry pulses as MIDI passes through them, notes rising from a full keyboard
  (orange from the on-screen keyboard, blue from MIDI inputs) and the level, square (1280×1280) or
  vertical 9:16 (1280×2276), with the chosen input as its sound (MP4 where the browser can write it,
  otherwise WebM). The only permission it needs is the input's, which the browser remembers.
- **Normalize to −3 dB:** sets the loudest peak of a recording to −3 dB when it ends. Audio takes
  are scaled before they're written, so nothing is lost. Videos can't be rewritten in the browser,
  so they're recorded with Opus audio (not AAC) and the gain is set in its header. That's instant
  and lossless, but it needs a player that honours the header and can play Opus. Takes below −60 dB
  are left alone.
- **Monitor:** a decoded log of everything routed to it, switched off by default.
- **Panic:** sends All Notes Off and releases sustain on every output.

Routes and keyboard settings are stored in the browser (`localStorage`), keyed by device name, so
they come back when the same devices are plugged in.

## Browser support

Web MIDI works in Chrome, Edge, Opera and Firefox on desktop and Android. Safari and every iOS
browser lack it; there the keyboard and monitor still work. Pages must be served over HTTPS (or
localhost), which GitHub Pages does.

Audio inputs record in every current browser. Sharing tab or system audio depends on the browser and
system: Chrome and Edge offer it, and phones don't. WebM files from the browser have no duration in
their header, so some players can't seek in them until they've been re-saved.

## Running and publishing

Everything is in `index.html`, with no build step and nothing fetched at runtime. Open it from any
static server, for example `python3 -m http.server`. To publish, enable GitHub Pages for the
repository with **Deploy from a branch**, `main`, folder `/ (root)`.

MP3 encoding uses [LAME](https://lame.sourceforge.io/) through
[lamejs](https://github.com/zhuker/lamejs) 1.2.1, LGPL-3.0. Its unmodified `lame.min.js` is embedded
as the `#lame-src` block at the end of `index.html`, and can be replaced there.
