![banner](assets/img/banner.avif)

# Xenus DT1/DT2 Decompiler

[![Platform](https://img.shields.io/badge/platform-Windows-blue.svg)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE)
[![.NET](https://img.shields.io/badge/.NET-8.0-blueviolet.svg)](https://dotnet.microsoft.com/download/dotnet/8.0)
[![Release](https://img.shields.io/github/v/release/CodeNoob53/xenus-dt1-decompiler)](../../releases/latest)

**[Українська версія (README_UA.md)](README_UA.md)** | **[Versión en español (README_ES.md)](README_ES.md)**

A tool for extracting textures from **Xenus 2: White Gold**, **Boiling Point: Road to Hell**, and **The Precursors** game cache files (`*.DT1`).
Saves decoded textures as `.dds`, `.tga`, `.png`, `.bmp`, or `.jpg` while preserving the original folder structure.

---

## Requirements

- **Windows** (x86 or x64)
- **[.NET 8 Runtime](https://dotnet.microsoft.com/download/dotnet/8.0)** — required to run the application
- **VELoader.dll** from the **Steam release** of Xenus 2: White Gold — place it next to `xenus-dt1-decompiler.exe`

> **Note:** `VELoader.dll` from pirated copies of the game will not work (error 1114). You must use the DLL from the legitimate Steam release.
> 
> **Boiling Point / The Precursors Compatibility:** This tool can also decompile textures from **Boiling Point: Road to Hell** (Xenus 1) and **The Precursors** (Предтечі). To process Boiling Point textures, you **must** use the `VELoader.dll` from **Xenus 2**, **The Precursors**, or the **Vital Engine 3 SDK**. The DLL from Boiling Point itself will not work.

---

## Getting the cache files

The game stores its assets inside `.grp` archives. The `CACHE/` folder with `.DT1` files may not exist in your game directory until the archives are unpacked.

To extract them, use **GrpUnpacker** — a tool that is typically included with the **Vital Engine SDK**. Run it against the game's `.grp` files to produce the `CACHE/` folder structure that this decompiler expects.

> **Note:** GrpUnpacker is sometimes distributed as part of the Vital Engine SDK, and sometimes as a standalone tool. Some versions may be flagged by antivirus software — this is common for old game modding utilities. Use your own judgement when sourcing it.

---

## Installation

1. Download the latest release archive from the [Releases](https://github.com/CodeNoob53/xenus-dt1-decompiler/releases) page.
2. Extract the archive — it will create a `xenus-dt1-decompiler-vX.X.X` folder.
3. Copy `VELoader.dll` from your game folder into that folder, or specify the path to it in the GUI.
4. Run `xenus-dt1-decompiler.exe`.

> **Tip:** If `VELoader.dll` is placed in the same folder as the exe, you don't need to specify its path in the GUI — it will be detected automatically.

---

## Usage

### GUI (Graphical Interface)

Double-click `xenus-dt1-decompiler.exe` (no arguments).

1. Select the **input folder** containing `.DT1` files (e.g. `Xenus 2\CACHE\TEXTURES`).
2. Select the **output folder** where decoded files will be saved (defaults to `out_tex` in the program folder).
3. `VELoader.dll` is detected automatically if placed next to the exe.
4. Choose the **output format**:
   - **Auto** — detects the real format from the file's magic bytes *(recommended)*
   - **dds / tga / png / bmp / jpg** — converts the texture to the chosen format
5. Click **START DECOMPILE** and watch the progress in the log window.

### Command Line

```
xenus-dt1-decompiler.exe <input> [output_dir] [veloader_path] [format]
```

| Argument | Default | Description |
|---|---|---|
| `input` | — | Path to a `.DT1` file or a folder to scan recursively |
| `output_dir` | same folder as input | Folder to save decoded files |
| `veloader_path` | auto-detected | Path to `VELoader.dll` |
| `format` | auto | Output format: `dds`, `tga`, `bmp`, `png`, `jpg` |

**Examples:**

```powershell
# Decode all textures, auto format
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out"

# Decode and convert to TGA
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out" "C:\Games\Xenus 2\VELoader.dll" tga
```

---

## Repacking textures back into the game

> Credits: this workflow was originally documented by **badmofo** on [RPGWatch Forums](https://rpgwatch.com/forum/threads/editing-the-in-game-textures.12577/).

The Vital Engine 3 automatically re-compresses raw TGA files into `.DT1`/`.DT2` cache format when the game starts. No external packer tool is needed.

> **Requirements:** The game directory must be fully unpacked (`.grp` archives extracted with GrpUnpacker, so that loose files exist in `CACHE\TEXTURES\`).

### Replacing an existing texture

Using `KURSOR_1_TGA.DT1` as an example:

1. **Extract the original texture** using this decompiler tool in TGA or BMP format.
   > **Note:** the original format is hinted by the filename suffix — e.g. `_TGA` in `KURSOR_1_TGA.DT1` indicates TGA.
2. **Edit or replace the TGA with your own texture.** The engine supports a maximum of **2048×2048** pixels — textures larger than that will not compile at all. Use 2048×2048 with caution (high memory usage); **1024×1024 is recommended** for stability.
3. **Save the edited file as `.TGA`.** The decompiler already strips the engine suffix automatically — `KURSOR_1_TGA.DT1` is saved as `KURSOR_1.tga`, so use that name.
4. **Delete** the original `.DT1` and any `.DT2`/`.DT3` files with the same base name from `CACHE\TEXTURES\HUD\`.
5. **Place** your new `KURSOR_1.TGA` into `TEXTURES\HUD\` inside the game directory (create this folder — it does not exist by default):
   ```
   <game dir>\TEXTURES\HUD\KURSOR_1.TGA
   ```
6. **Launch the game.** The engine detects the unconverted TGA and re-compresses it, creating new cache files in `CACHE\TEXTURES\HUD\`:
   ```
   KURSOR_1_TGA.DT1
   KURSOR_1_TGA.DT2
   ```

### Adding a new texture

The process is the same as replacing — place a new `.TGA` file with a unique name into `TEXTURES\<subfolder>\` and launch the game to cache it. After caching, reference it by name in the relevant `.MAT` material file.

> **Alternative:** SDK tools **RF2View** and **levedit2** can trigger re-compression without launching the full game, but they are difficult to run on modern systems.

### Distributing a texture mod

To release modified textures as a mod, distribute the generated `.DT1` / `.DT2` files from `CACHE\TEXTURES\`. When players place them in the matching subfolder of their own `CACHE\TEXTURES\` directory, those files take precedence over the packed `.grp` archives — no repacking required.

---

## Notes

- All game textures are stored internally as **DDS** regardless of filename suffix (`_TGA`, `_BMP`, etc.). The tool detects the real format automatically.
- Format conversion (tga/png/etc.) is handled by **[texconv](https://github.com/microsoft/DirectXTex)** (Microsoft DirectXTex, MIT license) — included in the release archive.

---

## License

MIT — see [LICENSE](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE)

## Source Code

[github.com/CodeNoob53/xenus-dt1-decompiler](https://github.com/CodeNoob53/xenus-dt1-decompiler)
