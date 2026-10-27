# Yakuza PSP Recomp

**Yakuza for PlayStation 2, running natively on the PlayStation Portable. Not emulated: its own code, translated for the PSP's processor.**

![Status](https://img.shields.io/badge/STATUS-EXPERIMENTAL-f59e0b?style=for-the-badge&labelColor=0d1117)
![Platform](https://img.shields.io/badge/PLATFORM-PSP-3b82f6?style=for-the-badge&logo=playstation&logoColor=white&labelColor=0d1117)
![Tested on](https://img.shields.io/badge/TESTED_ON-PSP_GO-8b5cf6?style=for-the-badge&logo=playstation&logoColor=white&labelColor=0d1117)
![Language](https://img.shields.io/badge/LANGUAGE-C-94a3b8?style=for-the-badge&logo=c&logoColor=white&labelColor=0d1117)
![Release](https://img.shields.io/badge/RELEASE-TBA-ef4444?style=for-the-badge&labelColor=0d1117)

---

> [!IMPORTANT]
> **In development. No public build.**
>
> The game already boots, loads saves and is playable on real hardware, but it is still far from a comfortable speed.
> No game files, disc images or executables are (or will be) distributed here: everyone uses **their own disc**.

## The idea

The PS2 and the PSP are cousins: both use **MIPS** processors. Instead of emulating the PS2 one instruction at a time, which is far too heavy for a 333 MHz handheld, this project takes the program from the disc and **translates every function to C**, which is then compiled as a native PSP program.

The game's logic (AI, physics, crowds, script, camera) runs as real PSP code. Whatever the PSP lacks is replaced.

## How it works

```
 PS2 disc (yours)                          PlayStation Portable
 ────────────────                          ────────────────────
 MIPS program (EE)   ── static recomp ───► native C code     ──► PSP CPU
 system calls        ── HLE ─────────────► kernel, files, memory card
 sound (IOP)         ── HLE ─────────────► modules answered in C
 VU1 + GS (graphics) ── graphics bridge ─► PSP GE commands
```

| Layer | What it does |
|---|---|
| **Static recompilation** | Every game function becomes C; code overlays are loaded on demand. |
| **HLE (high-level emulation)** | The PS2 operating system, disc reads, the memory card as a file, the controller: each answered exactly as the original would. |
| **Graphics bridge** | Reads what the game would send to the PS2's graphics chips, does the VU1's work (transform, character skinning) and draws with the PSP's **GE**. |
| **Render engine** | Reuses work between frames: model blocks recorded once and replayed, textures converted and kept, GE state sent only when it changes. |

## Developing on real hardware

Most of the work happens **on the console itself**, with the PC connected over USB:

- **PSPLink inside the game**: remote shell, file copies, screenshots and the live log.
- **Remote restart**: the PC asks, the game shuts down in order and starts again (even as another build, for bisection).
- **In-run A/B tests**: variants alternate in random blocks, compared by the median of per-round ratios, so warm-up and battery affect them all equally.
- **Profiler with CPU counters**: time per zone, CPI, instruction and data cache misses.

> Emulators answer *"does it work?"*. **Performance numbers only count when they come from a real PSP**, and some bugs (cache coherency between the CPU and the GE, for one) simply never show up in an emulator.

## Compatibility

| Model | RAM | Status |
|---|---|---|
| PSP-1000 | 32 MB | Not supported (not enough memory) |
| PSP-2000 / 3000 | 64 MB | Target |
| **PSP Go** | 64 MB | **Tested** (ARK custom firmware) |
| PSP Street | 64 MB | Target (untested) |

Requires custom firmware and the 64 MB mode.

## Current status

- [x] Boot, menus, cutscenes and gameplay on real hardware
- [x] Loading and saving
- [x] Correct picture on the PSP Go (including a fix for a mosaic that only existed on hardware)
- [x] Remote development over USB
- [ ] Playable speed without frame skipping
- [ ] Sound
- [ ] Depth flicker on some textures

**Current bottleneck:** the graphics bridge, about two thirds of every frame. The CPU spends more than half of its time waiting for memory; the way forward is **doing less work per frame**, keeping whatever does not change from one frame to the next.

## Legal notice

Yakuza is a registered trademark of SEGA. This is an independent, unofficial, non-commercial project, not affiliated with SEGA, Sony Interactive Entertainment or the original game's developers. No copyrighted material (game code, graphics, audio, disc images or BIOS) is distributed in this repository. Playing requires owning the original disc.

## Credits

- Port by **[RodrigoCobo](https://github.com/RodrigoCobo)**
- Recompilation: **[PS2RC](https://github.com/SandstoneDev/PS2RC)**
- Toolchain: **pspdev** (psp-gcc, PSPSDK) · debugging: **PSPLink** · custom firmware: **ARK**

---

<p align="center"><sub>Made for the PlayStation Portable homebrew community.</sub></p>
