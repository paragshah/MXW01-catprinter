![Cat Printer](./media/hackoclock.jpg)

This project allows you to use the MXW01 thermal cat printer with Python. It
provides a command-line interface to print images with various dithering
algorithms and intensity settings.

Discuss in the [#mxw01-hacking](https://hackclub.slack.com/archives/C08MKBC8BE3)
channel on the [Hack Club Slack](https://hackclub.com/slack)!

# Setup

The only requirement is [uv](https://docs.astral.sh/uv/getting-started/installation/).
`print.py` is a self-contained uv script: its dependencies are declared in a
[PEP 723](https://peps.python.org/pep-0723/) block at the top of the file, and
its shebang (`#!/usr/bin/env -S uv run --script`) makes uv install them into a
cached environment the first time you run it. There's no venv to create or
activate.

```bash
# Install uv (macOS/Linux; see the link above for other options)
$ curl -LsSf https://astral.sh/uv/install.sh | sh

# Clone the repo and run the script directly
$ git clone <this-repo-url> MXW01-catprinter
$ cd MXW01-catprinter
$ ./print.py --help
```

## Running the script

```bash
# From inside the repo
$ ./print.py ~/Pictures/cat.png

# From any other directory, using the full path
$ ~/path/to/MXW01-catprinter/print.py ~/Pictures/cat.png

# Or symlink it onto your PATH and run it from anywhere
$ ln -s "$PWD/print.py" ~/.local/bin/catprint
$ catprint ~/Pictures/cat.png

# Windows (shebangs aren't supported), or if the file isn't executable
$ uv run --script print.py ~/Pictures/cat.png
```

The first run takes a little longer while uv downloads the dependencies;
later runs reuse the cached environment.

If you change the dependencies, update both the `# /// script` block in
`print.py` and `pyproject.toml` so they stay in sync.

# Usage

```bash
$ ./print.py --help
usage: print.py [-h] [-l {debug,info,warn,error}]
                [-b {mean-threshold,floyd-steinberg,atkinson,halftone,none}]
                [-s] [-d DEVICE] [-i INTENSITY] [--top-first]
                filename

Prints an image on your MXW01 thermal cat printer

positional arguments:
  filename

options:
  -h, --help            show this help message and exit
  -l {debug,info,warn,error}, --log-level {debug,info,warn,error}
  -b {mean-threshold,floyd-steinberg,atkinson,halftone,none}, --dithering-algo
{mean-threshold,floyd-steinberg,atkinson,halftone,none}
                        Which image binarization algorithm to use. If 'none'
                        is used, no binarization will be used. In this case
                        the image has to have a width of 384 px.
  -s, --show-preview    If set, displays the final image and asks the user for
                        confirmation before printing.
  -d DEVICE, --device DEVICE
                        The printer's Bluetooth Low Energy (BLE) address (MAC
                        address on Linux; UUID on macOS) or advertisement name
                        (e.g.: "MXW01"). If omitted, the the script will try
                        to auto discover the printer based on its advertised
                        BLE services.
  -i INTENSITY, --intensity INTENSITY
                        Print intensity/energy byte (0x00-0xFF, default 0x5D).
                        Higher values generally produce darker prints. Accepts
                        hex (0xNN) or decimal.
  --top-first           Print the image top-first. By default, the image is
                        rotated 180 degrees to print right-side-up.
```

# Protocol Documentation

The MXW01's BLE protocol is documented in [PROTOCOL.md](./PROTOCOL.md). This
documentation is a work in progress, please contribute!

# Different Algorithms

This script offers several different dithering algorithms to convert images to
monochrome for printing. The following algorithms are available:

**Mean Threshold:**

![Mean threshold](./media/grumpymeanthreshold.png)

**Floyd Steinberg (default):**

![Floyd Steinberg](./media/grumpyfloydsteinbergexample.png)

**Atkinson:**

![Atkinson](./media/grumpyatkinsonexample.png)

**Halftone dithering:**

![Halftone](./media/grumpyhalftone.png)

**None (image must be 384px wide):**

![None](./media/grumpynone.png)
