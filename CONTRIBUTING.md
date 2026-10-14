# Contributing

ferry is at the very start (Phase A in [PLAN.md](PLAN.md)), so there's no code to build yet. Help is still useful though, and here's where.

## Where help is useful right now

- Licensing. If you know how Apple's Metal Shader Converter or Game Porting Toolkit licenses apply to an open-source project, start a discussion. Step A1 depends on it.
- D3D12 or Metal knowledge. Notes on how a D3D12 feature maps to Metal 4 (descriptor heaps, barriers, tiled resources, geometry shaders, anything) save a lot of time later. Post them in Discussions or send a PR under `docs/`.
- Test games. Suggestions for DX12 games that are DRM-free (GOG ideally), have a built-in benchmark and don't use anti-cheat.
- Benchmarking on fanless Macs. Ideas for a thermal protocol that gives repeatable numbers.

Once Phase C starts, each Microsoft D3D12 sample becomes its own issue with a clear "done when", and those will be the easiest way in.

## How the repo will be laid out

```
layer/     D3D12 and DXGI translation layer (Windows DLLs + macOS library)
app/       SwiftUI launcher
runtime/   Wine fork patches and DXMT bundling
bench/     benchmark runner, thermal protocol, results
docs/re/   reverse-engineering notes, with where each finding came from
tests/
```

Each part should build on its own, so you won't need the whole graphics stack to work on the app.

## Rules that matter

Code provenance. ferry is LGPL-2.1-or-later and it has to stay clean:

- Don't submit code copied or derived from decompiled closed binaries. That covers D3DMetal, the closed parts of CrossOver and Microsoft's DLLs.
- If you learned something by studying a binary (where your local law allows it for interoperability), write it up as a note in `docs/re/` or as a test, and say where it came from. Code gets written from the note.
- No DRM or anti-cheat work, in any form.
- Code from Wine, vkd3d-proton, DXMT or other LGPL-compatible projects is fine. Keep their copyright headers and mention the source in the PR.

Sign off your commits with `git commit -s`. That adds a `Signed-off-by` line saying you have the right to submit the code under the project license ([Developer Certificate of Origin](https://developercertificate.org)).

Performance claims need numbers. If a PR makes something faster, include before/after numbers, the game or test, the Mac model and how you ran it.

## Pull requests

- One logical change per PR, small enough to review.
- Say what it fixes, why, and how you tested it.
- Keep unrelated formatting changes out.

I try to reply to every issue and PR within a few days.

## Questions

Use [Discussions](https://github.com/NandishwarSingh/ferry/discussions) for questions and ideas. Issues are for bugs and tracked work.
