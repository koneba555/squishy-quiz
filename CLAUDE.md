# Squishy Quiz — Project Reference

## Overview
Thai-language personality quiz web app. Single HTML file, no build step, no framework.
**Live:** https://squishy-quiz.pages.dev
**Repo:** https://github.com/koneba555/squishy-quiz

## Deploy
Push to `main` → Cloudflare Pages auto-deploys. No manual step needed.
```
git add . && git commit -m "..." && git push
```

## File Structure
```
squishy-quiz/
  index.html        ← entire app (HTML + CSS + JS inline)
  asset/
    intro.png       ← welcome screen image
    ขนมปัง.png      ← 16 squishy character images (Thai filenames)
    สตรอว์เบอร์รี่.png
    โดนัท.png
    เลมอน.png
    มาร์ชเมลโล่.png
    มะม่วง.png
    เค้ก.png
    อุนจิ.png
    ซาลาเปา.png
    ชีส.png
    โมจิ.png
    ทุเรียน.png
    เนยแท่ง.png
    มัทฉะ.png
    เยลลี่หมี.png
    ช็อกโกแลต.png
```

## 16-Type System (MBTI-style)

4 dimensions × 2 values = 16 types. Code is 4-letter string.

| Pos | Dimension | Values | MBTI equiv |
|-----|-----------|--------|------------|
| 1 | Energy (E) | B=Bubbly / C=Chill | E / I |
| 2 | Relationship (R) | W=Warm / C=Cool | F / T |
| 3 | Decision (D) | S=Soft / B=Bold | P / J |
| 4 | Style (S) | S=Sweet / U=Unique | S / N |

### 16 Types
| Code | Name | MBTI |
|------|------|------|
| BWSS | ขนมปัง (bread) | ESFP |
| BWSU | สตรอว์เบอร์รี่ (strawberry) | ENFP |
| BWBS | โดนัท (donut) | ESFJ |
| BWBU | เลมอน (lemon) | ENFJ |
| BCSS | มาร์ชเมลโล่ (marshmallow) | ESTP |
| BCSU | มะม่วง (mango) | ENTP |
| BCBS | เค้ก (cake) | ESTJ |
| BCBU | อุนจิ (unchi) | ENTJ |
| CWSS | ซาลาเปา (salapao) | ISFP |
| CWSU | ชีส (cheese) | INFP |
| CWBS | โมจิ (mochi) | ISFJ |
| CWBU | ทุเรียน (durian) | INFJ |
| CCSS | เนยแท่ง (butter) | ISTP |
| CCSU | มัทฉะ (matcha) | INTP |
| CCBS | เยลลี่หมี (gummy-bear) | ISTJ |
| CCBU | ช็อกโกแลต (chocolate) | INTJ |

### Scoring Logic
8 questions, each option has `{ dim, val }`. Tally scores per dimension. Winner per dim:
```javascript
E = dimScores.E.B >= dimScores.E.C ? 'B' : 'C'
R = dimScores.R.W >= dimScores.R.C ? 'W' : 'C'
D = dimScores.D.S >= dimScores.D.B ? 'S' : 'B'
S = dimScores.S.S >= dimScores.S.U ? 'S' : 'U'
```
Ties → first value wins (B, W, S, S).

### Compatibility Score
```javascript
function compatScore(a, b) {
  let s = 0;
  if (a[0] === b[0]) s += 2; // Energy (weight 2)
  if (a[3] === b[3]) s += 2; // Style (weight 2)
  if (a[1] === b[1]) s += 1; // Relationship
  if (a[2] === b[2]) s += 1; // Decision
  return s; // max 6
}
```
Top 3 buddies shown on result screen.

## Questions
8 questions across 4 dimensions (2 questions each):
- Q1, Q6 → Energy (E)
- Q2, Q4 → Style (S)
- Q3, Q8 → Decision (D)
- Q5, Q7 → Relationship (R)

Each question has 4 options: 2 per value. Story-driven fantasy narrative.

## Key Features

### Screens
1. **Welcome** — intro image + start button
2. **Quiz** — progress bar + question + 4 options
3. **Result** — squishy image + desc + 3 buddies + share buttons

### Animations
- Cloud background: 16 gooey SVG blobs with mouse parallax + repel
- Option buttons: jelly bounce (`jellyPop` keyframe) on tap, no blue highlight
- Question transitions: fade in/out (400ms delay to let jelly finish)

### Share
- **แชร์ผล 🎉** — Web Share API with captured card PNG, fallback download
- **IG button** — mobile: save PNG + open `instagram://story-camera`, desktop: download + open IG web
- Captures card with html2canvas (scale 2, rounded corners 28px, hides buttons during capture)
- Saved filename: `YYYYMMDD_squishy_<english-name>.png`

### Result Card Capture
Hidden during capture: `.share-row`, `.copy-notice`, `.restart-btn`, `.btn-download`
Card padding reduced to 20px during capture to minimize white border.
Rounded corners applied post-capture via canvas `roundRect` clip.

## Fonts
- **Itim** (Google Fonts) — all Thai body text, questions, options, descriptions
- **Fredoka** — English labels, start button

## CSS Notes
- `white-space: pre-wrap` on `.result-desc` — preserves `\n` line breaks in desc strings
- Thai text: avoid `letter-spacing` — breaks combining characters on iOS
- Card: `border-radius: 28px`, `max-width: 480px`
- Background: SVG `#cloud-goo` filter (feGaussianBlur + feColorMatrix for gooey effect)

## TYPES Object Structure
```javascript
XXXX: {
  name: 'Thai name',
  image: 'asset/Thai name.png',
  traits: ['trait1', 'trait2', 'trait3', 'trait4'],
  tagline: 'short tagline',
  desc: 'line1\nline2\nline3',  // \n for line breaks, pre-wrap renders them
  soft: '#XXXXXX',  // background color for result card
  ink: '#XXXXXX'    // accent color
}
```

## External Scripts
- `html2canvas 1.4.1` — card-to-PNG capture
- `qrcodejs 1.0.0` — QR code generation on result screen (links to squishy-quiz.pages.dev)

## Wrangler (manual deploy, now unused)
```
npx wrangler pages deploy . --project-name squishy-quiz
```
Auto-deploy via GitHub is active — no need to run this manually anymore.
