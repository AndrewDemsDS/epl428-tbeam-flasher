# T-Beam Flasher

Browser tool to switch a LilyGO T-Beam S3 Supreme (ESP32-S3) between the C factory firmware and
MicroPython over Web Serial. Built for the University of Cyprus EPL428 IoT labs.

Live page: https://andrewdemsds.github.io/epl428-tbeam-flasher/

## Use

1. Open the page in Chrome, Edge or Opera on desktop (Web Serial needs one of these).
2. Pick a direction.
   - **C to MicroPython**: choose an ESP-IDF version and a MicroPython version, then flash.
     The two selectors are linked: picking an IDF version filters the MicroPython list.
   - **MicroPython to C**: restores the LilyGO factory image, or a `.bin` you pick (written at `0x0`).
3. Choose the serial port in the browser prompt. The page checks the chip is an ESP32-S3 before it writes.
4. Wait for the reset. Replug USB if the port does not come back.

If connecting fails, hold `BOOT` while plugging in USB and retry. Close Thonny, `mpremote` and any
other program that holds the port.

Flashing erases the board (the option is on by default). Files stored on a MicroPython board are lost.

## Custom firmware with a TensorFlow Lite model

The page can also flash a custom MicroPython build that includes TensorFlow Lite for Microcontrollers
(the `microlite` module from [mocleiri/tensorflow-micropython-examples](https://github.com/mocleiri/tensorflow-micropython-examples))
and a `.tflite` model frozen into flash.

A browser cannot compile firmware, so the build runs in GitHub Actions (`.github/workflows/build-custom.yml`):

1. On the page, choose your `.tflite` file (up to 48 KB, checked in the browser) or leave it empty for the sine demo.
   Larger models: commit them to `models/` and start the workflow from the Actions tab with the path.
2. Pick the board variant (no PSRAM works on every board), paste a fine-grained token (the page links to a
   pre-filled token form: Actions write, this repo only) and press Build. The token is never stored.
3. The page shows build progress. When it finishes the build is selected for flashing as a `custom-N` release.
4. On the board: `import microlite, tflm_model`, then `model = tflm_model.load()` and pass `model` to
   `microlite.interpreter(...)`.

The uploaded model appears in the public build log.

The MicroPython and ESP-IDF versions of a custom build are fixed by the upstream project at the pinned commit,
so they are older than the official builds above. The release notes list them.
The model is stored in flash as the frozen module `tflm_model` and copied to RAM only when `load()` runs.
Built images are kept in Releases, not in the repo.

## What is in the repo

| Path | Purpose |
|---|---|
| `index.html` | the whole app, one file, no build step |
| `catalog.json` | MicroPython builds (version, ESP-IDF version, sha256) and the C image URL |
| `firmware/` | official `ESP32_GENERIC_S3` MicroPython releases from micropython.org |
| `models/` | `.tflite` models for custom builds |
| `.github/workflows/` | the custom firmware build |
| `vendor/` | [esptool-js](https://github.com/espressif/esptool-js) 0.7.1 bundle and its licence |

MicroPython images are checked against their sha256 before writing, and the flash is verified with an
MD5 check afterwards. The ESP-IDF version of each build is read from the binary itself.

The C factory image belongs to LilyGO. It is fetched from their repository at flash time and is not
stored here.

## Run locally

```
python3 -m http.server 8000
```

Open `http://localhost:8000/`. Web Serial works on `localhost` and HTTPS only.

## Add a MicroPython release

Download the `ESP32_GENERIC_S3` `.bin` into `firmware/`, then add an entry to `catalog.json` with its
version, date, ESP-IDF version (`strings file.bin | grep 'IDF version'`), size and sha256.

## Licences

MicroPython is MIT licensed. esptool-js is Apache-2.0 (`vendor/esptool-js.LICENSE`).

## AI assistance

Parts of this tool were written with AI assistance and reviewed by the author.
