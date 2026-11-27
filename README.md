# Sulam · סֻלָּם

A Jewish recovery app for leaving pornography behind gradually, one rung a week, guided by the Tanakh, the Talmud, Pirkei Avot, Kabbalah and the Musar tradition. Alongside the recovery path runs a second strand toward kedushah: honouring women as created in God's image, and preparing for marriage or deepening an existing one.

This repository holds a **clickable prototype**: a single `index.html` with no build step and no dependencies apart from Google Fonts.

> Who is mighty? One who conquers his inclination. (Pirkei Avot 4:1)

## Run it

**Try it online: https://archaejon.github.io/sulam/**

Or open `index.html` in a browser. Or serve the folder:

```bash
python3 -m http.server 8000
```

On first open it shows an **example journey** (week 10, Tiferet of Yetzirah) so you can see the app in use. Tap **Start my own** to go through setup.

## The structure

### Two axes, one number

Each content type has a weight:

| Content | Weight |
|---|---|
| Boy-girl (BG) | 5 |
| Solo video (SV) | 4 |
| Solo picture (SP) / Clothed video (CV) | 3 |
| Clothed picture (CP) | 2 |
| Imagination (I) | 1 |

A week's score is the sum of what was used. The weekly **ceiling** starts at 35 (7×BG) and drops by **one point a week** to 0. This reproduces the de-escalation path exactly:

- Week 1: 7×BG = 35
- Week 2: 6×BG + 1×SV = 34
- Week 3: 5×BG + 2×SV = 33
- …
- Week 8: 7×SV = 28
- …
- Week 35: 1×I = 1
- Week 36: 0

Frequency and content type both count. Each world also has a **cap on content type**: in Yetzirah nothing worse than solo video, however many points remain.

### Five worlds × seven weeks

| Weeks | World | Transition | Kedushah teaching |
|---|---|---|---|
| 1–7 | Asiyah, World of Action | BG → SV | She is not content |
| 8–14 | Yetzirah, World of Formation | SV → SP/CV | Not good to be alone |
| 15–21 | Beriah, World of Creation | SP/CV → CP | In the image of God |
| 22–28 | Atzilut, World of Emanation | CP → I | One soul in two bodies |
| 29–35 | Adam Kadmon, Primordial Will | I → 0 | Building the home |
| 36+ | Shomer HaBrit | Ceiling of 0, guarded | |

Within each world the seven weeks follow the seven lower sefirot, Chesed to Malchut, like the counting of the Omer. Each week has a middah and a practice ("Gevurah of Asiyah").

### Placement

Setup asks for last week's frequency, the usual content type and the user's life stage. The starting week is the latest week whose points ceiling *and* content cap both cover the baseline. The ceiling is never set above the user's own baseline, so the app never gives anyone more room than they were already taking.

### Falling and returning

- A week under the ceiling climbs one rung. A week over it repeats. After three hard weeks in a row, the app offers to step back one rung.
- **A fall never erases progress.**
- Going over the ceiling, or above the world's content cap, opens a **teshuvah flow** following the Rambam (Hilchot Teshuvah 2:2): regret, with triggers to pick; confession; and a fence for the future. It ends with Tikkun HaKlali.
- Breaking a **covenant commitment** opens the same flow.

### The kedushah strand

Each world adds one commitment to the user's covenant, and commitments stay in force once added. The wording depends on life stage: single or dating (preparing for marriage), or married (elevating the marriage). The week review asks about each commitment. Covenant results are recorded but do not affect climbing.

## Features

- **Today:** the ladder-of-light hero, the weekly ceiling, a seven-day strip on the real calendar (missed days are flagged and caught up), the middah of the week, the kedushah card and covenant, and a quote of the day.
- **Ladder:** all 35 rungs through the five worlds, each world with its own glow colour.
- **SOS:** a 10-minute urge timer with a breathing guide, rotating quotes, immediate actions, and the chaver's number with a copy button. Conquering an urge triggers a moment of light.
- **Wisdom:** about 50 quotes in Hebrew and English, tagged for urges, falls, climbing, daily use and women and marriage, with links to Sefaria.
- **Journal:** week history, covenant results, patterns in what leads to a fall, life stage, chaver, PIN lock, privacy notes, and prototype tools (a test clock and a "fill week" button).
- **PIN lock:** an optional 4-digit PIN, asked on open and after a minute in the background.

## Status and caveats

- **Prototype.** State lives in the browser's `localStorage`, unencrypted. The PIN stops someone casually opening the app; it does not protect the data.
- **Quotes need verifying.** The English renderings are the author's own, and lines marked "summary" are paraphrases. Check every source on Sefaria, the Hebrew especially, before relying on it.
- **Rabbinic review needed.** The gradual ceiling is framed as a limit, never a permission (Kiddushin 21b; Rambam, Shemonah Perakim ch. 4). A rav should review this framing and the kedushah strand before release.
- **Not a medical device.** The app supports teshuvah and recovery and does not replace a therapist. A clinician who works with compulsive sexual behaviour should review it.
- **Kabbalistic framing.** The five worlds and the sefirot are used as a map for the journey, inspired by Kabbalah rather than a teaching of it.

## Roadmap

- **A real app:** React Native / Expo, encrypted on-device storage (SQLite), biometric lock, local notifications for check-ins.
- **Multi-faith editions:** Islam, Sikhi, the Bahá'í Faith and an interfaith edition, chosen at setup and centred on moral monotheism. See [docs/MULTI-FAITH.md](docs/MULTI-FAITH.md).
- **Optional accountability:** chaver check-ins, still with no server-side record of what was logged.
