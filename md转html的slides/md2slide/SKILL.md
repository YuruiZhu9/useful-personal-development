---
name: md2slide
description: Convert markdown documents into polished, self-contained HTML slide presentations with dark tech/sci-fi visual style. Use this skill whenever the user wants to create slides, presentations, HTML slides, or convert markdown/docs to a slide deck. Trigger on: "make slides", "create presentation", "md to slides", "html slides", "slide deck", "ppt from markdown", "convert to slides", or any request involving turning content into a navigable slide format.
---

# md2slide: Markdown to HTML Slide Generator

Convert structured markdown into a single self-contained HTML file with full-screen slides, keyboard/swipe/click navigation, and a dark sci-fi visual theme.

## When to use this skill

Any time the user wants a presentation/slides from a markdown document, outline, or structured content. Works best with documents that have clear section hierarchy (h1/h2/h3). The output is a single `.html` file that can be opened in any browser — no server, no dependencies.

---

## Skill Dependencies & Workflow

This skill works in concert with two other skills. Follow this workflow in order:

### Step 1: Invoke `ui-ux-pro-max` for Design System

`ui-ux-pro-max` is the **primary design authority**. Use it to look up the visual style before writing any HTML.

Run the search command to get style guidance:
```bash
python3 <ui-ux-pro-max-path>/scripts/search.py "dark tech sci-fi presentation slides" --domain style
python3 <ui-ux-pro-max-path>/scripts/search.py "dark presentation" --domain color
python3 <ui-ux-pro-max-path>/scripts/search.py "tech mono sans" --domain typography
```

This gives you the authoritative color palette, typography pairing, and style keywords. The design system in this skill's reference section was built from `ui-ux-pro-max` output — when in doubt about colors, fonts, or visual style, query `ui-ux-pro-max` first.

**Key principle**: The visual style must be **consistent with ui-ux-pro-max's dark theme aesthetic** — deep navy backgrounds (#0a0e17), cyan/purple/blue accent gradients, grid patterns, glow effects, glassmorphism cards with backdrop-blur. This is not a generic "dark mode" — it's the specific sci-fi/cyberpunk aesthetic that ui-ux-pro-max defines.

### Step 2: Invoke `skill-router` for Auxiliary Skills

Use `skill-router` to discover complementary skills that may help with specific content:

- If slides need diagrams or visual layouts → `frontend-design`, `canvas-design`
- If slides need data visualization → search via skill-router
- If slides need to be exported to PPTX after HTML review → `pptx`
- If the source content needs restructuring before conversion → `brainstorming`

### Step 3: Execute md2slide Conversion

With design guidance from ui-ux-pro-max and auxiliary skills from skill-router, now follow the rest of this skill to generate the HTML.

**Execution order**: `ui-ux-pro-max` (design) → `skill-router` (tools) → `md2slide` (build)

---

## Core Design Philosophy

### Meta-Rule: Beauty & Readability Are the Ultimate Authority

Every rule, number, and specification in this skill is a **reference baseline**, not an unbreakable law. They were derived from real iteration and represent what worked well in practice — but they serve the higher goal of **美观易读 (beautiful and readable)**.

If a user's feedback, a specific slide's content, or your own judgment calls for deviation from any prescribed value (font size, color, spacing, layout, particle count, whatever) — **deviate without hesitation**. The principles below exist to get you close on the first try; they do not override the user's eyes or your common sense.

Specifically:
- All `rem` values, pixel values, opacity percentages are starting points, not contract terms
- If a slide looks better with different spacing, use different spacing
- If a user says "this is too big / too small / too bright / too dim", trust them over the spec
- If content demands a layout not listed in the rules, invent the layout
- The Self-Review Checklist catches regressions, not creative decisions

**The only ironclad commitments are**: dark sci-fi visual identity, single-file HTML output, robust navigation JS, and **content fidelity** (see below). Everything else is negotiable in service of the final visual result.

---

### Content Fidelity: Preserve the Source Exactly

The markdown source is the ground truth for content. Do not rewrite, rephrase, translate, or "improve" the text.

- **Keep the original language**: If the source mixes Chinese and English (e.g. "Claude Code" in a Chinese sentence, "Vibe Coding → TDD → SDD" as-is), keep the mix exactly as-is. Do not translate English terms to Chinese or vice versa.
- **Keep the original logic and structure**: The author's reasoning flow, argument order, and conceptual hierarchy must be preserved. You may split a long section across slides or merge short ones for visual reasons, but never reorder or omit content points.
- **Keep the original phrasing**: Don't paraphrase bullet points, don't shorten/lengthen descriptions, don't add explanatory text that wasn't in the source. If the author wrote "Harness能力", use "Harness能力", not "Harness 能力框架" or "工程骨架能力".
- **Formatting is visual, not editorial**: You decide how to layout the content (card, table, list, flow) — but the text content inside those components comes verbatim from the markdown.

