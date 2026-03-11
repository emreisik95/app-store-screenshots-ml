---
name: app-store-screenshots
description: Use when building App Store screenshot pages, generating exportable marketing screenshots for iOS, iPadOS, watchOS, or macOS apps, or creating programmatic screenshot generators with Next.js. Supports multi-language localization for up to 100 languages. Triggers on app store, screenshots, marketing assets, html-to-image, phone mockup, iPad, Apple Watch, Mac, localization, multi-language.
---

# App Store Screenshots Generator

## Overview

Build a Next.js page that renders App Store screenshots as **advertisements** (not UI showcases) and exports them via `html-to-image` at Apple's required resolutions. Supports **iPhone, iPad, Apple Watch, and Mac** with **multi-language localization** for up to 100 languages. Screenshots are the single most important conversion asset on the App Store.

## Core Principle

**Screenshots are advertisements, not documentation.** Every screenshot sells one idea. If you're showing UI, you're doing it wrong — you're selling a *feeling*, an *outcome*, or killing a *pain point*.

## Step 1: Ask the User These Questions

Before writing ANY code, ask the user all of these. Do not proceed until you have answers:

### Auto-Detect Before Asking

Before asking the user anything, scan the project to auto-detect what you can. This reduces questions and shows competence:

#### App Icon Auto-Detection

Search these locations (in order) for the app icon:

```bash
# iOS / React Native / Expo
find . -path "*/AppIcon*" -name "*.png" | head -5
find . -path "*/Images.xcassets/AppIcon*" -name "*.png" | sort -r | head -1
find . -name "app-icon.png" -o -name "appicon.png" -o -name "icon.png" | head -5

# Expo specifically
# Check app.json or app.config.js for "icon" field
cat app.json 2>/dev/null | grep -o '"icon":\s*"[^"]*"'

# Flutter
find . -path "*/mipmap-xxxhdpi/*" -name "*.png" | head -1
find . -name "ic_launcher.png" | head -1

# Web / PWA
find . -name "favicon.png" -o -name "logo.png" -o -name "icon-512.png" | head -5
# Check manifest.json for icon paths
cat public/manifest.json 2>/dev/null | grep -o '"src":\s*"[^"]*"'

# Generic
find . -name "*.png" -path "*/icon*" | head -5
find . -name "*.png" -path "*/logo*" | head -5
```

**Pick the largest icon found** (ideally 1024×1024 for App Store). If multiple candidates exist, prefer:
1. `AppIcon` in `.xcassets` (exact iOS icon)
2. Expo `icon` field from `app.json`
3. Any file named `app-icon.png` or `icon.png` at 512px+
4. Logo files as last resort

**If found**: Tell the user "I found your app icon at `path/to/icon.png` — I'll use this." and move on.
**If not found**: Ask "Where is your app icon PNG?"

#### Brand Colors Auto-Detection

Try to extract brand colors from the project:

```bash
# Check for theme/color config files
find . -name "colors.ts" -o -name "colors.js" -o -name "theme.ts" -o -name "theme.js" | head -5
find . -name "tailwind.config.*" | head -1

# Check for CSS custom properties
grep -r "--color-primary\|--brand\|--accent" --include="*.css" --include="*.scss" -l | head -3

# iOS: check Asset catalog for accent color
find . -path "*/AccentColor*" -name "Contents.json" | head -1

# Expo/RN: check app.json for primaryColor
cat app.json 2>/dev/null | grep -o '"primaryColor":\s*"[^"]*"'
```

Look for primary/accent colors in theme files, Tailwind config (`colors.primary`, `colors.brand`), or CSS variables. Extract hex values.

**If found**: Tell the user "I found your brand colors: primary `#HEXVAL`, accent `#HEXVAL` — I'll use these. Want to adjust?"
**If not found**: Ask "What are your brand colors?"

#### Font Auto-Detection

```bash
# Check layout.tsx or global CSS for font imports
grep -r "next/font\|@font-face\|font-family" --include="*.tsx" --include="*.ts" --include="*.css" src/ | head -5

# Check for Google Fonts imports
grep -r "fonts.googleapis.com\|next/font/google" --include="*.tsx" --include="*.ts" --include="*.html" | head -5

# Check Tailwind config for fontFamily
grep -A5 "fontFamily" tailwind.config.* 2>/dev/null
```

**If found**: Use the project's font. Don't ask.
**If not found**: Ask "What font does your app use?"

### Required

After auto-detection, only ask questions you couldn't answer automatically:

1. **Target platforms** — "Which platforms do you need screenshots for? (iPhone, iPad, Apple Watch, Mac — select all that apply)"
2. **Device model** — "Which device mockup do you want? See the available models below."
3. **App screenshots** — "Where are your app screenshots? (PNG files of actual device captures)"
4. **App icon** — *(auto-detect first — only ask if not found)*
5. **Brand colors** — *(auto-detect first — only ask if not found)*
6. **Font** — *(auto-detect first — only ask if not found)*
7. **Feature list** — "List your app's features in priority order. What's the #1 thing your app does?"
8. **Number of slides** — "How many screenshots do you want? (Apple allows up to 10 per device type)"
9. **Style direction** — "What style do you want? Examples: warm/organic, dark/moody, clean/minimal, bold/colorful, gradient-heavy, flat. Share App Store screenshot references if you have any."

### Available Device Mockups

Present this list to the user when asking about device model:

**iPhone** (transparent-screen frames included with this skill):
| Model | Frame File | Best For |
|-------|-----------|----------|
| iPhone 17 Pro Max | `mockups/iphone/iphone-17-pro-max.png` | Latest flagship |
| iPhone 17 Pro | `mockups/iphone/iphone-17-pro.png` | Latest pro |
| iPhone Air | `mockups/iphone/iphone-air.png` | Latest standard |
| iPhone 16 Pro Max | `mockups/iphone/iphone-16-pro-max.png` | Current flagship |
| iPhone 16 Pro | `mockups/iphone/iphone-16-pro.png` | Current pro |
| iPhone 16 | `mockups/iphone/iphone-16.png` | Current standard |
| iPhone 16 Plus | `mockups/iphone/iphone-16-plus.png` | Current plus |
| iPhone 15 Pro Max | `mockups/iphone/iphone-15-pro-max.png` | Previous flagship |
| iPhone 14 Pro Max | `mockups/iphone/iphone-14-pro-max.png` | Older flagship |
| iPhone 13 mini | `mockups/iphone/iphone-13-mini.png` | Compact |
| Generic (legacy) | `mockup.png` | Fallback (uses overlay) |

**iPad** (transparent-screen frames, portrait and landscape):
| Model | Frame File |
|-------|-----------|
| iPad Pro 13" (M4/M5) Portrait | `mockups/ipad/ipad-pro-13-portrait.png` |
| iPad Pro 13" (M4/M5) Landscape | `mockups/ipad/ipad-pro-13-landscape.png` |
| iPad Pro 11" (1st-4th gen) Portrait | `mockups/ipad/ipad-pro-11-portrait.png` |
| iPad Pro 11" (1st-4th gen) Landscape | `mockups/ipad/ipad-pro-11-landscape.png` |
| iPad Air 13" (M2/M3) Portrait | `mockups/ipad/ipad-air-13-portrait.png` |
| iPad Air 11" (M2/M3) Portrait | `mockups/ipad/ipad-air-11-portrait.png` |
| iPad mini Portrait | `mockups/ipad/ipad-mini-portrait.png` |
| iPad mini Landscape | `mockups/ipad/ipad-mini-landscape.png` |

**Mac** (transparent-screen frames):
| Model | Frame File |
|-------|-----------|
| MacBook Air 13" | `mockups/mac/macbook-air-13.png` |
| MacBook Pro 16" | `mockups/mac/macbook-pro-16.png` |
| iMac 24" | `mockups/mac/imac-24.png` |

**Apple Watch**: No device frames needed — Apple requires watch screenshots without device frames. Export at the exact watch resolution.

10. **Languages** — "Which languages do you need? (e.g., English, German, Japanese, Arabic — up to 100 supported). Provide translations or should I generate them?"

### Optional

11. **Component assets** — "Do you have any UI element PNGs (cards, widgets, etc.) you want as floating decorations? If not, that's fine — we'll skip them."
12. **Additional instructions** — "Any specific requirements, constraints, or preferences?"

### Derived from answers (do NOT ask — decide yourself)

Based on the user's style direction, brand colors, and app aesthetic, decide (see **Step 5: Visual Design Craft** for detailed guidance):
- **Visual personality**: dark & premium, clean & minimal, bold & vivid, warm & organic, or sophisticated & trust
- **Color palette**: derive full palette from brand accent — backgrounds, text hierarchy, glow colors, glass tints
- **Background style**: multi-stop gradient direction/colors, light vs dark base per slide
- **Depth strategy**: glassmorphism, layered shadows, flat borders, or surface color shifts — pick one and commit
- **Decorative elements**: light sources, glass panels, vignettes, noise textures — match the personality
- **Dark vs light slides**: how many of each, which features suit dark treatment (minimum 1 contrast slide per 6)
- **Typography treatment**: weight, tracking, line height, text-shadow — match the brand personality

**IMPORTANT:** If the user gives additional instructions at any point during the process, follow them. User instructions always override skill defaults.

## Step 2: Set Up the Project

### Detect Package Manager

Check what's available, use this priority: **bun > pnpm > yarn > npm**

```bash
# Check in order
which bun && echo "use bun" || which pnpm && echo "use pnpm" || which yarn && echo "use yarn" || echo "use npm"
```

### Scaffold (if no existing Next.js project)

```bash
# With bun:
bunx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
bun add html-to-image jszip

# With pnpm:
pnpx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
pnpm add html-to-image jszip

# With yarn:
yarn create next-app . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
yarn add html-to-image jszip

# With npm:
npx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
npm install html-to-image jszip
```

### Copy Device Mockups

Copy the user's selected device mockup(s) from this skill's `mockups/` directory to the project's `public/mockups/` directory. The mockup files are in subdirectories of the same directory as this SKILL.md file.

```bash
# Example: copy selected iPhone and iPad frames
cp mockups/iphone/iphone-16-pro-max.png public/mockups/
cp mockups/ipad/ipad-pro-13-portrait.png public/mockups/
```

