# MIDI Patchbay

Client-side Web MIDI tool: patch bay, keyboard and monitor. Published with GitHub Pages from the
root of `main`.

- Everything lives in `index.html` (inline CSS and JS, no dependencies, no build). Keep it that way.
- Serve locally with `python3 -m http.server` from this folder; Web MIDI needs a secure context, and
  localhost counts.
- Routing: `transform()` applies a route's filters. Note-offs are matched to their note-on through
  `heldByRoute`, so channel or transpose changes never strand a note; keep that when changing it.
- Route and keyboard settings persist in `localStorage` under `midi-patchbay:v1`, keyed by port
  name (port ids aren't stable across sessions). Wrap storage access in try/catch.
- Without devices, test by replacing `navigator.requestMIDIAccess` with a mock that returns
  `{inputs, outputs}` Maps before pressing Enable MIDI.
