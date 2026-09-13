# Remotion Motion Graphics - Agent Skill

A Claude skill for building kinetic-typography / logo / brand promo motion graphics **as code** with [Remotion](https://www.remotion.dev) (React) and shipping an MP4. It covers the whole path: reading a reference clip and a brand's SVG logo, a scene-per-beat project with reusable animation helpers, a verify-by-contact-sheet loop, both 9:16 and 16:9 outputs from one codebase, and Remotion Studio for tweaks.

Built while cloning the motion of a reference promo for [hundredable.com](https://hundredable.com) — the workflow is general, the video is not included.

## Install

**Claude Code (CLI / desktop app)** — clone the skill into your skills folder:

```bash
git clone https://github.com/Boriwatopal/agent-skill-remotion-motion-graphics.git /tmp/rmg && \
cp -R /tmp/rmg/skills/remotion-motion-graphics ~/.claude/skills/ && rm -rf /tmp/rmg
```

Or for one project only: copy `skills/remotion-motion-graphics` into `<project>/.claude/skills/`.

**claude.ai / Cowork** — download [`dist/remotion-motion-graphics.skill`](dist/remotion-motion-graphics.skill) (or grab it from the latest [release](../../releases)) and upload it in Settings → Capabilities → Skills.

Then just ask for a motion piece: the skill triggers on requests like *"make an animated logo from this SVG"*, *"make a promo clip like this video"*, *"ทำโมชั่นโลโก้"*, or any Remotion code work.

## Folder Structure

```
skills/remotion-motion-graphics/
  SKILL.md                              # The workflow: read material → scaffold → scenes → verify → deliver
  assets/                               # Starter code to copy into src/
    theme.template.ts                   #   brand colours, PORTRAIT/LANDSCAPE presets, named scene durations
    layout.ts                           #   useLayout() — every position derives from the frame size
    anim.ts                             #   easing curves, prog(), springAt(), riseStyle(), typed()
    fonts.ts, Main.template.tsx, Root.template.tsx
    components/
      Backgrounds.tsx                   #   LightBg / DarkBg with brand glow
      Cursors.tsx                       #   arrow + dot cursors that spring between targets
      Words.tsx                         #   word-by-word reveal with optional exit
      SvgArt.tsx                        #   animate SVG artwork per path (letter pop, character bounce)
  scripts/
    analyze_reference.sh                # contact sheets from a reference video → beat map
    svg_to_paths.mjs                    # SVG → TypeScript path data + bounding-box report
    check_render.sh                     # low-res render + contact sheets for a composition
    still_frame.sh                      # one frame from an MP4 at a timestamp
  references/
    scene-patterns.md                   # 16 motion patterns with the timings that worked
    verification-and-gotchas.md         # the check loop and the traps met in practice
    remotion-rules/                     # 13 official Remotion rule files (see Credits)
dist/remotion-motion-graphics.skill     # packaged skill for claude.ai upload
```

## What the workflow does

1. **Read the material first.** Contact sheets from the reference clip become a beat map; the brand site or the logo's SVG fills give colours, font and real copy; the logo's paths are mapped so letters and character can animate separately.
2. **One skeleton for every ratio.** Named scene durations in one theme file, a layout hook so positions derive from `useVideoConfig()`, one file per scene. A second `<Composition>` gives 16:9 without a second codebase.
3. **Patterns, not improvisation.** Phrase swap, pill → button → click, flying collage, circle wipe, ticker + panel wipe, shape hand-off between scenes, route drawing, UI toggle moment, gradient statement, character + letter pop, bubble rows, chart card, spotlight, burst → lockup, fade out.
4. **Verify like an editor.** `tsc` → 0.3-scale render → look at every contact sheet → still-render suspicious frames → fix → final render → pull a frame from the final file as proof.
5. **Deliver.** MP4 next to the source files, Remotion Studio for scrubbing, an honest summary of what was invented and what was left out.

## Requirements

- Node.js 18+ (Remotion 4), `ffmpeg` / `ffprobe` on PATH (for the analysis and check scripts)
- Chrome is downloaded by Remotion on first render

## Gotchas captured in the skill

1. CSS applies `filter` before `clip-path` — blur the parent, clip the child, or a soft spotlight gets razor edges.
2. A "cleanup" full-frame overlay drawn after the content hides the text; the wipe shape already covers the frame.
3. Artwork cropped at the SVG canvas edge shows a hard cut on dark backgrounds — mask with a gradient that is fully transparent for the first ~4%.
4. `url(#gradient)` fills render black without their `<defs>`; the SVG script swaps them for the first stop colour.
5. Per-path transforms in inline SVG need `transformBox: "fill-box"`, or scale/rotate pivots on the canvas origin.

## Credits

- `references/remotion-rules/` are copied unchanged from [remotion-dev/skills](https://github.com/remotion-dev/skills) (the `remotion-best-practices` skill, also installable with `npx skills add remotion-dev/skills`). They remain the work of the Remotion team; see that repository for their terms.
- Everything else in this repo is released under the MIT License (see `LICENSE`).
