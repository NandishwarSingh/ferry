# Plan

Week numbers assume about 20 hours a week. Each phase ends with a gate that has to be met before moving on, and every gate is something measurable.

## Overview

| Phase | Name | Weeks | Gate to move on |
|---|---|---|---|
| A | Foundation and go/no-go | 1-3 | Shader converter license decided, Wine + DXMT runs a game, GPTK baselines recorded |
| B | Thin app shell | 3-6 (parallel with C) | Notarized .dmg launches a D3D11 game on a fresh user account, no Terminal |
| C | D3D12 bring-up | 4-12 | Microsoft samples match Windows reference images; vkd3d test pass count tracked nightly |
| D | First real games | 12-22 | 3 games finish their built-in benchmark with no visual errors |
| E | Performance | 22-32 | Every optimization has before/after numbers, no smoke test regressions |
| F | Governor (temperature, power, performance) | 26-34 (parallel with E) | Governed 30 min runs beat uncapped on p99 frame time |
| G | Layman product | 30-42 | A non-technical tester goes from download to playing without help |
| H | CrossOver benchmark window (14-day trial) | once 5+ games run | Frozen CrossOver baseline dataset |
| I | Public beta and release | 42+ | Notarized v1.0, public compatibility list |

Running the whole time:

- benchmark against GPTK (Apple's D3DMetal) on every change
- rebase the Wine fork on each Wine stable release
- watch what Apple does with Rosetta. Full Rosetta is promised through macOS 27, and macOS 28 keeps only a games subset. The whole x86 Wine stack depends on it.
- watch Wine ARM64EC on macOS. DXMT already has an ARM64X build config. If that path becomes real, the D3D12 layer should build the same way so the Metal side runs native instead of under Rosetta.

## Phase A: Foundation and go/no-go (weeks 1-3)

| ID | Step | Done when |
|---|---|---|
| A1 | Read the licenses for Metal Shader Converter, GPTK and DXMT, plus Wine's LGPL obligations | Written decision: use Metal Shader Converter, download it on first run, or build our own DXIL compiler |
| A2 | Toolkit: Ghidra (with Microsoft's symbol server), DXC, Metal Shader Converter CLI, Tracy, Xcode GPU tools | Each tool opens or disassembles a real game file |
| A3 | Windows reference PC with PIX, RenderDoc, GFXReconstruct and x64dbg | One D3D12 capture made and replayed on Windows |
| A4 | Build Wine 11 from source (x86_64, under Rosetta) | A Windows test app runs from our build |
| A5 | Build DXMT, run a D3D11 game, read how its Windows and macOS halves talk | Architecture notes written, game reaches gameplay |
| A6 | Telemetry logger v0, an `xctrace` runner script and a thermal test protocol | Overlay frame times agree with `xctrace`, repeat runs within 3% |
| A7 | GPTK baseline: 5 GOG games through Wine + D3DMetal | Results stored with versions, settings and room temperature |
| A8 | Ghidra learning pass on a small game exe | Imports, vendor checks and the D3D12 setup path mapped in one exe |

## Phase B: Thin app shell (weeks 3-6, parallel with C)

| ID | Step | Done when |
|---|---|---|
| B1 | SwiftUI launcher (arm64), runtime bundled inside the .app, prefixes in Application Support | App runs Wine from inside its own bundle |
| B2 | One click: pick a GOG installer, install it, launch it through DXMT | D3D11 game launches from the app |
| B3 | Developer ID signing, hardened runtime entitlements, notarization | Notarized .dmg opens on a fresh macOS user account with no warnings |
| B4 | Rosetta onboarding screen; drop the Z: drive and map only game folders | Windows apps can't see the home folder |
| B5 | Pick the graphics backend automatically from the exe's imports | Right backend chosen for 10 test exes |

## Phase C: D3D12 bring-up (weeks 4-12)

| ID | Step | Done when |
|---|---|---|
| C1 | `d3d12.dll` / `dxgi.dll` skeleton: device, adapters, outputs, queue, swapchain on `CAMetalLayer`, clear and present | D3D12HelloWindow sample runs |
| C2 | Shader path per A1; root signatures mapped to Metal argument buffers | D3D12HelloTriangle runs |
| C3 | Resources: committed and placed resources, heaps, upload/readback, copies, textures, samplers | HelloTexture and HelloConstBuffers samples run |
| C4 | Command lists, allocators, bundles, fences, barriers, multiple queues | Bundles, FrameBuffering and Multithreading samples run |
| C5 | Render pass inference v1, correctness only | No missing or garbage attachments in the samples |
| C6 | vkd3d's D3D12 test suite on a nightly run | Pass count graphed per commit |
| C7 | ExecuteIndirect, queries, MSAA, compute | ExecuteIndirect and nBodyGravity samples run |
| C8 | Image diff against Windows references with NVIDIA's FLIP | Every sample under its FLIP threshold |

## Phase D: First real games (weeks 12-22)

| ID | Step | Done when |
|---|---|---|
| D1 | Pick 3 targets: DX12, no Denuvo or anti-cheat, built-in benchmark, GOG copies (e.g. Cyberpunk 2077 plus 2 lighter, older titles) | List frozen, game versions pinned |
| D2 | GFXReconstruct capture of each game on Windows, replayed through the layer under Wine | Replays run as regression tests |
| D3 | Fix loop: `WINEDEBUG=+relay`, Ghidra on the game exe, black-box Metal captures compared against D3DMetal | Each game renders past its menus |
| D4 | Feature gap policy: reported capabilities and feature level, geometry and tessellation emulation, per-game config entries | Policy written, configs live in the database |
| D5 | Games that ship their own D3D12 runtime (Agility SDK `D3D12Core.dll` in the game folder) | Agility SDK games load our layer, not their bundled runtime |
| D6 | Nightly smoke suite: launch, first frame, no crash, for every game and replay | Runs against the packaged app |
| D7 | First comparison against GPTK | Per-game fps and frame time gap recorded |

## Phase E: Performance (weeks 22-32)

| ID | Step | Done when |
|---|---|---|
| E1 | Render pass inference v2: load/store actions, memoryless temporary attachments | Before/after frame time and memory bandwidth per game |
| E2 | Shader stutter: binary archives, background compiles on efficiency cores, pipeline list recording | First run vs second run 1% lows, before/after |
| E3 | Memory policy: shared vs private storage, no duplicate copies, unified memory flag reported to games | Resident memory and fps, before/after |
| E4 | CPU cost of the encode path under Rosetta, profiled with Tracy | CPU ms per frame, before/after |
| E5 | Frame pacing: limiter at frame start, present timing, 60/30 on a 60 Hz panel | Frame time variance, before/after |
| E6 | MetalFX: FSR 3.1 DLL replacement, DLSS through an NVAPI/NGX shim, XeSS | fps at the same output resolution, plus FLIP quality score |

## Phase F: Governor (weeks 26-34, parallel with E)

| ID | Step | Done when |
|---|---|---|
| F1 | Sensor module in the launcher: thermal state, power and frequency (IOReport), temperatures, power source | Readings logged, with a fallback when private APIs fail |
| F2 | Local socket between the launcher and the in-game layer | Frame times out, cap decisions in, under 1 ms overhead |
| F3 | Overlay drawn in the present path | fps, frame time graph, GPU watts, temperature and current cap visible |
| F4 | Control loop with hysteresis, battery vs charger profiles, thread priorities, Game Mode via the games category | Cap stays stable without flapping over a 30 min run |
| F5 | Proof: 30 min sustained runs, uncapped vs governed, on 2+ games | Lower p99 frame time in the last 10 min, or the defaults get changed |

## Phase G: Layman product (weeks 30-42)

| ID | Step | Done when |
|---|---|---|
| G1 | Steam flow: Install Steam button, Steam's web helper working, library visible | A Steam game launches from the app |
| G2 | Per-game database: schema, signed updates, keyed by Steam app ID or exe hash | Settings applied at launch with no user input |
| G3 | Anti-cheat detection and a compatibility rating shown before install | Anti-cheat games flagged before download |
| G4 | Controllers, Mac keyboard defaults, Retina and resolution defaults | Xbox and PlayStation pads work with no setup |
| G5 | Opt-in "Send report" with scrubbed logs, to a rate-limited endpoint on our own server | Report lands with logs, profile and Mac model |
| G6 | Sparkle updates for app and runtime, daily database updates, pipeline lists fetched on demand | Update from one version to the next without losing prefixes |
| G7 | UX test with 3 non-technical people, timed, no help | Download to playing in under 10 min (not counting the game download), zero Terminal |

## Phase H: CrossOver benchmark window (14-day trial, once 5+ games run)

| ID | Step | Done when |
|---|---|---|
| H1 | Before day 1: runner drives CrossOver's command line, game list ready, GOG copies installed | Dry run passes on GPTK |
| H2 | Days 1-5: full performance runs, every game and preset, 5 runs each | All results stored |
| H3 | Days 6-10: compatibility pass over a larger list (around 50 games) | Each game marked runs, renders correctly or fails, on both stacks |
| H4 | Days 11-12: UX benchmark, minutes and clicks to the first game | Compared with G7 |
| H5 | Days 13-14: rerun outliers, then freeze the baseline | Dataset tagged with CrossOver, D3DMetal and macOS versions |

## Phase I: Public beta and release (week 42+)

| ID | Step | Done when |
|---|---|---|
| I1 | Public docs and a contribution guide | Repo builds from a clean clone |
| I2 | Compatibility list site on a custom domain, with proper SEO | Live and indexed |
| I3 | Beta testers on M1 to M4 Macs, per-chip profiles | Smoke suite passes on 4 chip generations |
| I4 | Community compatibility reports feeding the database | First 20 outside reports merged |
| I5 | v1.0 release | Notarized, honest per-game ratings, published comparison against the frozen CrossOver baseline |
