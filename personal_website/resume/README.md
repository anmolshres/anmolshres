# Resumes

LaTeX sources for my resumes, built on the [Deedy resume template](https://github.com/deedydas/Deedy-Resume) (OpenFont version).

## Layout

```
resume/
├── Shrestha, Anmol (Resume).pdf   # the PDF the website links to (index.html)
└── latex/
    ├── template/                  # Deedy class file, fonts, license, original README
    ├── general/                   # general audience resume (source of the website PDF)
    ├── mayo-lab/                  # Mayo Clinic research lab versions (two page and one page)
    └── rochester-pbac/            # Rochester Pedestrian & Bicycle Advisory Committee version
```

Each folder under `latex/` (other than `template/`) holds one audience. Build output and PDFs inside `latex/` are git ignored, so only the `.tex` sources get committed.

## Setup

You need XeLaTeX and latexmk. On macOS:

```sh
brew install --cask mactex-no-gui   # includes xelatex and latexmk
brew install poppler                # optional, for pdftoppm previews
```

The fonts (Lato and Raleway) ship in `latex/template/fonts/`, so nothing needs to be installed system wide.

## Build a resume

Run latexmk from the resume's own folder:

```sh
cd latex/general
latexmk -xelatex resume-general.tex
```

The PDF lands next to the `.tex` file. To clean up build files (including the PDF):

```sh
latexmk -C
```

## Update the website resume

The website serves `Shrestha, Anmol (Resume).pdf`, which is a copy of the general resume. After editing `latex/general/resume-general.tex`:

```sh
cd latex/general
latexmk -xelatex resume-general.tex
cp resume-general.pdf "../../Shrestha, Anmol (Resume).pdf"
```

Keep that file name and location. `index.html` links to it, and the site is deployed from `personal_website/` on Netlify.

## Make a new resume

1. Copy the closest existing folder, for example `cp -r latex/general latex/<audience>`.
2. Rename the `.tex` file to `resume-<audience>.tex`.
3. Keep these lines right above `\documentclass`. They let the file find the class and fonts in `template/`:

   ```latex
   \makeatletter\def\input@path{{../template/}}\makeatother
   \newcommand{\resumefontdir}{../template/fonts/}
   \documentclass[]{deedy-resume-openfont}
   ```

   New folders must sit directly under `latex/` for the `../template/` paths to work.
4. Edit, build, and check the page count in the latexmk output.

## Template quirks

- **Silent overflow.** Each column is a `minipage`. If a column gets too long, LaTeX pushes it to a new page (sometimes leaving a blank page) instead of erroring. Check the page count after every build.
- **Overlapping bullets.** A `\runsubsection` or `\section` followed straight by a `tightemize` list can overlap the heading. Add `\vspace{\topsep}` before `\begin{tightemize}`.
- **Long titles.** A long `\runsubsection` plus `\descript` wraps badly. Put the role in `\location{Role | Dates}` instead.
- **No hyphenation.** Every resume uses `\usepackage[none]{hyphenat}` and `\sloppy` so words never break across lines.
- **Last updated date.** `\lastupdated` prints the build date, so rebuild before sending.

## Before you publish

This repo is public. Netlify serves everything under `personal_website/`, but `personal_website/_redirects` returns a 404 for anything under `/resume/latex/`, so the `.tex` sources aren't served on the website. They are still readable on GitHub.

- Leave the phone number off the general resume.
- Don't name internal Bentley products, repos, or clients. Public product names (iTwin.js, iTwin Sensor Data API, ProjectWise) are fine.
- Every claim should be something you can back up in an interview.
