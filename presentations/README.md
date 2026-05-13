# FlashDeck.AI — Presentations

This folder contains all FlashDeck pitch decks and presentation materials, version-controlled via Git.

## Folder Structure

```
presentations/
  schools-pitch-v1/     ← Schools partnership pitch deck (May 2026 — initial version)
  schools-pitch-v2/     ← (future) Updated version after first round of feedback
  investor-deck-v1/     ← (future) Investor / Cutlass Group update deck
```

## How Versioning Works

- Each presentation version lives in its own subfolder (e.g. `schools-pitch-v1`)
- When a presentation is updated, a new subfolder is created (e.g. `schools-pitch-v2`) — the previous version is never overwritten
- Git tracks every change, so you can compare any two versions or roll back at any time
- The live/shareable version of each deck is hosted via Manus Slides (link in the relevant section below)

## Presentations

### Schools Partnership Pitch Deck

**Purpose:** Pitch to head teachers in UK, Pakistan, Nigeria and Middle East  
**Target audience:** Head teachers, school administrators  
**First created:** May 2026  
**Pricing shown:** £ primary, ~$ USD reference  

| Version | Folder | Date | Key Changes | Live Link |
|---|---|---|---|---|
| v1 | `schools-pitch-v1/` | May 2026 | Initial version | See Manus project |

### Slides in This Deck

| File | Slide | Content |
|---|---|---|
| `cover.html` | 1 | Title slide — FlashDeck.AI Schools Partnership |
| `problem.html` | 2 | The revision problem students face |
| `solution.html` | 3 | How FlashDeck solves it |
| `how_it_works.html` | 4 | 3-step process |
| `school_features.html` | 5 | Features for schools and teachers |
| `global_reach.html` | 6 | Target markets: UK, Pakistan, Nigeria, Middle East |
| `pricing.html` | 7 | School pricing tiers (Starter / Growth / Whole School) |
| `testimonials.html` | 8 | Early adopter quotes (placeholder) |
| `next_steps.html` | 9 | Free pilot offer and next steps |
| `closing.html` | 10 | Contact and closing |

## Adding a New Presentation Version

1. Create a new subfolder: `presentations/schools-pitch-v2/`
2. Copy the files from the previous version as a starting point
3. Make your changes
4. Commit with a descriptive message: `Update schools pitch — added case study slide`
5. Update the version table in this README
