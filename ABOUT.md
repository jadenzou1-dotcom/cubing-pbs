# About this repo

A record of my 3x3 personal bests, plus the solve data behind each one, so I can see not only *how fast* I'm getting but *how consistent*. Every PB is kept, not just the latest, so the history shows how my averages improve over time.

[← Current PBs and PB history](README.md)

## How the repo is organized

| Path | What it is |
|---|---|
| [`README.md`](README.md) | My current PBs, the improvement on each, and the full PB history. Generated. |
| `ABOUT.md` | This file. Written by hand. |
| `CLAUDE.md` | Instructions for Claude so a new chat can add PBs and run the workflow. Written by hand. |
| `records/<event>/` | **The raw data.** One `.txt` file per PB, for `single`, `ao5`, `ao12`, `ao25`, `ao50` and `ao100`. |
| `stats/<event>/<date>/` | **Generated analysis** for each PB: stats page, two charts, and a JSON copy of the numbers. |
| `stats/progression.png` | The PB value for each event over time. |
| `fast-solves/` | A hand-picked bank of good solves with reconstructions. Not averages, so no distributions. |
| `scripts/build.py` | Reads everything in `records/` and `fast-solves/` and regenerates all the generated files above. |

The `.txt` files are the source of truth. Everything else can be deleted and rebuilt with `python3 scripts/build.py`, so no generated file is edited by hand.

## Records

Each file is a csTimer export: solve times with their scrambles. Names are `YYYY-MM-DD.txt`; a second PB of the same event on the same day is `YYYY-MM-DD_2.txt`, then `_3`, in the order they happened. A new PB is always a **new file**, so old records stay and become history. Optional reconstructions (`>` lines under a solve) are added for solves I want to break down move by move.

## What the stats page for each PB contains

- **The official average.** The best and worst 5% of solves (rounded up, at least one each) are dropped and the rest are averaged, as in WCA rules. The script recomputes this and warns if it disagrees with csTimer.
- **Distribution stats:** mean, median, min, max, Q1, Q3, IQR, range, and standard deviation, both over all solves and over only the solves that counted.
- **Consistency**, described below.
- **Shape:** skewness (a positive value means a tail of slow solves) and a Shapiro–Wilk p-value (below 0.05 means the times are unlikely to be normally distributed).
- **Sub-X counts:** how many solves were under 12, 13, 14, and so on.
- **Every solve,** with trimmed ones in (parentheses), and reconstructions if I added them.

## The two charts

**`distribution.png`: where my times land.**
- *ao25, ao50, ao100:* a smooth density curve (a kernel density estimate) with one tick per solve along the bottom, the average and median marked, a normal curve for comparison, and a box plot underneath. The y-axis is "solves within a 1-second window", so the curve's height at 14.3s is about how many solves fell near 14.3s. There are no histogram bins, because a time like 13.01 and one like 13.49 shouldn't land in the same bar.
- *ao5, ao12:* too few solves for a curve, so every solve is a labeled dot on a number line. Close dots stack into piles, and the line is shaded darker where times are concentrated.

**`sequence.png`: how the solves went in order.** Each solve in the order I did it, with the rolling average, and a dashed line at the PB average. It shows whether a PB came from a steady run or a hot streak. Time axes have a gridline every second (and dotted half-seconds when the times are close together).

## Consistency: σ / mean

A standard deviation of 2 seconds means very different things at a 10s average than at a 20s average, so each stats page also gives it as a fraction of the mean: **σ / mean**, the coefficient of variation. σ of 2 on a 10s average is 20%; on a 20s average it's 10%.

A single blowup solve inflates σ, so there's a second, outlier-proof measure: **IQR / median**, the spread of the middle half of my solves relative to the typical one. When σ / mean is much higher than IQR / median, a few outliers are the cause (as in my ao12, where one 21.58 drives most of the spread).

Two cautions:
- Compare consistency *within the same event*. An ao5 has only 5 solves, so its σ is noisy: one bad solve can swing it from 8% to 16%.
- σ / mean is not the same as speed. A lower percentage means steadier, not faster.

Over time, watching this number in the PB history shows whether new PBs are also getting more consistent, or just faster.

## Fast solve bank

`fast-solves/` holds individual solves I want to keep, with the scramble, time, date and a reconstruction. It's not part of any average, so it has no distributions. The bank's page summarizes move count (STM), turns per second (TPS), cube rotations, moves per F2L pair, and moves in the cross and the last layer.

## How to add a PB

New PBs go through Claude: paste the csTimer export into a chat opened in this folder. Claude saves the record, runs `scripts/build.py`, checks any reconstruction on a cube simulator, and pushes. The steps are in `CLAUDE.md`.