Also copy the legacy `mockup.png` to `public/` if the user selects the generic iPhone frame.

### Copy App Icon

Copy the auto-detected (or user-provided) app icon to the project's `public/` directory:

```bash
# Copy from wherever it was found (auto-detected or user-specified)
cp /path/to/detected/AppIcon.png public/app-icon.png
```

If the icon is larger than 1024×1024, it's fine — it will be displayed at smaller sizes in the screenshots. If it's an `.xcassets` folder, pick the largest PNG inside it.

### File Structure

```
project/
├── public/
│   ├── mockups/                 # Selected device frames
│   │   ├── iphone-16-pro-max.png
│   │   ├── ipad-pro-13-portrait.png
│   │   └── ...
│   ├── mockup.png               # Legacy generic phone frame (optional)
│   ├── app-icon.png             # User's app icon
│   └── screenshots/             # User's app screenshots
│       ├── home.png
│       ├── feature-1.png
│       └── ...
├── src/app/
│   ├── layout.tsx               # Font setup (multi-script support)
│   └── page.tsx                 # The screenshot generator (single file)
└── package.json
```

**The entire generator is a single `page.tsx` file.** No routing, no extra layouts, no API routes. All translations live inline in `page.tsx` — no separate i18n files needed.

### Font Setup

**Always use the project's own font.** If the project already has a font configured (check `layout.tsx`, `tailwind.config`, or `globals.css`), use it. If the user specified a font in Step 1, use that. Only fall back to Inter if the project has no font preference.

For single-language projects:

```tsx
// src/app/layout.tsx
import { ProjectFont } from "next/font/google"; // The user's actual brand font
const font = ProjectFont({ subsets: ["latin"], weight: ["400", "600", "700"] });

export default function Layout({ children }: { children: React.ReactNode }) {
  return <html><body className={font.className}>{children}</body></html>;
}
```

For multi-language projects, the project font remains primary for all Latin-script languages. Import additional Noto Sans variants only for non-Latin scripts — see **Step 5: Localization** for the full font map.

## Step 3: Plan the Slides

### Screenshot Framework (Narrative Arc)

Adapt this framework to the user's requested slide count. Not all slots are required — pick what fits:

| Slot | Purpose | Notes |
|------|---------|-------|
| #1 | **Hero / Main Benefit** | App icon + tagline + home screen. This is the ONLY one most people see. |
| #2 | **Differentiator** | What makes this app unique vs competitors |
| #3 | **Ecosystem** | Widgets, extensions, watch, iPad — beyond the main app. Skip if N/A. |
| #4+ | **Core Features** | One feature per slide, most important first |
| 2nd to last | **Trust Signal** | Identity/craft — "made for people who [X]" |
| Last | **More Features** | Pills listing extras + coming soon. Skip if few features. |

**Rules:**
- Each slide sells ONE idea. Never two features on one slide.
- Vary layouts across slides — never repeat the same template structure.
- Include 1-2 contrast slides (inverted bg) for visual rhythm.

### Multi-Device Considerations

When building for multiple platforms, each platform gets its OWN set of screenshots:
- **iPhone screenshots** should focus on mobile-first features
- **iPad screenshots** can highlight multitasking, split view, larger canvas
- **Apple Watch screenshots** should be minimal — one glanceable piece of info per slide
- **Mac screenshots** can show power-user features, keyboard shortcuts, menu bars

## Step 4: Write Copy FIRST

Get all headlines approved before building layouts. Bad copy ruins good design.

### The Iron Rules

1. **One idea per headline.** Never join two things with "and."
2. **Short, common words.** 1-2 syllables. No jargon unless it's domain-specific.
3. **3-5 words per line.** Must be readable at thumbnail size in the App Store.
4. **Line breaks are intentional.** Control where lines break with `<br />`.

### Three Approaches (pick one per slide)

| Type | What it does | Example |
|------|-------------|---------|
| **Paint a moment** | You picture yourself doing it | "Check your coffee without opening the app." |
| **State an outcome** | What your life looks like after | "A home for every coffee you buy." |
| **Kill a pain** | Name a problem and destroy it | "Never waste a great bag of coffee." |

### What NEVER Works

