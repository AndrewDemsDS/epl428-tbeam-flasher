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
   **Your own firmware**: drag any `.bin` onto the page (or the drop box). The page checks it is an ESP32-S3
   image, shows the MicroPython and ESP-IDF versions it finds, and flashes it at the address you set (default `0x0`,
   right for merged images such as `firmware.bin`).
3. Choose the serial port in the browser prompt. The page checks the chip is an ESP32-S3 before it writes.
4. Wait for the reset. Replug USB if the port does not come back.

If connecting fails, hold `BOOT` while plugging in USB and retry. Close Thonny, `mpremote` and any
other program that holds the port.

Flashing erases the board (the option is on by default). Files stored on a MicroPython board are lost.

## MicroPython with TensorFlow Lite (microlite)

The MicroPython list includes `1.19.1 + microlite` (ESP-IDF 4.4): MicroPython with TensorFlow Lite for Microcontrollers
and ulab, built from [mocleiri/tensorflow-micropython-examples](https://github.com/mocleiri/tensorflow-micropython-examples).
It is **experimental and not yet tested on a board**. It is an old MicroPython (1.19.1) and has about 130 KB of app
space left. If the REPL does not appear over USB after flashing, flash an official build again.

Try the sine demo in `examples/`: copy `hello_world.py` and `model.tflite` to the root of the board
(`mpremote cp examples/hello_world.py examples/model.tflite :`), then `import hello_world` in the REPL.

## Python IDE

The **Python IDE** tab embeds [ViperIDE](https://viper-ide.org) (MIT), an existing browser IDE for MicroPython with an editor,
terminal and file manager. Use its USB button to connect the board. Flashing from this page unloads the IDE first so the
serial port is free. Some browsers block third-party storage inside frames and leave the frame blank, so there is also an
"Open in new tab" button. The link `#ide` opens the tab directly.

## What is in the repo

| Path | Purpose |
|---|---|
| `index.html` | the whole app, one file, no build step |
| `catalog.json` | MicroPython builds (version, ESP-IDF version, sha256) and the C image URL |
| `firmware/` | official `ESP32_GENERIC_S3` MicroPython releases from micropython.org, plus the microlite build |
| `examples/` | the microlite sine demo and its model |
| `vendor/` | [esptool-js](https://github.com/espressif/esptool-js) 0.7.1 (Apache-2.0) [xterm.js](https://xtermjs.org) 5.5.0 (MIT) and [Bootstrap](https://getbootstrap.com) 5.3.3 (MIT), with licences |

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

MicroPython is MIT licensed. esptool-js is Apache-2.0 (`vendor/esptool-js.LICENSE`). Bootstrap is MIT (`vendor/bootstrap.LICENSE`).

## AI assistance

Parts of this tool were written with AI assistance and reviewed by the author.
