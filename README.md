# ferry

Run Windows games on Apple Silicon Macs. Free and open source.

Very early. Nothing runs yet. Right now this repo is the plan, and the code comes next.

## What it is

The idea is one Mac app that runs Windows games without any setup. Inside it there's a Wine fork, DXMT for D3D11 games, and a new D3D12 layer that translates straight to Metal 4.

That D3D12 layer is the reason this project exists. DX12 on the Mac today mostly means Apple's closed D3DMetal, and there isn't an open-source equivalent that holds up. The plan is to write one the way DXMT does D3D11: Windows-side DLLs plus a macOS library that drives Metal directly, with no Vulkan in between.

Things it should get right:

- one app: download it, drag it to Applications, install Steam, play
- less shader stutter on first run, by precompiling pipelines in the background
- sensible memory use on 16 GB machines, since the GPU shares that memory
- steady frame pacing on 60 Hz screens
- a governor that trades frame rate against heat on fanless Macs, so a game holds a steady rate instead of throttling
- MetalFX behind games that use DLSS, FSR 3.1 or XeSS

Every change gets benchmarked, against Apple's D3DMetal and against CrossOver.

Not planned: games that need kernel anti-cheat, anything to do with DRM, and Intel Macs.

## Requirements (planned)

Apple Silicon (M1 or newer) and macOS 26 or newer. Development and tuning happen on a MacBook Air M5 (8-core GPU, 16 GB), so that's the reference machine for numbers.

## Reverse engineering

Most of what's needed is public: the D3D12 spec, DXIL docs, and the source of Wine, vkd3d-proton, DXVK, DXMT and MoltenVK. For the rest:

- Microsoft's runtime DLLs and game binaries can be studied where the law allows it for interoperability, to learn behavior. Findings get written up as notes and test programs, and code is written from those.
- D3DMetal and other closed Mac translation layers are black-box only. That means frame captures, traces and comparing output. No decompiling, and no code from them.
- No work on DRM or anti-cheat.

## Plan

See [PLAN.md](PLAN.md). It's split into phases A to I, each with steps and a measurable gate.

## Contributing

Help is welcome, even this early. [CONTRIBUTING.md](CONTRIBUTING.md) lists what's useful right now and the rules on code provenance. Questions and ideas go in [Discussions](https://github.com/NandishwarSingh/ferry/discussions).

## License

LGPL-2.1-or-later, same as Wine, vkd3d-proton and DXMT, so code can move between them. See [LICENSE](LICENSE).

Not affiliated with Apple, Microsoft, CodeWeavers or Valve. DirectX, Metal, D3DMetal and CrossOver are trademarks of their owners.
