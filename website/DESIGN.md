# DESIGN.md — GHP Design System
*Extracted via Google Stitch · April 2026*

## Color Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--color-navy` | `#1B2B4B` | Primary brand, nav, headings |
| `--color-navy-dark` | `#111D33` | Hover states, footer |
| `--color-navy-light` | `#2C3E6B` | Secondary sections |
| `--color-cream` | `#F7F2E8` | Backgrounds, cards |
| `--color-cream-dark` | `#EDE6D6` | Alternate section BG |
| `--color-gold` | `#C9A55A` | Accent, CTAs, dividers |
| `--color-gold-light` | `#E8CC87` | Hover gold states |
| `--color-white` | `#FFFFFF` | Cards, inputs |
| `--color-text-primary` | `#1A1A1A` | Body copy |
| `--color-text-muted` | `#6B7280` | Captions, secondary |

## Typography

| Token | Font | Weight | Size | Usage |
|-------|------|--------|------|-------|
| `--font-serif` | Playfair Display | 400 / 700 | — | All headings |
| `--font-sans` | Inter | 400 / 500 / 600 | — | Body, UI, labels |
| `--text-hero` | serif | 700 | clamp(2.5rem, 6vw, 5rem) | Hero headline |
| `--text-h1` | serif | 700 | clamp(2rem, 4vw, 3.5rem) | Section headings |
| `--text-h2` | serif | 400 | clamp(1.5rem, 3vw, 2.25rem) | Sub-headings |
| `--text-body` | sans | 400 | 1rem / 1.125rem | Body copy |
| `--text-label` | sans | 600 | 0.75rem | Tags, labels (uppercase) |

## Spacing Scale (8px grid)

`4 · 8 · 12 · 16 · 24 · 32 · 48 · 64 · 80 · 96 · 128px`

## Elevation / Shadows

| Level | CSS Value | Usage |
|-------|-----------|-------|
| `shadow-sm` | `0 1px 3px rgba(0,0,0,0.08)` | Cards resting |
| `shadow-md` | `0 4px 16px rgba(0,0,0,0.10)` | Cards hover, dropdowns |
| `shadow-lg` | `0 12px 40px rgba(0,0,0,0.14)` | Modals, booking widget |

## Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `--radius-sm` | `4px` | Badges, tags |
| `--radius-md` | `8px` | Cards, inputs |
| `--radius-lg` | `16px` | Panels, overlays |
| `--radius-pill` | `999px` | Pills, floating buttons |

## Component Patterns

- **Booking widget:** navy background, gold CTA button, white inputs, persistent sticky on scroll
- **Room cards:** cream background, serif title, gold price accent, full-bleed image top
- **Section layout:** max-width 1200px, horizontal padding 24px (mobile) / 48px (desktop)
- **Dividers:** 1px gold line or gold ornament (✦) centered
- **CTAs primary:** navy fill, white text, gold hover
- **CTAs secondary:** gold outline, navy text, gold fill hover
- **Trust badges:** white pill on navy/cream, icon + short label
- **Tags:** uppercase, 0.75rem, letter-spacing 0.1em, colored background pill

## Motion

- Transitions: `200ms ease` for color/opacity, `300ms ease` for transforms
- Hover lifts: `translateY(-4px)` on cards
- Scroll reveals: `opacity 0 → 1, translateY(20px → 0)` via IntersectionObserver