---

### 1. Start Aggressive, Not Conservative

The single most common failure mode is being too conservative with sizes and spacing. Users notice when nothing changes after "improvements."

- Font sizes, padding, max-widths — start 20-30% larger than you think is reasonable
- When user feedback says "not enough change," your next adjustment should be 2-3x the magnitude of the previous attempt
- A slide that feels "full" at first draft is correct. A slide that feels "spacious" is wrong
- The content should feel like it **fills** the viewport, not floats in it

### 2. One Font to Rule Them All

Do NOT use separate English and Chinese font families. This was tested with Inter, Space Grotesk, and others — they all created visual inconsistency.

- Use `Noto Sans SC` for everything (Chinese + English + numbers)
- Only use `JetBrains Mono` for code blocks
- Never add `font-family` overrides on individual elements. If you're tempted to, don't.
- The font declaration should appear exactly once: on `html, body`

### 3. Color Readability Is Non-Negotiable

Card body text at `#94a3b8` (tailwind gray-400) is too pale for dark backgrounds. This was identified as a readability problem on multiple slides.

- Card/list body text: minimum `#b8c4d0` — use `var(--text-primary)` (#e2e8f0) as the target
- Only de-emphasize truly secondary information to `var(--text-secondary)`
- Highlighted/strong text in cards should use accent colors (cyan for emphasis)
- If text is inside a bordered container (card, highlight-box, flow-step), it needs to be brighter than text on bare background

### 4. Sparse Content Centers, Dense Content Spreads

Layout must adapt to content density. This was a specific complaint.

- Short bullet lists (<=5 items, each <30 chars): center-align, hide bullets, increase font to 1.1rem+
- Dense bullet lists or long text: left-align with bullets, normal spacing
- Few cards (2-3): use `cols-2` or `cols-3` with wider cards
- Many items: consider `cols-4` or table format
- When in doubt, center sparse content, left-align dense content

### 5. Section Tags Must Be Consistent Within a Chapter

If the source markdown has sections like 3.1, 3.2, 3.3, 3.4 — these all belong to section "三". The section tag on each slide must show the SAME chapter label, not change per subsection.

Format: `章号 · 章节名 <sub-page>N / M</sub-page>`

Example: All slides under section three show:
```
三 · AI Coding 经验 <sub-page>1/4</sub-page>
三 · AI Coding 经验 <sub-page>2/4</sub-page>
三 · AI Coding 经验 <sub-page>3/4</sub-page>
三 · AI Coding 经验 <sub-page>4/4</sub-page>
```

### 6. Navigation Must Be Bulletproof

The prev/next button bug pattern: using `slides[currentSlide]` inside a `setTimeout` AFTER updating `currentSlide` to newIndex. This references the wrong slide.

Correct pattern:
```javascript
const oldSlide = slides[currentSlide];
const newSlide = slides[newIndex];
// ... animate using oldSlide and newSlide references, never slides[currentSlide]
currentSlide = newIndex;
// setTimeout cleanup uses oldSlide reference
```

Always use animation lock (`isAnimating`) to prevent rapid-click race conditions.

---

## Content Transformation Rules

Don't mechanically convert markdown → HTML. Restructure for visual impact.

### Slide Splitting Strategy

| Source Pattern | Slide Treatment |
|---|---|
| h1 section + brief intro | 1 slide with section tag + title + bullet list |
| h2 subsection with table | 1 slide with table, widen columns for readability |
| h2 subsection with 3+ parallel concepts | 1 slide with `cols-3` or `cols-4` card grid |
| h2 subsection with 2 contrasting concepts | 1 slide with `cols-2` card grid, different border accents |
| h2 subsection with sequential process | 1 slide with flow-steps chain |
| h2 subsection with code example | 1 slide with code-block + highlight-box explanation |
| Very long section (5+ subsections) | Split into multiple slides, each with same section tag + sub-page counter |

### Visual Variety Across Slides

Avoid making every slide look the same. Alternate between these layouts:
- Card grids (2-col, 3-col, 4-col)
- Tables
- Bullet lists (centered for short, left-aligned for long)
- Flow-step chains
- Code blocks with highlight boxes
- Numbered cards with accent-colored numbers

The first slide after a section tag change should feel different from the last slide of the previous section.

### Title Slide

Always create a title slide as slide 0 with:
- Large main title (3.2rem+)
- Subtitle (1.5rem)
- Glow line separator
- Meta/description text (1.1rem)

### Closing Slide

Always create a closing slide with:
- "Thank You" or equivalent (3rem+)
- Glow line
- Brief closing text or "Questions & Discussion"

---

## Design System Reference

### Visual Style Identity

The slide style must be **visually identical** to what was produced in the validated output. If in doubt, refer to the completed slide file at `D:\技术分享\ai-coding-share-slides.html` as the ground truth reference.

**The look and feel is**: Dark sci-fi / cyberpunk presentation. Deep navy-black backgrounds, cyan + purple gradient accents, subtle grid overlay, glassmorphism cards with glow borders, monospace code blocks, neon-tinted badges. Think "hacker terminal meets premium keynote."

This style was specifically chosen to match `ui-ux-pro-max`'s dark theme aesthetic. When the user asks for "tech style" or "科技风" or "暗色调", this is what they mean. Do NOT deviate to generic corporate blue, minimal white, or gradient-pastel themes.

### Color Palette

```
Background:     #0a0e17 (deep navy black)
Card bg:        rgba(17, 24, 39, 0.8)
Accent cyan:    #00f0ff
Accent purple:  #a855f7
Accent blue:    #3b82f6
Accent green:   #10b981
Accent orange:  #f59e0b
Accent pink:    #ec4899
Text primary:   #e2e8f0
Text secondary: #94a3b8
Text dim:       #64748b
Border glow:    rgba(0, 240, 255, 0.15)
```

### Typography Scale

```
Title slide main:     3.6rem, weight 900
Section slide title:  2.6rem, weight 900
Section tag:          0.95rem, weight 700
Slide subtitle:       1.08rem
Card h3:              1.1rem, weight 700
Card body text:       0.96rem
Table header:         0.95rem
Table cell:           0.92rem
Bullet list:          1.05rem
Highlight box:        1rem
Flow step:            0.96rem
Badge:                0.88rem
Code block:           0.9rem
Nav button:           0.85rem
```

### Spacing

```
Slide padding:        25px horizontal-sides, 80px vertical-sides, 40px bottom
Card padding:         24px
Card grid gap:        20px
Max content width:    1080px
Nav bar height:       48px
Progress bar height:  2px
```

### Component Styles

**Cards**: `background: rgba(17,24,39,0.8)`, `border: 1px solid rgba(0,240,255,0.15)`, `border-radius: 12px`, `backdrop-filter: blur(10px)`

**Highlight box**: Gradient background from cyan to purple at 4% opacity, `border-radius: 12px`, padding `18px 24px`

**Flow steps**: Same as cards but smaller padding. Connected with `→` arrows in accent cyan.

**Badges**: Small colored pills. Background at 10% opacity of accent color. Use different accent colors for visual grouping.

**Tables**: Separate borders. Header with 6% cyan background. Hover rows at 2% cyan. Code in cells uses monospace with 6% cyan background.

### Background Effects

1. Fixed grid pattern: `linear-gradient` lines at 3% cyan opacity, 60px grid
2. Radial gradient overlays: cyan at 20%/50%, purple at 80%/20%, blue at 50%/80%
3. Progress bar: gradient from cyan to purple, with box-shadow glow
4. **Floating particle system** (Canvas-based, details below)
5. **Corner decorative frames** (HUD-style L-brackets, details below)

### Decorative Enhancement System

These three enhancements transform a flat presentation into an immersive experience. Include all three by default.

#### 1. Floating Particle Background

A full-screen `<canvas>` layer with ~140 particles. Never static — particles create a "living" atmosphere.

**Particle variety dimensions (all must be present):**

| Dimension | Values | Purpose |
|-----------|--------|---------|
| Color | cyan, purple, blue, green, silver-white | Visual richness, avoid monotony |
| Size | 0.4px (far) → 2.6px (near) | Depth/parallax illusion |
| Speed | 0.08 (far) → 0.53 (near) | Layer separation |
| Alpha | 0.05 (far) → 0.30 (near) | Atmospheric depth |
| Movement | Linear drift OR sinusoidal wobble | Organic, non-mechanical feel |
| Pulse | Each particle oscillates alpha at its own phase/speed | Breathing effect, never frozen |
| Glow | ~15% of near-layer particles get radial gradient halo | Soft light spots |
| Trail | ~10% of near-layer particles leave 6-frame fade trail | Motion directionality |

**Implementation pattern:**
- Use `requestAnimationFrame` loop on a fixed-position `<canvas>` with `pointer-events: none`
- Each particle stores its own color, size, velocity, wobble params, pulse phase
- Wrap particles at edges with -10px/+10px margin (smooth re-entry)
- Glow uses `createRadialGradient` from particle color at 30% alpha → 0 alpha

**Key principle**: Particles must feel organic, not uniform. Randomize phase, speed, size, and behavior for each particle. The result should look like "data flowing through space" not "dots on a screen."

#### 2. Animated Gradient Card Borders

Cards breathe with a slow color-cycling border animation.

- `@keyframes borderGlow`: cycle through `border-color` cyan → purple → blue → cyan over 6 seconds
- Each card gets a different `animation-delay` (-1s to -4s) so they don't pulse in sync
- Add matching `box-shadow` glow at 6% opacity that follows the border color
- Use `ease-in-out` timing for smooth, organic transitions

This replaces the static `border: 1px solid rgba(0,240,255,0.15)` on cards.

#### 3. Corner Decorative Frames (HUD Brackets)

L-shaped brackets at all four corners of each slide, evoking a HUD / heads-up display.

- Size: 40px × 40px per corner
- Each corner uses `::before` (horizontal line) and `::after` (vertical line), both 1.5px thick
- Color: cyan gradient at 25% opacity
- Top corners: gradient fades from cyan to transparent (going inward)
- Bottom corners: positioned at `bottom: 60px` to clear the nav bar
- `pointer-events: none` so they don't interfere with interaction

### Animations

- Slide transitions: 0.45s opacity + translateX
- Content fade-in: 0.4s translateY(12px → 0)
- Stagger cards: 0.05s delay between each card
- Card hover: translateY(-1px) + border brightens
- Card border glow: 6s ease-in-out infinite, staggered per card
- Particle system: continuous requestAnimationFrame loop

---

## Self-Review Checklist

Before delivering the HTML file, mentally walk through every slide and check:

1. **Font consistency**: Is every non-code element using Noto Sans SC? (no Inter, no Space Grotesk, no font-family overrides)
2. **Text readability**: Are card interiors and highlight boxes using bright enough text? (minimum #b8c4d0)
3. **Content fills space**: Does each slide look "full"? Is there excessive empty space above or below content?
4. **Section tags**: Are tags consistent within each chapter? Same chapter name, different sub-page counter?
5. **Sparse content**: Are short lists centered? Are they large enough?
6. **Navigation**: Does the JS use `oldSlide`/`newSlide` variables (not `slides[currentSlide]` after update)? Is there an animation lock?
7. **Visual variety**: Do consecutive slides use different layouts (not all card grids)?
8. **Padding**: Top padding ≤25px, bottom padding accounts for 48px nav bar?
9. **Particles**: Is the canvas present with ~140 particles? Do they have color variety (5 colors), size/speed layering, pulse, wobble, glow, and trails?
10. **Card animation**: Do cards have the borderGlow keyframe with staggered delays?
11. **Corner frames**: Are all 4 HUD brackets positioned correctly (bottom ones clearing nav bar)?

If any check fails, fix it before showing the result. The goal is to need zero iteration rounds.

---

## Output Format

Single `.html` file containing:
- All CSS inline in `<style>`
- All JS inline in `<script>`
- Google Fonts `@import` for Noto Sans SC and JetBrains Mono
- No external dependencies

File should be saved in the same directory as the source markdown.

---

## Interaction Meta-Principles

### How to handle feedback

When user says "changes aren't noticeable" or "too conservative":
- Double or triple the magnitude of your adjustments
- Don't do 0.04rem increments — do 0.15rem+ increments
- Change multiple properties simultaneously (size + padding + max-width)
- Rewrite the entire CSS block rather than making isolated edits

When user points to a specific slide:
- Fix that slide AND check if the same issue exists on other slides
- Apply the fix globally if it's a systemic issue, locally if it's slide-specific

### How to deliver first time

The first version should be so good that the user only needs minor tweaks on 2-3 specific slides, not a complete overhaul. This means:
- Use the aggressive sizes from the Typography Scale above (don't start small)
- Apply the Self-Review Checklist before delivering
- Visual variety should be baked in from the start
- Center sparse content, left-align dense content — decide per slide