- **Feature lists as headlines**: "Log every item with tags, categories, and notes"
- **Two ideas joined by "and"**: "Track X and never miss Y"
- **Compound clauses**: "Save and customize X for every Y you own"
- **Vague aspirational**: "Every item, tracked"
- **Marketing buzzwords**: "AI-powered tips" (unless it's actually AI)

### Copy Process

1. Write 3 options per slide using the three approaches
2. Read each at arm's length — if you can't parse it in 1 second, it's too complex
3. Check: does each line have 3-5 words? If not, adjust line breaks
4. Present options to the user with reasoning for each

### Reference Apps for Copy Style

- **Raycast** — specific, descriptive, one concrete value per slide
- **Turf** — ultra-simple action verbs, conversational
- **Mela / Notion** — warm, minimal, elegant

## Step 5: Visual Design Craft

This section teaches you to build premium visual treatments that make screenshots feel like they were designed by a top-tier agency. These techniques are all **html-to-image compatible** — no backdrop-filter, no CSS blend modes that break during export.

### Choose a Visual Personality

Before designing slides, commit to a visual direction. Match it to the app's personality:

| Personality | Background | Depth | Feeling |
|-------------|-----------|-------|---------|
| **Dark & Premium** | Deep navy/emerald/charcoal, multi-stop gradients | Glass panels, layered shadows, light sources | Stripe, Linear, pro tools |
| **Clean & Minimal** | White/cream (#F8F7F4), subtle tinted radials | Single subtle shadows, thin borders | Notion, Mela, lifestyle apps |
| **Bold & Vivid** | Saturated gradients, high contrast | Hard shadows, sharp edges, no glass | Fitness apps, games, youth |
| **Warm & Organic** | Warm cream/peach, soft blobs | Soft shadows, rounded everything | Food apps, wellness, social |
| **Sophisticated & Trust** | Cool slate/blue-gray, layered surfaces | Layered shadows, surface color shifts | Finance, health, enterprise |

**Pick one. Commit. Don't mix.** A dark glassmorphism slide next to a flat minimal slide looks broken, not eclectic.

### Color Palette Derivation

Start with the user's brand accent color and derive a full palette:

```typescript
// Given: brand accent (e.g., emerald #10B981)
const PALETTE = {
  // Dark slide backgrounds — desaturate + darken the accent
  darkBg: "linear-gradient(170deg, #0A0F1E, #041F17, #0D3D2E)",  // navy → deep accent
  darkBgAlt: "linear-gradient(165deg, #0A1628, #0E3B3B, #0D2D2D)", // cooler variant

  // Light slide backgrounds — warm neutral + faint accent radial
  lightBg: "#F8F7F4",  // warm white (never pure #FFFFFF)
  lightAccentGlow: "radial-gradient(ellipse at 60% 40%, rgba(16,185,129,0.06), transparent 70%)",

  // Text — 4-level contrast hierarchy
  textPrimary: "#FFFFFF",       // dark slides
  textSecondary: "rgba(255,255,255,0.7)",
  textMuted: "rgba(255,255,255,0.5)",
  textFaint: "rgba(255,255,255,0.3)",

  // Light slide text
  lightTextPrimary: "#111827",
  lightTextSecondary: "#6B7280",

  // Glow/light source colors — accent at varying opacities
  glowPrimary: "rgba(16,185,129,0.25)",   // strongest
  glowSecondary: "rgba(16,185,129,0.15)", // medium
  glowTertiary: "rgba(16,185,129,0.08)",  // ambient

  // Glass tint — accent at near-invisible opacity
  glassTint: "rgba(16,185,129,0.06)",
};
```

#### Programmatic Palette from Any Accent

Don't hardcode emerald. Derive the palette from any brand color using HSL:

```typescript
function hexToHSL(hex: string): [number, number, number] {
  const r = parseInt(hex.slice(1, 3), 16) / 255;
  const g = parseInt(hex.slice(3, 5), 16) / 255;
  const b = parseInt(hex.slice(5, 7), 16) / 255;
  const max = Math.max(r, g, b), min = Math.min(r, g, b);
  let h = 0, s = 0;
  const l = (max + min) / 2;
  if (max !== min) {
    const d = max - min;
    s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
    if (max === r) h = ((g - b) / d + (g < b ? 6 : 0)) / 6;
    else if (max === g) h = ((b - r) / d + 2) / 6;
    else h = ((r - g) / d + 4) / 6;
  }
  return [Math.round(h * 360), Math.round(s * 100), Math.round(l * 100)];
}

function derivePalette(accentHex: string) {
  const [h, s, l] = hexToHSL(accentHex);
  return {
    darkBg: `linear-gradient(170deg, hsl(${h + 10}, ${Math.min(s, 20)}%, 7%) 0%, hsl(${h}, ${Math.min(s, 30)}%, 12%) 50%, hsl(${h - 5}, ${Math.min(s, 25)}%, 15%) 100%)`,
    lightBg: "#F8F7F4",
    glowPrimary: `hsla(${h}, ${s}%, ${l}%, 0.25)`,
    glowSecondary: `hsla(${h}, ${s}%, ${l}%, 0.15)`,
    glowTertiary: `hsla(${h}, ${s}%, ${l}%, 0.08)`,
    glassTint: `hsla(${h}, ${s}%, ${l}%, 0.06)`,
    labelColor: `hsl(${h}, ${Math.min(s, 70)}%, 75%)`,
  };
}
```

**Rules:**
- Dark backgrounds are NEVER pure black — always tinted toward the brand color
- Light backgrounds are NEVER pure white — use warm whites (#F8F7F4, #FAFAF8) or cool whites (#F8FAFC)
- Each slide should have a slightly different color temperature for variety
- Label colors should use a lighter tint of the accent (e.g., #6EE7B7 or #A7F3D0 for emerald)

### Glassmorphism (html-to-image Compatible)

`backdrop-filter: blur()` does NOT work in html-to-image exports. Simulate frosted glass with these layered techniques:

#### Glass Panel Component

```typescript
// Multi-stop gradient frost (simulates blur)
const glassBg = mode === "dark"
  ? "linear-gradient(135deg, rgba(255,255,255,0.12) 0%, rgba(255,255,255,0.06) 25%, rgba(255,255,255,0.03) 50%, rgba(255,255,255,0.08) 75%, rgba(255,255,255,0.14) 100%)"
  : "linear-gradient(135deg, rgba(255,255,255,0.85) 0%, rgba(255,255,255,0.70) 50%, rgba(255,255,255,0.80) 100%)";

// 8-layer inset box-shadow border simulation
const glassShadow = [
  "inset 0 1px 0 rgba(255,255,255,0.25)",     // top edge (brightest)
  "inset 1px 0 0 rgba(255,255,255,0.10)",      // left edge
  "inset 0 -1px 0 rgba(255,255,255,0.04)",     // bottom edge (subtle)
  "inset -1px 0 0 rgba(255,255,255,0.04)",     // right edge (subtle)
  "inset 0 0 20px -5px rgba(255,255,255,0.10)",// inner glow
  "0 1px 2px rgba(0,0,0,0.10)",                // tight shadow
  "0 4px 16px rgba(0,0,0,0.08)",               // medium shadow
  "0 12px 48px rgba(0,0,0,0.12)",              // ambient shadow
].join(", ");
```

#### Glass Panel Layers (inside-out)

1. **Background**: Multi-stop gradient (not flat rgba)
2. **Accent tint**: Brand color at 4-8% opacity, also as gradient
3. **Noise texture**: SVG feTurbulence as data-URI background — adds grain
4. **Top-edge shine**: 1px gradient line across top (transparent → white → transparent)
5. **Content**: The actual text/elements on top

```tsx
// SVG noise texture overlay (inline data URI)
const noiseTexture = `url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E")`;

// Apply as absolute-positioned overlay at low opacity (0.06-0.12)
// NOTE: Do NOT use mixBlendMode — it breaks in html-to-image.
// Simply use opacity alone — the SVG noise at low opacity creates sufficient grain.
```

**Do NOT use `border:` on glass panels.** The inset box-shadow approach looks far better — it creates a natural edge fade rather than a hard line.

#### Complete GlassPanel Component

```tsx
function GlassPanel({ mode = "dark", accentColor = "rgba(16,185,129,0.06)", children, style, className = "" }: {
  mode?: "dark" | "light";
  accentColor?: string;
  children: React.ReactNode;
  style?: React.CSSProperties;
  className?: string;
}) {
  const bg = mode === "dark"
    ? "linear-gradient(135deg, rgba(255,255,255,0.12) 0%, rgba(255,255,255,0.06) 25%, rgba(255,255,255,0.03) 50%, rgba(255,255,255,0.08) 75%, rgba(255,255,255,0.14) 100%)"
    : "linear-gradient(135deg, rgba(255,255,255,0.85) 0%, rgba(255,255,255,0.70) 50%, rgba(255,255,255,0.80) 100%)";

  const shadow = mode === "dark"
    ? [
        "inset 0 1px 0 rgba(255,255,255,0.25)",
        "inset 1px 0 0 rgba(255,255,255,0.10)",
        "inset 0 -1px 0 rgba(255,255,255,0.04)",
        "inset -1px 0 0 rgba(255,255,255,0.04)",
        "inset 0 0 20px -5px rgba(255,255,255,0.10)",
        "0 1px 2px rgba(0,0,0,0.10)",
        "0 4px 16px rgba(0,0,0,0.08)",
        "0 12px 48px rgba(0,0,0,0.12)",
      ].join(", ")
    : [
        "inset 0 1px 0 rgba(255,255,255,0.60)",
        "0 0 0 0.5px rgba(0,0,0,0.05)",
        "0 1px 2px rgba(0,0,0,0.04)",
        "0 4px 8px rgba(0,0,0,0.03)",
      ].join(", ");

  return (
    <div className={className} style={{ position: "relative", overflow: "hidden", borderRadius: 20, ...style }}>
      {/* Glass background */}
      <div style={{ position: "absolute", inset: 0, background: bg, boxShadow: shadow }} />
      {/* Accent tint */}
      <div style={{ position: "absolute", inset: 0, background: accentColor }} />
      {/* Noise texture */}
      <div style={{
        position: "absolute", inset: 0, opacity: 0.08,
        backgroundImage: `url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E")`,
        backgroundSize: "150px 150px",
      }} />
      {/* Top-edge shine */}
      <div style={{
        position: "absolute", top: 0, left: "10%", right: "10%", height: 1,
        background: "linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent)",
      }} />
      {/* Content */}
      <div style={{ position: "relative", zIndex: 1 }}>{children}</div>
    </div>
  );
}
```

### Depth Techniques

#### Layered Shadows (Stripe-style)

For cards, pills, floating elements — create realistic depth with multiple shadow layers:

```typescript
// Light mode — subtle lift
const cardShadowLight = [
  "0 0 0 0.5px rgba(0,0,0,0.05)",  // hairline border
  "0 1px 2px rgba(0,0,0,0.04)",     // contact shadow
  "0 2px 4px rgba(0,0,0,0.03)",     // near shadow
  "0 4px 8px rgba(0,0,0,0.02)",     // ambient
].join(", ");

// Dark mode — glow + depth
const cardShadowDark = [
  "inset 0 1px 0 rgba(255,255,255,0.10)",  // top shine
  "0 1px 2px rgba(0,0,0,0.20)",
  "0 4px 16px rgba(0,0,0,0.15)",
  "0 12px 48px rgba(0,0,0,0.20)",
].join(", ");

// Feature pills — tinted glass
const pillShadow = [
  "inset 0 1px 0 rgba(255,255,255,0.15)",
  "0 1px 3px rgba(0,0,0,0.12)",
  "0 2px 8px rgba(0,0,0,0.08)",
].join(", ");
```

#### Light Sources (Organic Shapes)

Light sources create ambient glow behind glass panels and devices. Make them organic — not perfect circles:

```tsx
function LightSource({ color, size, top, left, opacity, blur }: {
  color: string; size: number; top: string; left: string;
  opacity: number; blur: number;
}) {
  return (
    <div style={{
      position: "absolute",
      width: size,
      height: size * 0.92,                        // slightly squashed
      borderRadius: "45% 55% 50% 50%",            // organic shape
      background: `radial-gradient(ellipse at 40% 40%, ${color}, transparent 70%)`,
      top, left,
      filter: `blur(${blur}px)`,
      opacity,
      zIndex: 0,
    }} />
  );
}
```

**Light source rules:**
- Every dark slide needs 2-3 light sources minimum
- Position them off-center — behind where the glass panel or device sits
- Vary color temperature: primary accent, lighter tint, complementary
- Use large blur values (80-150px) for ambient, smaller (40-60px) for focused
- Opacity between 0.08 (ambient) and 0.25 (primary) — too bright kills the mood

#### Vignette Overlay

Vignette draws focus to the center and adds cinematic depth. Apply to all dark slides:

```typescript
const vignette = {
  position: "absolute", inset: 0, zIndex: 2, pointerEvents: "none",
  background: "radial-gradient(ellipse 75% 70% at 50% 45%, transparent 30%, rgba(0,0,0,0.30) 100%)",
};
```

Adjust the 75%/70% ellipse size per slide — tighter for focused slides, wider for panoramic ones.

#### Screen Reflection Overlay

Add a subtle diagonal shine across the device screen for realism:

```typescript
// Place inside the DeviceMockup component, above the screenshot
const screenReflection = {
  position: "absolute" as const,
  left: `${scrL}%`, top: `${scrT}%`,
  width: `${scrW}%`, height: `${scrH}%`,
  background: "linear-gradient(135deg, transparent 40%, rgba(255,255,255,0.06) 50%, transparent 60%)",
  zIndex: 3, // above frame
  pointerEvents: "none" as const,
};
```

Use sparingly — opacity above 0.08 makes it look fake.

#### Device Glow

Add a radial glow beneath the device mockup to make it float:

```typescript
// Glow div placed behind the phone/device
const deviceGlow = {
  position: "absolute",
  width: "40%",
  height: "20%",
  bottom: "8%",
  left: "50%",
  transform: "translateX(-50%)",
  background: "radial-gradient(ellipse, rgba(16,185,129,0.18), transparent 70%)",
  filter: "blur(40px)",
  zIndex: 0,
};
```

### Typography Craft

#### Weight & Tracking System

| Element | Font Size | Weight | Letter-Spacing | Line Height |
|---------|-----------|--------|----------------|-------------|
| Hero headline | `W * 0.10` | 700 | `-0.03em` | 0.92 |
| Slide headline | `W * 0.09` | 700 | `-0.02em` | 1.0–1.05 |
| Subheadline | `W * 0.038` | 500 | `0em` | 1.3 |
| Category label | `W * 0.028` | 600 | `0.08em` | default |
| Feature pill | `W * 0.028` | 600 | `0.02em` | default |
| Coming soon pill | `W * 0.024` | 500 | `0.02em` | default |

**Subheadlines** are optional supporting text below the main headline. Use them sparingly — most slides are stronger with just a label + headline. Use subheadlines only when the headline alone doesn't convey enough context (e.g., "Share a link. Start planning instantly.").

**Text positioning safe zones:**
- Top caption: `top: 5-8%`, `left/right: 6-8%` from edges
- Never place text in the bottom 30% — that's where the device sits
- Keep at least `W * 0.06` padding from left/right edges

**Key rules:**
- **Headlines get negative tracking** — tighter letters feel premium and confident
- **Labels get positive tracking** — wider letters feel organized and structured
- **Line height < 1.0 for hero** — lines should almost touch for dramatic impact
- **Never go below 0.92 line height** — text becomes illegible

#### Text Shadow for Depth

On dark backgrounds, headlines benefit from a subtle glow + shadow:

```typescript
const headlineTextShadow = "0 0 40px rgba(255,255,255,0.15), 0 2px 8px rgba(0,0,0,0.3)";
```

The white glow creates a subtle halo (premium feel), and the black shadow adds separation from the background. Only apply to white/light text on dark backgrounds.

### Background Composition

#### Multi-Stop Gradients

Never use 2-color gradients — they look flat. Always use 3+ stops:

```typescript
// Bad: flat gradient
"linear-gradient(180deg, #0A0F1E, #064E3B)"

// Good: dimensional gradient with color curve
"linear-gradient(170deg, #0A0F1E 0%, #041F17 40%, #064E3B 70%, #0D3D2E 100%)"
```

**Vary the angle** per slide: 165deg, 170deg, 175deg — subtle differences create variety.

**Prevent gradient banding**: Dark gradients with subtle color shifts can show visible banding in PNG exports. Combat this by adding the SVG noise texture overlay (from the Glass Panel section) at 0.03-0.06 opacity over the entire slide background. The grain breaks up smooth gradients and prevents banding artifacts.

#### Bottom Fade (Phone Bleed)

When the phone extends past the bottom edge, add a fade so it doesn't look clipped:

```typescript
const bottomFade = {
  position: "absolute",
  bottom: 0, left: 0, right: 0,
  height: "5%",
  background: "linear-gradient(to bottom, transparent 0%, <slide-bg-color> 100%)",
  zIndex: 5, // above phone, below nothing
};
```

Match the fade color to the slide's dominant bottom-edge background color.

### Visual Rhythm Across Slides

**Slide contrast pattern** — alternate moods to keep the eye engaged:

| Slide | Mood | Example |
|-------|------|---------|
| 1 | Dark (hero) | Deep navy, dramatic glow |
| 2 | Light (contrast) | Warm white, clean |
| 3 | Dark (deep) | Emerald tones, moody |
| 4 | Dark (cool) | Teal/cyan shift, differentiated |
| 5 | Dark (navy) | Back to navy, different layout |
| 6 | Dark (features) | Navy, glass card with pills |

**Rules:**
- At least 1 light slide per 6 slides (usually slide 2 or 3)
- No two adjacent slides should share the same gradient direction
- Vary phone position every slide (center → right → center → two-phone → right → no-phone)
- Each dark slide should have a different color temperature (navy, emerald, teal, charcoal)
- Label colors should differ between slides that share a background mood — use accent tints (#6EE7B7, #67E8F9, #A7F3D0)

### What NOT to Use (html-to-image Breaks)

These CSS features look great in the browser but **break or disappear** during html-to-image export:

| Feature | Why it breaks | Alternative |
|---------|--------------|-------------|
| `backdrop-filter: blur()` | Not supported in SVG serialization | Multi-stop gradient frost |
| `background-clip: text` | Renders as invisible text | Use solid text color + text-shadow |
| `mix-blend-mode` on complex elements | Inconsistent rendering | Use opacity + layering instead |
| CSS `filter: blur()` on text | Sometimes renders blank | Use text-shadow for glow effects |
| CSS `filter: blur()` on decorative divs | Works in most browsers but can fail in Safari SVG serialization | Test exports; if glow divs disappear, replace with larger `radial-gradient` at lower opacity |
| `clip-path` with complex shapes | Partial support | Use border-radius + overflow hidden |
| Web fonts not in layout.tsx | Renders as system font | Always register fonts via next/font |
| `position: fixed` elements | Captured at wrong position | Use `position: absolute` |
| CSS animations/transitions | Captures mid-state or blank | Set final state, no transitions |

**Always test every visual effect by exporting.** If it looks good in the browser but bad in the PNG, it's useless.

## Step 6: Localization

### Translations Data Structure

All slide copy is driven by a `translations` object. Write copy in the primary language first (Step 4), then translate. The structure maps locale codes to per-slide strings:

```typescript
type SlideStrings = {
  label: string;     // Category label (e.g., "TRACKING")
  headline: string;  // Main headline (HTML — use <br /> for line breaks)
};

type Translations = {
  [locale: string]: {
    slides: SlideStrings[];
    // Optional: "More Features" slide
    moreFeatures?: {
      headline: string;
      pills: string[];
      comingSoonPills?: string[];
    };
  };
};

const translations: Translations = {
  en: {
    slides: [
      { label: "YOUR COFFEE", headline: "A home for<br />every bean" },
      { label: "FRESHNESS",   headline: "Never waste<br />a great bag" },
      // ... one entry per slide
    ],
    moreFeatures: {
      headline: "And so much more.",
      pills: ["Widgets", "Apple Watch", "Shortcuts"],
      comingSoonPills: ["iPad app"],
    },
  },
  de: {
    slides: [
      { label: "DEIN KAFFEE", headline: "Ein Zuhause<br />für jede Bohne" },
      { label: "FRISCHE",     headline: "Nie wieder<br />guten Kaffee verschwenden" },
    ],
    moreFeatures: {
      headline: "Und so viel mehr.",
      pills: ["Widgets", "Apple Watch", "Kurzbefehle"],
      comingSoonPills: ["iPad-App"],
    },
  },
  ja: {
    slides: [
      { label: "あなたのコーヒー", headline: "すべての豆に<br />居場所を" },
      { label: "鮮度",            headline: "最高の一杯を<br />無駄にしない" },
    ],
  },
  ar: {
    slides: [
      { label: "قهوتك", headline: "مكان لكل<br />حبة بن" },
      { label: "الطازجة", headline: "لا تهدر أبداً<br />كيساً رائعاً" },
    ],
  },
  // ... up to 100 languages
};
```

### Apple App Store Supported Localizations

Apple supports these locale codes in App Store Connect. Use these exact codes as keys:

```typescript
const APP_STORE_LOCALES = [
  // Major markets
  'en-US', 'en-GB', 'en-AU', 'en-CA',
  'zh-Hans', 'zh-Hant',  // Chinese Simplified / Traditional
  'ja', 'ko',
  'fr', 'fr-CA', 'de', 'es', 'es-MX', 'pt-BR', 'pt-PT',
  'it', 'nl', 'sv', 'da', 'fi', 'no',
  'ru', 'pl', 'cs', 'sk', 'hu', 'ro', 'hr', 'uk',
  'tr', 'ar', 'he', 'th', 'vi', 'id', 'ms',
  'el', 'bg', 'ca', 'hi',
  // Additional
  'kk', 'az', 'ka', 'hy', 'km', 'lo', 'my', 'ne',
  'si', 'ta', 'te', 'ml', 'kn', 'mr', 'gu', 'pa', 'bn',
  'or', 'as', 'ur', 'sw', 'am', 'zu',
  'fil', 'mn', 'bo', 'ug',
] as const;
```

You can use simplified codes (e.g., `en` instead of `en-US`) internally and map to Apple's codes at export time. When the user provides only `en`, assume `en-US`.

### Translation Guidelines

**DO translate:**
- Labels and headlines (the advertising copy)
- "More Features" pill text
- "Coming Soon" text

**Do NOT translate:**
- Brand names and app names
- Technical terms that are universally understood in that market
- Feature names if they're proper nouns

**Translation quality rules:**
1. **Never machine-translate blindly.** Headlines are advertising copy — they need a native speaker's feel.
2. **Re-apply the Iron Rules per language.** A 3-word English headline may become 5 words in German — adjust line breaks.
3. **Respect character width.** CJK characters are ~2x wider than Latin. A 5-word English headline may need only 3-4 CJK characters.
4. **Keep the same emotional tone**, not a literal translation. "Never waste a great bag" → German shouldn't be a word-for-word translation but should evoke the same feeling.
5. **Test line breaks in every language.** Use `<br />` intentionally — don't let text wrap randomly.

### Font Handling for Different Scripts

**The project's brand font is always the primary font.** Use the font the user specified in Step 1 (question #6) for ALL Latin-script locales. Only fall back to Noto Sans variants for non-Latin scripts that the brand font cannot render.

```tsx
// src/app/layout.tsx
// 1. ALWAYS import the user's project font first — this is the primary font
// Use next/font/google for Google Fonts, or next/font/local for custom fonts
import { Inter } from "next/font/google";  // ← Replace with user's actual font

// 2. Only import Noto variants for non-Latin scripts the user selected
import { Noto_Sans_JP } from "next/font/google";             // Japanese
import { Noto_Sans_KR } from "next/font/google";             // Korean
import { Noto_Sans_SC } from "next/font/google";             // Chinese Simplified
import { Noto_Sans_TC } from "next/font/google";             // Chinese Traditional
import { Noto_Sans_Arabic } from "next/font/google";         // Arabic
import { Noto_Sans_Hebrew } from "next/font/google";         // Hebrew
import { Noto_Sans_Thai } from "next/font/google";           // Thai
import { Noto_Sans_Devanagari } from "next/font/google";     // Hindi

// Project font — used for ALL Latin-script languages
const projectFont = Inter({ subsets: ["latin"], weight: ["400", "600", "700"] });

// Non-Latin script fonts (only import what's needed)
const notoJP = Noto_Sans_JP({ subsets: ["latin"], weight: ["400", "600", "700"] });
const notoKR = Noto_Sans_KR({ subsets: ["latin"], weight: ["400", "600", "700"] });
const notoSC = Noto_Sans_SC({ subsets: ["latin"], weight: ["400", "600", "700"] });
const notoTC = Noto_Sans_TC({ subsets: ["latin"], weight: ["400", "600", "700"] });
const notoAR = Noto_Sans_Arabic({ subsets: ["arabic"], weight: ["400", "600", "700"] });
const notoHE = Noto_Sans_Hebrew({ subsets: ["hebrew"], weight: ["400", "600", "700"] });
const notoTH = Noto_Sans_Thai({ subsets: ["thai"], weight: ["400", "600", "700"] });
const notoDV = Noto_Sans_Devanagari({ subsets: ["devanagari"], weight: ["400", "600", "700"] });

// Map ONLY non-Latin locales to their script-specific font
// Everything else automatically uses the project font
export const LOCALE_FONTS: Record<string, string> = {
  ja: notoJP.className,
  ko: notoKR.className,
  'zh-Hans': notoSC.className,
  'zh-Hant': notoTC.className,
  ar: notoAR.className,
  he: notoHE.className,
  ur: notoAR.className,    // Urdu uses Arabic script
  th: notoTH.className,
  hi: notoDV.className,
  ne: notoDV.className,
  mr: notoDV.className,
};

// DEFAULT_FONT = project font, NOT Inter or any generic fallback
export const DEFAULT_FONT = projectFont.className;
```

**Font priority rules:**
1. **Project font is king.** English, German, French, Spanish, Portuguese, Italian, Dutch, Swedish, Turkish, Polish, Czech, Romanian — ALL Latin-script languages use the project's brand font.
2. **Non-Latin scripts only** get Noto Sans overrides — Japanese, Korean, Chinese, Arabic, Hebrew, Thai, Hindi, etc.
3. **Only import Noto variants for languages the user actually selected.** Don't import `Noto_Sans_JP` if Japanese isn't in the language list.
4. **If the project uses a local/custom font** (e.g., loaded via `next/font/local`), use that as `projectFont` instead of a Google Font import.
5. **If the project has no specific font**, then default to Inter as the fallback.

### RTL (Right-to-Left) Support

Arabic (`ar`) and Hebrew (`he`) require mirrored layouts:

```typescript
const RTL_LOCALES = new Set(['ar', 'he', 'ur']);

function isRTL(locale: string): boolean {
  return RTL_LOCALES.has(locale.split('-')[0]);
}
```

When rendering an RTL locale:
1. Set `dir="rtl"` on the slide container
2. Mirror text alignment: `text-left` → `text-right`
3. Mirror phone/device placement: if phone was on the right, move it to the left
4. Keep decorative elements — they don't need mirroring
5. **Do NOT mirror the device mockup itself** — phones look the same in all locales

```tsx
function SlideContainer({ locale, children }: { locale: string; children: React.ReactNode }) {
  const rtl = isRTL(locale);
  return (
    <div
      dir={rtl ? 'rtl' : 'ltr'}
      style={{
        width: W, height: H,
        textAlign: rtl ? 'right' : 'left',
      }}
    >
      {children}
    </div>
  );
}
```

### Dynamic Font Sizing for Text Overflow

Different languages produce different text lengths. German is ~30% longer than English. CJK is ~40% shorter in character count but wider per character.

```typescript
// Scale factor per locale — adjust headline font size
const LOCALE_FONT_SCALE: Record<string, number> = {
  de: 0.85,    // German: longer words
  fr: 0.90,    // French: slightly longer
  it: 0.90,    // Italian: slightly longer
  es: 0.90,    // Spanish: slightly longer
  pt: 0.88,    // Portuguese: longer
  ru: 0.85,    // Russian: longer words
  pl: 0.85,    // Polish: longer words
  nl: 0.88,    // Dutch: longer words
  ja: 0.95,    // Japanese: fewer chars but wider
  ko: 0.95,    // Korean: similar to Japanese
  'zh-Hans': 1.0, // Chinese: compact
  'zh-Hant': 1.0, // Chinese Traditional: compact
  ar: 0.90,    // Arabic: slightly longer
  th: 0.90,    // Thai: slightly longer
  hi: 0.88,    // Hindi: longer
  // Default: 1.0 (English and unlisted languages)
};

function getHeadlineSize(locale: string, baseSize: number): number {
  const scale = LOCALE_FONT_SCALE[locale.split('-')[0]] ?? 1.0;
  return Math.round(baseSize * scale);
}
```

## Step 7: Build the Page (see Step 5 for Visual Design, Step 6 for Localization)

### Architecture

**IMPORTANT:** `page.tsx` must start with `"use client"` — it uses `useState`, `useRef`, `useEffect`, `ResizeObserver`, and `html-to-image` which are all client-side APIs.

```
"use client";
page.tsx
├── Constants (W, H, SIZES, DEVICE_CONFIG, design tokens)
├── translations object (all locales × all slides)
├── LOCALE_FONTS map + LOCALE_FONT_SCALE map
├── DeviceMockup component (generic — works for all device types)
├── Caption component (label + headline + locale-aware font/direction)
├── SlideContainer component (dir, font class, text alignment per locale)
├── Decorative components (blobs, glows, shapes)
├── Screenshot1..N components (accept locale prop → read from translations)
├── SCREENSHOTS array (registry)
├── ScreenshotPreview (ResizeObserver scaling + hover export)
├── ExportToolbar (Download Current / Download All → ZIP)
├── exportAsZip() + resizeToBlob() (jszip-based ZIP export)
└── ScreenshotsPage (grid + toolbar + language selector + platform tabs)
```

### Canvas Constants

Define the design canvas size — all slides are designed at this resolution and scaled down for export:

```typescript
// Design at the largest iPhone size — everything scales down from here
const W = 1260;   // canvas width (iPhone 6.9" width)
const H = 2736;   // canvas height (iPhone 6.9" height)
```

For iPad-first projects, use `W = 2064, H = 2752`. For Mac-first, use `W = 2880, H = 1800`. All typography, spacing, and element sizing reference `W` and `H` throughout this document.

### Device Types & Export Sizes

#### iPhone Export Sizes (Apple Required, portrait)

```typescript
const IPHONE_SIZES = [
  { label: '6.9"', w: 1260, h: 2736 },  // iPhone Air, 17 Pro Max, 16 Pro Max, 16 Plus, 15 Pro Max
  { label: '6.5"', w: 1284, h: 2778 },  // iPhone 14 Plus, 13 Pro Max, 12 Pro Max, 11 Pro Max, 11, XR
  { label: '6.5"', w: 1242, h: 2688 },  // iPhone XS Max (alternative)
  { label: '6.3"', w: 1206, h: 2622 },  // iPhone 17 Pro, 16 Pro, 15 Pro (alternative)
  { label: '6.3"', w: 1179, h: 2556 },  // iPhone 17, 16, 15, 14 Pro
  { label: '6.1"', w: 1170, h: 2532 },  // iPhone 17e, 16e, 14, 13, 12, etc.
  { label: '6.1"', w: 1125, h: 2436 },  // iPhone 13 Pro, 12 Pro, X, XS
  { label: '5.5"', w: 1242, h: 2208 },  // iPhone 8 Plus (legacy)
] as const;
```

Design at the LARGEST size (1260x2736 for `6.9"`) and scale down for export. In App Store Connect, the `6.9"` and `6.5"` sizes are mandatory — all others auto-scale from these.

#### iPad Export Sizes (Apple Required)

```typescript
const IPAD_SIZES = [
  { label: '13"',   w: 2064, h: 2752 },  // iPad Pro 13" M5/M4, iPad Air M4/M3/M2 (mandatory)
  { label: '13"',   w: 2048, h: 2732 },  // iPad Pro 6th-1st gen (legacy alternative)
  { label: '11"',   w: 1488, h: 2266 },  // iPad Pro M5/M4, iPad Air M4/M3/M2, iPad mini A17 Pro
  { label: '11"',   w: 1668, h: 2388 },  // iPad Pro 4th-1st gen, iPad Air 5th-4th gen
  { label: '11"',   w: 1640, h: 2360 },  // iPad A16, iPad 10th gen
  { label: '10.5"', w: 1668, h: 2224 },  // iPad Air 3rd gen, older
  { label: '9.7"',  w: 1536, h: 2048 },  // iPad mini older, older iPads
] as const;

// For landscape, swap w and h
```

Design at 2064x2752 (13" portrait) and scale for export. The `13"` size is mandatory — all others auto-scale.

#### Apple Watch Export Sizes

```typescript
const WATCH_SIZES = [
  { label: 'Ultra 3',     w: 422, h: 514 },
  { label: 'Ultra 2',     w: 410, h: 502 },
  { label: 'Series 10-11', w: 416, h: 496 },
  { label: 'Series 7-9',  w: 396, h: 484 },
  { label: 'Series 4-6',  w: 368, h: 448 },
  { label: 'Series 3',    w: 312, h: 390 },
] as const;
```

Apple Watch screenshots do NOT use device frames. Export the UI directly.

#### Mac Export Sizes

```typescript
const MAC_SIZES = [
  { label: 'Retina',  w: 2880, h: 1800 },
  { label: 'Large',   w: 2560, h: 1600 },
  { label: 'Medium',  w: 1440, h: 900  },
  { label: 'Small',   w: 1280, h: 800  },
] as const;
```

### Rendering Strategy

Each screenshot is designed at full resolution. Two copies exist:

1. **Preview**: CSS `transform: scale()` via ResizeObserver to fit a grid card
2. **Export**: Offscreen at `position: absolute; left: -9999px` at true resolution

### Device Mockup Components

#### Device Frame Measurements

All mockup frames (except legacy `mockup.png`) have **transparent screens**. The screenshot is placed behind the device frame so it shows through the transparent screen area.

**iPhone Frame Measurements:**

```typescript
const IPHONE_FRAMES = {
  'iphone-17-pro-max': { w: 1520, h: 3068, scrL: 6.58, scrT: 3.26, scrW: 86.84, scrH: 93.48 },
  'iphone-17-pro':     { w: 1406, h: 2822, scrL: 7.11, scrT: 3.54, scrW: 85.78, scrH: 92.91 },
  'iphone-air':        { w: 1490, h: 2996, scrL: 6.71, scrT: 3.34, scrW: 86.58, scrH: 93.32 },
  'iphone-16-pro-max': { w: 1520, h: 3068, scrL: 6.58, scrT: 3.26, scrW: 86.84, scrH: 93.48 },
  'iphone-16-pro':     { w: 1406, h: 2822, scrL: 7.25, scrT: 3.54, scrW: 85.78, scrH: 92.91 },
  'iphone-16':         { w: 1379, h: 2756, scrL: 7.25, scrT: 3.63, scrW: 85.50, scrH: 92.74 },
  'iphone-16-plus':    { w: 1490, h: 2996, scrL: 6.71, scrT: 3.34, scrW: 86.58, scrH: 93.32 },
  'iphone-15-pro-max': { w: 1490, h: 2996, scrL: 6.64, scrT: 3.34, scrW: 86.58, scrH: 93.32 },
  'iphone-14-pro-max': { w: 1490, h: 2996, scrL: 6.78, scrT: 3.34, scrW: 86.58, scrH: 93.32 },
  'iphone-13-mini':    { w: 1280, h: 2540, scrL: 7.81, scrT: 8.27, scrW: 84.38, scrH: 87.80 },
} as const;
```

**iPad Frame Measurements:**

```typescript
const IPAD_FRAMES = {
  'ipad-pro-13-portrait':  { w: 2264, h: 2952, scrL: 4.42, scrT: 3.39, scrW: 91.17, scrH: 93.22 },
  'ipad-pro-13-landscape': { w: 2952, h: 2264, scrL: 3.39, scrT: 4.42, scrW: 93.22, scrH: 91.17 },
  'ipad-pro-11-portrait':  { w: 1868, h: 2620, scrL: 5.30, scrT: 3.82, scrW: 89.29, scrH: 92.37 },
  'ipad-pro-11-landscape': { w: 2620, h: 1868, scrL: 3.82, scrT: 5.35, scrW: 92.37, scrH: 89.29 },
  'ipad-air-13-portrait':  { w: 2248, h: 2932, scrL: 4.45, scrT: 3.41, scrW: 91.10, scrH: 93.18 },
  'ipad-air-11-portrait':  { w: 1880, h: 2600, scrL: 6.38, scrT: 4.62, scrW: 87.23, scrH: 90.77 },
  'ipad-mini-portrait':    { w: 1888, h: 2666, scrL: 10.59, scrT: 7.54, scrW: 78.81, scrH: 85.00 },
  'ipad-mini-landscape':   { w: 2666, h: 1888, scrL: 7.54, scrT: 10.65, scrW: 85.00, scrH: 78.81 },
} as const;
```

**Mac Frame Measurements:**

```typescript
const MAC_FRAMES = {
  'macbook-air-13': { w: 3260, h: 2164, scrL: 10.74, scrT: 14.14, scrW: 78.53, scrH: 74.31 },
  'macbook-pro-16': { w: 4340, h: 2860, scrL: 10.21, scrT: 13.22, scrW: 79.59, scrH: 75.80 },
  'imac-24':        { w: 4880, h: 5720, scrL: 4.10,  scrT: 27.97, scrW: 91.80, scrH: 44.06 },
} as const;
```

#### DeviceMockup Component (Transparent-Screen Frames)

For all new frames (transparent screens), the screenshot goes BEHIND the frame:

```tsx
function DeviceMockup({ frameSrc, screenSrc, frameId, style, className = "" }: {
  frameSrc: string;
  screenSrc: string;
  frameId: string;
  style?: React.CSSProperties;
  className?: string;
}) {
  // Look up measurements from IPHONE_FRAMES, IPAD_FRAMES, or MAC_FRAMES
  const m = ALL_FRAMES[frameId];
  return (
    <div className={`relative ${className}`}
      style={{ aspectRatio: `${m.w}/${m.h}`, ...style }}>
      {/* Screenshot behind the frame — shows through transparent screen */}
      <img src={screenSrc} alt=""
        className="absolute object-cover object-top"
        style={{
          left: `${m.scrL}%`, top: `${m.scrT}%`,
          width: `${m.scrW}%`, height: `${m.scrH}%`,
          zIndex: 1,
        }}
        draggable={false} />
      {/* Device frame on top */}
      <img src={frameSrc} alt=""
        className="absolute inset-0 w-full h-full"
        style={{ zIndex: 2 }}
        draggable={false} />
    </div>
  );
}
```

#### Legacy Phone Component (Opaque-Screen Frame)

For the original `mockup.png` (opaque black screen), the screenshot goes ON TOP with clipping:

```typescript
const MK_W = 1022;  // mockup image width
const MK_H = 2082;  // mockup image height
const SC_L = (52 / MK_W) * 100;
const SC_T = (46 / MK_H) * 100;
const SC_W = (918 / MK_W) * 100;
const SC_H = (1990 / MK_H) * 100;
const SC_RX = (126 / 918) * 100;
const SC_RY = (126 / 1990) * 100;
```

```tsx
function Phone({ src, alt, style, className = "" }: {
  src: string; alt: string; style?: React.CSSProperties; className?: string;
}) {
  return (
    <div className={`relative ${className}`}
      style={{ aspectRatio: `${MK_W}/${MK_H}`, ...style }}>
      <img src="/mockup.png" alt=""
        className="block w-full h-full" draggable={false} />
      <div className="absolute z-10 overflow-hidden"
        style={{
          left: `${SC_L}%`, top: `${SC_T}%`,
          width: `${SC_W}%`, height: `${SC_H}%`,
          borderRadius: `${SC_RX}% / ${SC_RY}%`,
        }}>
        <img src={src} alt={alt}
          className="block w-full h-full object-cover object-top"
          draggable={false} />
      </div>
    </div>
  );
}
```

#### Apple Watch Component (No Device Frame)

Apple Watch screenshots are shown WITHOUT a device frame. Use a simple rounded rectangle:

```tsx
function WatchScreen({ src, alt, style, className = "" }: {
  src: string; alt: string; style?: React.CSSProperties; className?: string;
}) {
  return (
    <div className={`relative overflow-hidden ${className}`}
      style={{
        aspectRatio: '368/448',
        borderRadius: '22%',
        background: '#000',
        ...style,
      }}>
      <img src={src} alt={alt}
        className="block w-full h-full object-cover"
        draggable={false} />
    </div>
  );
}
```

#### Localized Caption Component

The Caption component reads text from the `translations` object for the active locale:

```tsx
function Caption({ slideIndex, locale, style, className = "" }: {
  slideIndex: number;
  locale: string;
  style?: React.CSSProperties;
  className?: string;
}) {
  const t = translations[locale]?.slides[slideIndex]
         ?? translations['en']?.slides[slideIndex]; // fallback to English
  if (!t) return null;

  const rtl = isRTL(locale);
  const fontClass = LOCALE_FONTS[locale.split('-')[0]] ?? DEFAULT_FONT;
  const headlineSize = getHeadlineSize(locale, W * 0.095);

  return (
    <div
      className={`${fontClass} ${className}`}
      dir={rtl ? 'rtl' : 'ltr'}
      style={{
        textAlign: rtl ? 'right' : 'left',
        ...style,
      }}
    >
      <div style={{
        fontSize: W * 0.028,
        fontWeight: 600,
        textTransform: 'uppercase',
        letterSpacing: '0.05em',
        opacity: 0.7,
        marginBottom: W * 0.015,
      }}>
        {t.label}
      </div>
      <div
        style={{
          fontSize: headlineSize,
          fontWeight: 700,
          lineHeight: 1.0,
        }}
        dangerouslySetInnerHTML={{ __html: t.headline }}
      />
    </div>
  );
}
```

#### Language Selector & Toolbar

Add a language dropdown and "Export All Languages" button to the toolbar:

```tsx
function ScreenshotsToolbar({ locales, activeLocale, onLocaleChange, onExportAll }: {
  locales: string[];
  activeLocale: string;
  onLocaleChange: (locale: string) => void;
  onExportAll: () => void;
}) {
  return (
    <div style={{ display: 'flex', gap: 12, alignItems: 'center', padding: '16px 0' }}>
      <select
        value={activeLocale}
        onChange={(e) => onLocaleChange(e.target.value)}
        style={{ padding: '8px 12px', borderRadius: 8, fontSize: 14 }}
      >
        {locales.map((loc) => (
          <option key={loc} value={loc}>
            {LOCALE_DISPLAY_NAMES[loc] ?? loc}
          </option>
        ))}
      </select>

      <span style={{ fontSize: 13, opacity: 0.5 }}>
        {locales.length} language{locales.length > 1 ? 's' : ''}
      </span>

      <button
        onClick={onExportAll}
        style={{
          marginLeft: 'auto',
          padding: '8px 16px',
          borderRadius: 8,
          background: '#111',
          color: '#fff',
          fontSize: 14,
          cursor: 'pointer',
        }}
      >
        Export All Languages
      </button>
    </div>
  );
}

// Human-readable language names
const LOCALE_DISPLAY_NAMES: Record<string, string> = {
  en: 'English', 'en-US': 'English (US)', 'en-GB': 'English (UK)',
  de: 'Deutsch', fr: 'Français', 'fr-CA': 'Français (Canada)',
  es: 'Español', 'es-MX': 'Español (México)',
  pt: 'Português', 'pt-BR': 'Português (Brasil)',
  it: 'Italiano', nl: 'Nederlands', sv: 'Svenska',
  da: 'Dansk', fi: 'Suomi', no: 'Norsk',
  pl: 'Polski', cs: 'Čeština', sk: 'Slovenčina',
  hu: 'Magyar', ro: 'Română', hr: 'Hrvatski', uk: 'Українська',
  ru: 'Русский', tr: 'Türkçe', el: 'Ελληνικά', bg: 'Български',
  ar: 'العربية', he: 'עברית',
  ja: '日本語', ko: '한국어',
  'zh-Hans': '简体中文', 'zh-Hant': '繁體中文',
  th: 'ไทย', vi: 'Tiếng Việt', id: 'Indonesia', ms: 'Melayu',
  hi: 'हिन्दी', bn: 'বাংলা', ta: 'தமிழ்', te: 'తెలుగు',
  ml: 'മലയാളം', kn: 'ಕನ್ನಡ', mr: 'मराठी', gu: 'ગુજરાતી',
  pa: 'ਪੰਜਾਬੀ', ur: 'اردو', sw: 'Kiswahili', fil: 'Filipino',
  ca: 'Català', ka: 'ქართული', hy: 'Հայերեն', kk: 'Қазақша',
  az: 'Azərbaycan', mn: 'Монгол',
};
```

### Typography (Resolution-Independent)

All sizing relative to canvas width W:

| Element | Size | Weight | Line Height |
|---------|------|--------|-------------|
| Category label | `W * 0.028` | 600 (semibold) | default |
| Headline | `W * 0.09` to `W * 0.1` | 700 (bold) | 1.0 |
| Hero headline | `W * 0.1` | 700 (bold) | 0.92 |

For iPad and Mac screenshots (wider canvases), scale typography down:
- **iPad**: Headline at `W * 0.06` to `W * 0.07`
- **Mac**: Headline at `W * 0.04` to `W * 0.05`
- **Apple Watch**: No text overlays — the watch UI speaks for itself

### Device Placement Patterns

#### iPhone Placement

Vary across slides — NEVER use the same layout twice in a row:

**Centered phone** (hero, single-feature):
```
bottom: 0, width: "82-86%", translateX(-50%) translateY(12-14%)
```

**Two phones layered** (comparison):
```
Back: left: "-8%", width: "65%", rotate(-4deg), opacity: 0.55
Front: right: "-4%", width: "82%", translateY(10%)
```

**Phone + floating elements** (only if user provided component PNGs):
```
Cards should NOT block the phone's main content.
Position at edges, slight rotation (2-5deg), drop shadows.
If distracting, push partially off-screen or make smaller.
```

#### iPad Placement

iPads take up more width — adjust layouts accordingly:

**Centered tablet** (hero):
```
bottom: 0, width: "90-95%", translateY(8-10%)
```

**Angled tablet** (feature showcase):
```
width: "85%", rotate(-3deg) to rotate(3deg), perspective(1200px)
```

**Portrait + Landscape combo** (multitasking showcase):
```
Portrait: left: "5%", width: "45%", translateY(5%)
Landscape: right: "5%", width: "55%", translateY(15%)
```

#### Mac Placement

Mac devices are landscape-oriented — screenshots should be landscape:

**Centered laptop** (hero):
```
bottom: 0, width: "95%", translateY(10-15%)
```

**Floating laptop** (feature):
```
width: "90%", rotate(-2deg), drop-shadow
```

**iMac centered** (desktop app):
```
bottom: 0, width: "80%", translateY(5%)
```

#### Apple Watch Placement

Watch screenshots are small — pair with descriptive text:

**Centered watch** (with large headline):
```
width: "35-40%", centered horizontally, translateY(10%)
```

**Watch + phone combo** (ecosystem slide):
```
Phone: right side, width: "55%"
Watch: left side, width: "25%", translateY(15%)
```

### App Icon Glow (Hero Slide)

For hero slides that feature the app icon, add a glow behind it:

```tsx
function AppIconGlow({ iconSrc, size, glowColor, style }: {
  iconSrc: string; size: number; glowColor: string;
  style?: React.CSSProperties;
}) {
  return (
    <div style={{ position: "relative", width: size, height: size, ...style }}>
      {/* Glow behind icon */}
      <div style={{
        position: "absolute", inset: -size * 0.3,
        borderRadius: "50%",
        background: `radial-gradient(circle, ${glowColor}, transparent 70%)`,
        filter: `blur(${size * 0.25}px)`,
      }} />
      {/* Icon with rounded corners */}
      <img src={iconSrc} alt="" style={{
        position: "relative", zIndex: 1,
        width: size, height: size,
        borderRadius: size * 0.224, // Apple's exact corner radius ratio
      }} draggable={false} />
    </div>
  );
}
```

### Feature Pills (More Features Slide)

Styled pills for the "More Features" slide with glass treatment:

```tsx
function FeaturePill({ text, accentColor = "rgba(16,185,129,0.10)", isComingSoon = false }: {
  text: string; accentColor?: string; isComingSoon?: boolean;
}) {
  return (
    <div style={{
      display: "inline-flex", alignItems: "center", gap: W * 0.012,
      padding: `${W * 0.018}px ${W * 0.03}px`,
      borderRadius: W * 0.02,
      background: isComingSoon ? "rgba(255,255,255,0.04)" : accentColor,
      boxShadow: [
        `inset 0 1px 0 rgba(255,255,255,${isComingSoon ? 0.06 : 0.15})`,
        `0 1px 3px rgba(0,0,0,0.12)`,
        `0 2px 8px rgba(0,0,0,0.08)`,
      ].join(", "),
      fontSize: W * 0.028,
      fontWeight: 600,
      letterSpacing: "0.02em",
      color: isComingSoon ? "rgba(255,255,255,0.4)" : "rgba(255,255,255,0.9)",
    }}>
      {text}
      {isComingSoon && (
        <span style={{ fontSize: W * 0.022, opacity: 0.6, fontWeight: 500 }}>Soon</span>
      )}
    </div>
  );
}
```

### "More Features" Slide (Optional)

Dark/contrast background with app icon, headline ("And so much more."), and feature pills. Can include a "Coming Soon" section with dimmer pills.

## Step 8: Export

### Why html-to-image, NOT html2canvas

`html2canvas` breaks on CSS filters, gradients, drop-shadow, backdrop-filter, and complex clipping. `html-to-image` uses native browser SVG serialization — handles all CSS faithfully.

### Export Implementation

```typescript
import { toPng } from "html-to-image";

// Before capture: move element on-screen
el.style.left = "0px";
el.style.opacity = "1";
el.style.zIndex = "-1";

const opts = { width: W, height: H, pixelRatio: 1, cacheBust: true };

// CRITICAL: Double-call trick — first warms up fonts/images, second produces clean output
await toPng(el, opts);
const dataUrl = await toPng(el, opts);

// After capture: move back off-screen
el.style.left = "-9999px";
el.style.opacity = "";
el.style.zIndex = "";
```

### ZIP Export with Categorized Folders

**All exports download as a single ZIP file.** Never trigger individual file downloads — always bundle into a ZIP with a clear folder hierarchy. Install `jszip` as a required dependency (already added in Step 2).

```bash
# Required dependency — add during project setup
bun add jszip    # or: pnpm add jszip / npm install jszip
```

### ZIP Folder Structure

The ZIP organizes files by **language → device type → size** so the user can drag-and-drop directly into App Store Connect:

```
app-store-screenshots.zip
├── en/
│   ├── iPhone 6.9in/
│   │   ├── 01-hero.png
│   │   ├── 02-freshness.png
│   │   └── ...
│   ├── iPhone 6.5in/
│   │   ├── 01-hero.png
│   │   └── ...
│   ├── iPhone 6.3in/
│   │   └── ...
│   ├── iPhone 6.1in/
│   │   └── ...
│   ├── iPad 13in/
│   │   ├── 01-hero.png
│   │   └── ...
│   ├── iPad 11in/
│   │   └── ...
│   ├── Mac Retina/
│   │   └── ...
│   └── Watch Ultra 3/
│       └── ...
├── de/
│   ├── iPhone 6.9in/
│   │   ├── 01-hero.png
│   │   └── ...
│   └── ...
├── ja/
│   └── ...
└── ar/
    └── ...
```

**Folder naming rules:**
- Language folders use the locale code: `en`, `de`, `ja`, `ar`, `zh-Hans`, etc.
- Device folders use `in` instead of `"` for inches: `iPhone 6.9in`, `iPad 13in` — avoids filesystem issues on Windows
- File names are zero-padded slide index + slug: `01-hero.png`, `02-freshness.png`, etc.
- **No resolution in filename** — the folder already identifies the size, and each folder contains only one resolution
- For single-language projects, skip the language folder: `iPhone 6.9"/01-hero.png`
- For single-device projects, skip the device folder: `en/01-hero.png`
- For single-language AND single-device, flat structure: `01-hero.png`, `02-freshness.png`

### Batch Export Implementation

```typescript
import JSZip from "jszip";
import { toPng } from "html-to-image";

// Device type to folder name mapping
// IMPORTANT: Use inch mark (in) instead of typographic quotes (") in folder names
// to avoid filesystem issues on Windows and in ZIP archives
const DEVICE_FOLDER: Record<string, Record<string, string>> = {
  iphone: {
    '1260x2736': 'iPhone 6.9in',
    '1284x2778': 'iPhone 6.5in',
    '1242x2688': 'iPhone 6.5in',
    '1206x2622': 'iPhone 6.3in',
    '1179x2556': 'iPhone 6.3in',
    '1170x2532': 'iPhone 6.1in',
    '1125x2436': 'iPhone 6.1in',
    '1242x2208': 'iPhone 5.5in',
  },
  ipad: {
    '2064x2752': 'iPad 13in',
    '2048x2732': 'iPad 13in',
    '1488x2266': 'iPad 11in',
    '1668x2388': 'iPad 11in',
    '1640x2360': 'iPad 11in',
    '1668x2224': 'iPad 10.5in',
    '1536x2048': 'iPad 9.7in',
    // Landscape (swap dimensions)
    '2752x2064': 'iPad 13in Landscape',
    '2732x2048': 'iPad 13in Landscape',
    '2266x1488': 'iPad 11in Landscape',
    '2388x1668': 'iPad 11in Landscape',
  },
  watch: {
    '422x514': 'Watch Ultra 3',
    '410x502': 'Watch Ultra 2',
    '416x496': 'Watch Series 10-11',
    '396x484': 'Watch Series 7-9',
    '368x448': 'Watch Series 4-6',
    '312x390': 'Watch Series 3',
  },
  mac: {
    '2880x1800': 'Mac Retina',
    '2560x1600': 'Mac Large',
    '1440x900': 'Mac Medium',
    '1280x800': 'Mac Small',
  },
};

interface ExportConfig {
  locales: string[];
  screenshots: { name: string; ref: HTMLElement | null }[];
  deviceTypes: { type: string; sizes: { label: string; w: number; h: number }[] }[];
}

async function exportAsZip(config: ExportConfig, onProgress: (pct: number) => void) {
  const zip = new JSZip();
  const { locales, screenshots, deviceTypes } = config;
  const isMultiLang = locales.length > 1;
  const isMultiDevice = deviceTypes.length > 1;

  // Count total exports for progress
  const totalSizes = deviceTypes.reduce((sum, dt) => sum + dt.sizes.length, 0);
  const total = locales.length * screenshots.length * totalSizes;
  let done = 0;

  for (const locale of locales) {
    // 1. Switch locale → re-render slides
    setActiveLocale(locale);
    await new Promise((r) => setTimeout(r, 500)); // wait for render + fonts

    for (const { type: deviceType, sizes } of deviceTypes) {
      for (const size of sizes) {
        const sizeKey = `${size.w}x${size.h}`;
        const deviceFolder = DEVICE_FOLDER[deviceType]?.[sizeKey] ?? `${deviceType} ${size.label}`;

        for (let i = 0; i < screenshots.length; i++) {
          const el = screenshots[i].ref;
          if (!el) { done++; continue; }

          // Move on-screen for capture
          el.style.left = "0px";
          el.style.opacity = "1";
          el.style.zIndex = "-1";

          const opts = { width: W, height: H, pixelRatio: 1, cacheBust: true };

          // Double-call trick
          await toPng(el, opts);
          const dataUrl = await toPng(el, opts);

          // Move back off-screen
          el.style.left = "-9999px";
          el.style.opacity = "";
          el.style.zIndex = "";

          // Resize to target dimensions
          const resizedBlob = await resizeToBlob(dataUrl, size.w, size.h);

          // Build ZIP path based on what's multi
          const filename = `${String(i + 1).padStart(2, "0")}-${screenshots[i].name}.png`;
          let zipPath: string;

          if (isMultiLang && isMultiDevice) {
            zipPath = `${locale}/${deviceFolder}/${filename}`;
          } else if (isMultiLang) {
            zipPath = `${locale}/${filename}`;
          } else if (isMultiDevice) {
            zipPath = `${deviceFolder}/${filename}`;
          } else {
            zipPath = filename;
          }

          zip.file(zipPath, resizedBlob);

          done++;
          onProgress(Math.round((done / total) * 100));
          await new Promise((r) => setTimeout(r, 300)); // throttle
        }
      }
    }
  }

  // Generate and download ZIP
  const zipBlob = await zip.generateAsync({
    type: "blob",
    compression: "DEFLATE",
    compressionOptions: { level: 6 },
  });

  const link = document.createElement("a");
  link.href = URL.createObjectURL(zipBlob);
  link.download = "app-store-screenshots.zip";
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
  URL.revokeObjectURL(link.href);
}

// Helper: resize a data URL to target dimensions, return as Blob
async function resizeToBlob(
  dataUrl: string, targetW: number, targetH: number
): Promise<Blob> {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = () => {
      const canvas = document.createElement("canvas");
      canvas.width = targetW;
      canvas.height = targetH;
      const ctx = canvas.getContext("2d")!;
      ctx.drawImage(img, 0, 0, targetW, targetH);
      canvas.toBlob(
        (blob) => {
          if (blob) resolve(blob);
          else reject(new Error(`toBlob returned null for ${targetW}x${targetH}`));
        },
        "image/png"
      );
    };
    img.onerror = () => reject(new Error("Failed to load image for resize"));
    // Timeout safety — if image never loads, reject after 10s
    const timeout = setTimeout(() => reject(new Error("Image load timeout")), 10000);
    img.onload = function () {
      clearTimeout(timeout);
      const canvas = document.createElement("canvas");
      canvas.width = targetW;
      canvas.height = targetH;
      const ctx = canvas.getContext("2d")!;
      ctx.drawImage(img, 0, 0, targetW, targetH);
      canvas.toBlob(
        (blob) => blob ? resolve(blob) : reject(new Error("toBlob returned null")),
        "image/png"
      );
    };
    img.src = dataUrl;
  });
}
```

### Export UI

The toolbar provides two export actions:

```tsx
function ExportToolbar({ onExportCurrent, onExportAll, progress, isExporting }: {
  onExportCurrent: () => void;    // Export current locale + device as ZIP
  onExportAll: () => void;        // Export ALL locales + devices as ZIP
  progress: number;               // 0-100
  isExporting: boolean;
}) {
  return (
    <div className="flex items-center gap-3 p-4">
      <button onClick={onExportCurrent} disabled={isExporting}
        className="px-4 py-2 bg-blue-600 text-white rounded-lg">
        Download Current
      </button>
      <button onClick={onExportAll} disabled={isExporting}
        className="px-4 py-2 bg-green-600 text-white rounded-lg">
        Download All Languages
      </button>
      {isExporting && (
        <div className="flex items-center gap-2">
          <div className="w-48 h-2 bg-gray-200 rounded-full overflow-hidden">
            <div className="h-full bg-blue-600 transition-all"
              style={{ width: `${progress}%` }} />
          </div>
          <span className="text-sm text-gray-600">{progress}%</span>
        </div>
      )}
    </div>
  );
}
```

- **"Download Current"**: Exports only the active locale and selected device type as a ZIP
- **"Download All Languages"**: Exports every locale × every device type × every required size as a ZIP
- Both always produce a `.zip` file — never individual downloads

### Key Rules

- **Always ZIP**: Every export produces a `.zip` file — never trigger individual file downloads. Even a single screenshot exports as a ZIP.
- **Double-call trick**: First `toPng()` loads fonts/images lazily. Second produces clean output. Without this, exports are blank.
- **On-screen for capture**: Temporarily move to `left: 0` before calling `toPng`.
- **Offscreen container**: Use `position: absolute; left: -9999px` (not `fixed`).
- **Resizing**: Load data URL into Image, draw onto canvas at target size via `resizeToBlob()`.
- 300ms delay between sequential exports.
- Set `fontFamily` on the offscreen container — use the project's brand font.
- **Numbered filenames**: Prefix exports with zero-padded index: `01-hero.png`, `02-freshness.png`. Use `String(index + 1).padStart(2, "0")`.
- **Categorized folders inside ZIP**: Language → Device size → Files. Folder depth adjusts based on whether export is multi-language and/or multi-device (see folder structure above).

### Export Robustness

#### Font Embedding

For faster and more reliable exports, pre-embed fonts before the export loop:

```typescript
import { toPng, getFontEmbedCSS } from "html-to-image";

// Pre-compute font CSS once before the loop
const fontCSS = await getFontEmbedCSS(firstSlideElement);

// Use it in all subsequent toPng calls
const opts = { width: W, height: H, pixelRatio: 1, cacheBust: true, fontEmbedCSS: fontCSS };
```

#### Error Handling

Wrap each slide export in try-catch so one failure doesn't kill the entire batch:

```typescript
for (let i = 0; i < screenshots.length; i++) {
  try {
    // ... capture + resize logic ...
    zip.file(zipPath, resizedBlob);
  } catch (err) {
    console.error(`Failed to export slide ${i + 1}:`, err);
    // Continue with next slide — don't abort the entire export
  }
  done++;
  onProgress(Math.round((done / total) * 100));
}
```

#### Locale Switch Race Condition

When switching locales during batch export, wait for BOTH state update AND font loading:

```typescript
// Instead of a fixed 500ms delay:
setActiveLocale(locale);
await new Promise((r) => setTimeout(r, 500));

// Better: wait for render + verify fonts loaded
setActiveLocale(locale);
await new Promise((r) => setTimeout(r, 100)); // let React re-render
await document.fonts.ready;                    // ensure fonts are loaded
await new Promise((r) => setTimeout(r, 200)); // safety buffer for images
```

#### Memory Management for Large Exports

When exporting many languages × devices × slides, the browser can run out of memory:

```typescript
// Process one locale at a time, clear refs between locales
for (const locale of locales) {
  // ... export all slides for this locale ...

  // Release memory between locales
  if (locales.length > 5) {
    await new Promise((r) => setTimeout(r, 1000)); // GC breathing room
  }
}

// Use toBlob instead of toPng to avoid base64 encoding overhead
import { toBlob } from "html-to-image";
const blob = await toBlob(el, opts);
// Note: toBlob returns Blob directly — no base64 intermediate.
// But still needs double-call trick for font warmup.
```

## Apple Compliance Notes

- **No rounded corners on final export**: App Store Connect applies its own rounding — export with square corners
- **No status bars**: Do not include fake status bars in screenshots — Apple rejects these
- **Text-free safe zones**: Keep important text at least 5% from all edges — some devices crop edges slightly
- **No "App Store" branding**: Don't include Apple logos, App Store badges, or "Available on the App Store" text in screenshots
- **No pricing**: Don't show prices — they vary by region
- **Accurate representation**: Screenshots must show actual app functionality — don't show features that don't exist

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| All slides look the same | Vary device position (center, left, right, two-device, no-device) |
| Decorative elements invisible | Increase size and opacity — better too visible than invisible |
| Copy is too complex | "One second at arm's length" test |
| Floating elements block the device | Move off-screen edges or above the device |
| Plain white/black background | Use gradients — even subtle ones add depth |
| Too cluttered | Remove floating elements, simplify to device + caption |
| Too simple/empty | Add larger decorative elements, floating items at edges |
| Headlines use "and" | Split into two slides or pick one idea |
| No visual contrast across slides | Mix light and dark backgrounds |
| Export is blank | Use double-call trick; move element on-screen before capture |
| iPad screenshots look like iPhone | Use wider layouts, show multitasking, landscape options |
| Watch text too small | Watch slides should have minimal or no text overlays |
| Mac screenshots too zoomed in | Show full app window with context, not cropped views |
| Using same frame for all models | Pick frame matching the user's target device |
| Wrong export dimensions | Double-check against Apple's official specs — pixel-exact match required |
| Text overflows in German/Russian | Use `LOCALE_FONT_SCALE` to shrink headline, re-check line breaks |
| Arabic/Hebrew text not mirrored | Set `dir="rtl"` on slide container, mirror text alignment |
| CJK text uses Latin font | Map locale to correct Noto Sans variant in `LOCALE_FONTS` |
| Same line breaks for all languages | Each language needs its own `<br />` placement in translations |
| Machine-translated headlines | Headlines are ads — run them by a native speaker or re-craft per locale |
| Missing font subsets | Import only needed subsets, but ensure all glyphs render (test with real text) |
| Individual file downloads instead of ZIP | Always use `jszip` — never trigger browser downloads per file |
| Flat ZIP with no folders | Organize: `locale/Device Size/01-name.png` — matches App Store Connect upload |
| Export takes forever with 50+ langs | Export only mandatory sizes (6.9" + 6.5" for iPhone, 13" for iPad), show progress bar |
| Forgot to switch locale before export | Set locale → wait 500ms for re-render → then capture |
| Using Inter/generic font for Latin locales | Always use the project's brand font — only use Noto for non-Latin scripts |

## Device Frame Sources & Licensing

The device mockup frames included with this skill come from:

- **iPhone, iPad & Mac frames**: [mockup-device-frames](https://github.com/jamesjingyi/mockup-device-frames) by James Jingyi — sourced from Apple Developer Resources
- **Legacy iPhone frame** (`mockup.png`): Included with original skill

**Important**: These frames are provided for creating App Store marketing screenshots for your own apps. Do not redistribute the frames separately.
