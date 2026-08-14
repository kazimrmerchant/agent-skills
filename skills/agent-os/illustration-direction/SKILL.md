---
name: illustration-direction
description: "Use when working with illustration direction, visual systems, interface craft, layouts, assets, diagrams, brand visuals, interaction patterns, implementation guidance, review, and polish, especially when the user explicitly asks for illustration direction or adjacent tasks."
version: 1.0.1
---

## When to Use
Use this skill for interface design, UX review, accessibility, responsive layouts, design systems, mobile/web UI, component behavior, interaction states, visual hierarchy, usability improvements, and frontend implementation guidance. Also use for visual hierarchy, diagrams, canvas tools, Figma-style workflows, typography, color, iconography, and UI polish.

## Prerequisites
Collect the target users, primary task, platform, viewport range, existing design system, accessibility requirements, brand constraints, data density, interaction states, and success metric. Ask for screenshots or reference URLs when visual fidelity matters.

## Procedure
1. **Define Strategy & Audience**: Define audience, domain tone, brand constraints, information hierarchy, and the decision the visual must support. Select illustration style (Flat/Geometric, Line Art, Isometric, Hand-Drawn, 3D Rendered) based on communication goals and technical constraints.
2. **Establish Design Tokens**: Define tokens for type, spacing, color, elevation, radius, iconography, and motion. Use a JSON manifest to codify stroke weights, color restrictions, and corner radii.
3. **Design for Scanning**: Apply hierarchy, grouping, alignment, density, contrast, and rhythm. Ensure minimum 15% breathing room around focal points.
4. **Include Accessibility**: Ensure text elements inside illustrations maintain a contrast ratio of at least 4.5:1 against the background. Ensure major shapes and icons have a contrast ratio of at least 3:1. Add `aria-labelledby` and description attributes to SVGs.
5. **Map Key States**: Map empty, loading, success, error, disabled, permission-limited, offline, and responsive variants. Use warm, friendly visual metaphors for empty states (200px-400px wide). Keep error screens clean and simple.
6. **Implement SVG Assets**: Use `viewBox` coordinates instead of hardcoded width/height for responsive scaling. Use CSS classes instead of inline styles. Apply SVG noise filters for grain textures without raster bloat.
7. **Validate & Deliver**: Validate with realistic content, long labels, error text, touch/keyboard interaction, small screens, wide screens, dark/light variants, and export requirements. Deliver concrete implementation guidance, not vague aesthetic notes.

## Pitfalls
- **Auto-tracing Rasters**: Do not auto-trace JPG photos to create SVGs. This creates thousands of paths, leading to slow rendering and massive file sizes.
- **Inline Font Styles**: Never leave raw text elements in SVGs without converting them to outlines (paths). Missing system fonts will break text alignment.
- **Complex Filters**: Avoid deep SVG drop-shadow filters on pages loading multiple assets, as this causes scroll lag on low-end mobile devices.
- **Aspect Ratio Mismatch**: `viewBox` coordinates must match the aspect ratio of the target UI container. Exporting a 16:9 illustration to a 512x512 container adds unwanted white space.
- **Unused Metadata**: Remove unused metadata elements (`<metadata>`, `sketch:type`, `sodipodi:docname`) from SVG code before committing to keep file sizes small.
- **Copyrighted Layouts**: Do not use copyrighted visual layouts or character styles in commercial products without verified, signed licensing agreements.
- **Non-scaling Strokes**: Do not use `vector-effect: non-scaling-stroke` unless specified by the design system, as this can cause strokes to appear too thin on large screens.

## Verification
1. **Run SVG Audit Script**: If `scripts/audit_svg.py` exists in the workspace, execute it to verify SVGs conform to the style guide (checks root tag, `viewBox` presence, raster links, and inline styles).
   ```powershell
   python scripts\audit_svg.py assets\illustrations\
   ```
2. **Check Contrast Ratios**: Verify text contrast (4.5:1) and non-text contrast (3:1) against backgrounds.
3. **Responsive Check**: Ensure all SVGs use `viewBox` and scale properly on mobile screens without hardcoded dimensions.
4. **Accessibility Check**: Confirm SVGs include `aria-labelledby="title-id"` and `desc-id` for screen readers.
