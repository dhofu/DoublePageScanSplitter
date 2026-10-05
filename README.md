# DoublePageScanSplitter

DoublePageScanSplitter splits scans of double pages (book openings) into a left page (**verso**) and a right page (**recto**). It's a small tool for digitisation projects. Fully automatic splitting often fails on historical material, while splitting by hand in an image editor is slow. With DoublePageScanSplitter you mark the gutter of each scan with a couple of clicks and the tool does the rest.

The tool comes in two versions:

| Version | File | Requirements |
|---|---|---|
| **Browser app** (recommended) | [`code/DoublePageScanSplitter.html`](code/DoublePageScanSplitter.html) | A modern web browser. No installation and no internet connection needed. |
| **Jupyter notebook** | [`code/DoublePageScanSplitter_v2.ipynb`](code/DoublePageScanSplitter_v2.ipynb) | Python 3, OpenCV, NumPy, pandas, Jupyter |

Both versions use the same splitting logic and write the same output files.

## How it works

For each double page, click on the gutter **twice**: once near the top and once near the bottom. The two points define the split line, so the tool also handles gutters that are slanted or skewed. The image is split along that line and the next image opens.

There are two split modes:

- **`rotate`** (default): the image is rotated around the middle of the gutter until the gutter is vertical, then it is split straight down. Both pages come out deskewed. Corners that have no image content after the rotation are filled with the fill colour (white by default).
- **`cut`**: the image is cut along the slanted line with no rotation and no resampling, so the original pixels stay untouched. The small triangle that belongs to the other page is filled with the fill colour.

If the gutter is almost exactly vertical (less than 0.1°), both modes make a plain vertical cut.

## Browser app

**Use it online:** https://dhofu.github.io/DoublePageScanSplitter/

Even the online version processes everything in your browser and never uploads your images.

1. Open the online version, or download [`code/DoublePageScanSplitter.html`](code/DoublePageScanSplitter.html) and open it in your browser. Double-clicking the file works, and it runs offline.
2. Click **Open images** or **Open folder**, or drag JPG files onto the window.
3. *Optional, Chrome/Edge only:* click **Output folder…** to write the split pages straight into a folder. In other browsers, or if you don't pick a folder, the results are offered as a ZIP download.
4. Choose the **Mode** (`rotate` or `cut`), the **JPEG** quality, the **Fill** colour and, if you like, your name as **Executor** (it's recorded in `timing.csv`).
5. Click the gutter of each image twice. A magnifier next to the cursor helps you place the points precisely.
6. When you're done, click **Finish & export**.

Everything runs locally in your browser and no images are uploaded anywhere.

### Controls

| Input | Action |
|---|---|
| Left click ×2 | Mark the top and bottom of the gutter (order doesn't matter) |
| Right click / <kbd>S</kbd> | Don't split this image |
| <kbd>R</kbd> / <kbd>Esc</kbd> | Reset the clicks on the current image |
| <kbd>←</kbd> / <kbd>→</kbd> | Previous / next image |
| Click in the file list | Go to that image (to redo a split, just click twice again) |

### Browser support

| Browser | Input | Output |
|---|---|---|
| Chrome, Edge (and other Chromium-based browsers) | Files, folders, drag & drop | Directly into a folder, or ZIP |
| Firefox, Safari | Files, folders, drag & drop | ZIP download |

## Jupyter notebook

### Installation

```bash
pip install -r requirements.txt
```

### Usage

1. Open [`code/DoublePageScanSplitter_v2.ipynb`](code/DoublePageScanSplitter_v2.ipynb) in Jupyter.
2. In the configuration cell, set `sourceDir` (folder with your scans) and `targetDir` (output folder). You can also set `MODE`, `FILL`, `MIN_ANGLE` and `JPEG_QUALITY` there.
3. Run all cells. An OpenCV window opens for each image:
   - **Left click** twice: top and bottom of the gutter. The window moves on automatically after the second click.
   - **Right click**: don't split this image.
   - **r**: reset the clicks on the current image.
   - **Esc** or closing the window: stop collecting points. The images you've already marked are still processed.

Subfolders of `sourceDir` are processed recursively and their structure is mirrored in `targetDir`.

[`code/DoublePageScanSplitter_v1.ipynb`](code/DoublePageScanSplitter_v1.ipynb) is the original version, which takes one click per image and splits strictly vertically. It's kept for reference.

## Output

For every split image `NAME.jpg` the tool writes two files:

- `NAME_L_verso.jpg`: the left page
- `NAME_R_recto.jpg`: the right page

It also writes two CSV files that document the run:

**`splitValues_v2.csv`**: one row per image, with a header row. New runs are appended.

| Column | Meaning |
|---|---|
| `Filepaths` | Path of the source image |
| `x1`, `y1` | Upper point of the gutter (pixels) |
| `x2`, `y2` | Lower point of the gutter (pixels) |
| `angle` | Gutter angle against the vertical, in degrees (positive = the bottom leans to the right) |
| `mode` | `rotate` or `cut` |

For images that weren't split, the coordinate, angle and mode columns are empty.

**`timing.csv`**: one row per run, with no header. Each row is appended.

`sourceDir, sourceDirFileCount, targetDir, targetDirFileCount, elapsedTime (s), elapsedTime (HH:MM:SS), date/time, executor`

## Sample data

[`data/images/`](data/images/) contains four sample double-page scans. [`data/split/`](data/split/) contains the results of splitting them with version 1 of the notebook, including the v1 log files (`splitValues.csv` holds one x value per image).

## Repository structure

```
DoublePageScanSplitter/
├── code/
│   ├── DoublePageScanSplitter.html       # browser app
│   ├── DoublePageScanSplitter_v2.ipynb   # notebook, two-point split (current)
│   └── DoublePageScanSplitter_v1.ipynb   # notebook, one-click vertical split (legacy)
├── data/
│   ├── images/                       # sample double-page scans
│   └── split/                        # sample output
├── index.html                        # GitHub Pages entry point (forwards to the browser app)
├── requirements.txt
├── LICENSE
└── README.md
```

## License

The code is released under the [MIT License](LICENSE).
