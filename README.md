# N-Light

A interactive pipeline for an existing laxpcsoft (AstroSat LAXPC Format-B) and light curve inspection. Installs through the standard reduction sequence and produces a lightcurve plot plus one plot per GTI (good time interval).

It does **NOT** bundle or build laxpcsoft itself. You need `laxpcl1.e` and `backshiftv3.e` already compiled somewhere (your normal LAXPC1/eclipse_gti folder), and HEASoft's `lcurve` available (run `heainit` first, or make sure it's on PATH).

## Install

```bash
bash installer/install.sh
```

You'll be asked where to install (default: your home directory). It creates:

```
<install path>/N-Light/
    N-Light.sh    <- run this to launch N-Light
    Readme.md     <- this file
    .N-Light/     <- everything else (hidden -- leading dot, so it
                     won't clutter a normal `ls` or file browser)
```

Inside `.N-Light/` it creates a venv and installs PyQt5, pexpect, astropy, matplotlib, numpy into it, and sets up a desktop launcher (see "Logo and desktop launcher" below).

## Run

```bash
~/N-Light/N-Light.sh
```

or use the "N-Light" entry in your application menu, installed by the desktop launcher.

## Usage

1. Enter a source/folder name (this becomes the output subfolder).
2. Add one or more orbit folder paths with the "Add Orbit" button, on the left-hand panel.
3. Press "Run" -- this opens Settings, pre-filled with defaults taken from a real laxpcsoft session. Everything here is editable:
   - LAXPC1 software dir (your eclipse_gti path) -- set this once to match your machine.
   - Output base path (default: home directory).
   - All `laxpcl1.e` / `backshiftv3.e` / `lcurve` parameters.
4. Press "Proceed" to run the pipeline. Progress and raw tool output stream into the log panel on the right.
5. Output lands in `<output base path>/<source name>/` -- see "Output files" below for what each one is.

## Output files

Everything for a run is written to `<output base path>/<source name>/`:

- **`<source name>_lightcurve.png`**
  The full combined lightcurve (all orbits, all GTIs together), plotted from the final `.lc` file. This is the main "did the reduction work" plot.

- **`gti_plots/gti_001.png, gti_002.png, ...`**
  One plot per good time interval (GTI), in time order, cut from the same lightcurve as above. GTI boundaries are read from `gti.inp` (see below), so the number of plots equals the number of GTIs found. Useful for looking at individual segments without the gaps between orbits/passes.

- **`<source name>.lc`** (or similarly named `.lc` file)
  The FITS lightcurve produced by HEASoft's `lcurve` -- this is the actual data behind the PNG plots (time, rate, error per bin). Copied here so you have the raw product, not just the picture, for further analysis.

- **`gti.inp`**
  A copy of the GTI table used for this run (start/stop times for each good time interval), renamed from the `lxp2level2.gti` file `laxpcl1.e` produces. This is what `plot_gti_segments()` reads to split the lightcurve into the per-GTI plots above.

Everything else (`laxpcl1.inp`, `back4.inp`, intermediate level-2 files, etc.) stays in your LAXPC1/eclipse_gti working directory, the same as a manual laxpcsoft run -- N-Light only copies the final products above into the output folder.

## Notes on backshiftv3

`backshiftv3.e` sometimes suggests a different background epoch (e.g. "It may be better to use feb17"). That judgement call is left to you: after that step the app logs the response file, gain offset, and any suggestion. If you want to try a different epoch, edit the first line of `back4.inp` in your LAXPC1 dir yourself and re-run N-Light.

## References and Acknowledgements

N-Light is a wrapper/inspection GUI around external instruments, software, and libraries that it does not itself implement. Credit for the actual reduction and analysis work belongs to these tools:

### Mission / instrument

- **AstroSat and the LAXPC instrument** -- the mission and payload whose Format-B data this pipeline reduces and inspects.
  ISRO / TIFR AstroSat-LAXPC team.
  https://www.iucaa.in/~laxpc/

### Reduction software (external, not bundled)

- **laxpcsoft (`laxpcl1.e`, `backshiftv3.e`)** -- the LAXPC1/eclipse_gti level-1/level-2 reduction and background-shift tools that N-Light drives via pexpect. N-Light does not build or distribute these; you must have your own compiled copy.
  LAXPC Instrument Team / POC, AstroSat.
  https://www.iucaa.in/~laxpc/data_reduction.html

- **HEASoft `lcurve`** -- used to produce the FITS lightcurve from the reduced level-2 products.
  NASA HEASARC (High Energy Astrophysics Science Archive Research Center).
  https://heasarc.gsfc.nasa.gov/docs/software/lheasoft/

### Python libraries (bundled into the app's venv -- see `.N-Light/requirements.txt`)

- **PyQt5** -- GUI framework.
  Riverbank Computing. https://www.riverbankcomputing.com/software/pyqt/
- **pyqtgraph** -- interactive lightcurve/GTI plotting.
  pyqtgraph developers. https://www.pyqtgraph.org/
- **astropy** -- FITS I/O (reading/writing `.lc` files).
  The Astropy Collaboration. https://www.astropy.org/
- **matplotlib** -- static PNG rendering of the lightcurve and GTI plots.
  The Matplotlib Development Team. https://matplotlib.org/
- **numpy** -- array handling throughout the pipeline and plotting code.
  NumPy developers. https://numpy.org/
- **pexpect** -- scripted interaction with the laxpcsoft command-line tools (`laxpcl1.e`, `backshiftv3.e`).
  pexpect developers. https://pexpect.readthedocs.io/

If you publish results produced with N-Light, please cite AstroSat/LAXPC and HEASoft per their respective citation guidelines, in addition to (or instead of) this tool.

---

For any queries, contact knnazimrh@gmail.com
