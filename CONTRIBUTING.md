# Contributing

This skill is maintained by the engineering team. Contributions are welcome — especially new panel models, component patterns, and PLC platform exports.

## How to add a new HMI panel model

Edit `references/hmi-panels.md`.

### If the manufacturer already has a section:

Add a new row to the existing table. Follow the column format exactly:

```markdown
| Model Number | Screen Size | Resolution | Aspect Ratio | PPI | Touch Type | Runtime | Notes |
```

Calculate PPI using: `PPI = sqrt(width_px^2 + height_px^2) / diagonal_inches`

Example — adding a new Weintek model:

```markdown
| cMT3162X | 15.6" | 1920×1080 | 16:9 | 141 | Projected Cap | EasyBuilder Pro | Full HD. Multi-touch. Good for complex process views. |
```

### If this is a new manufacturer:

Add a new section with this template:

```markdown
---

## Manufacturer Name Series Name

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| MODEL1 | 7" | 800×480 | 5:3 | 133 | Resistive | RuntimeName | Brief notes. |
| MODEL2 | 10" | 1024×600 | ~16:9 | 118 | Resistive | RuntimeName | Brief notes. |

**Platform notes:** Description of the development environment, communication protocols supported, and any design constraints or unique features relevant to HMI design.
```

### Required fields

- **Model**: Manufacturer's part number (what engineers order)
- **Screen**: Diagonal size in inches
- **Resolution**: Width × Height in pixels
- **Aspect**: Ratio (16:9, 5:3, 4:3, 16:10)
- **PPI**: Pixels per inch (calculated)
- **Touch**: Resistive, Projected Capacitive, or Resistive + Keys
- **Runtime**: The HMI runtime software
- **Notes**: Practical design guidance — what works well, what to watch out for

### Platform notes should cover

- Development environment / IDE name
- Supported communication protocols
- How tags are addressed (by name, by register, by path)
- Any unique features (function keys, multi-touch, web-based, remote access)
- Common use cases or typical applications

---

## How to add a new React component

Edit `references/components.md`.

Add a new section following this structure:

```markdown
## Component Name

Brief description of what it represents and when to use it.

\`\`\`jsx
function ComponentName({ prop1, prop2, tagPrefix }) {
  return (
    <div data-tag={`${tagPrefix}_SomeTag`}>
      {/* Component markup using Tailwind classes and COLORS constants */}
    </div>
  );
}
\`\`\`

Usage:
\`\`\`jsx
<ComponentName prop1="value" prop2={stateVar} tagPrefix="AREA_EQUIP" />
\`\`\`
```

### Component rules

- Use the `COLORS` constants defined at the top of `components.md` — don't hardcode colors
- Every data-bound element must have a `data-tag` attribute with the PLC tag name
- Use Tailwind utility classes only — no external CSS
- Include a `tagPrefix` prop so tag names follow the `{Area}_{Equipment}` convention
- Minimum touch target: 48px (maps to ~20mm on typical HMI densities)
- Keep it self-contained — no imports beyond React hooks and the COLORS object

---

## How to add a new PLC platform export

Create a new file: `references/{platform}-export.md`

Follow the structure used in `codesys-export.md` or `ab-logix-export.md`:

1. **Overview** — What platform this targets, what IDE/tools are involved
2. **Export format(s)** — At least one, ideally two:
   - A native project import format (XML, structured text, etc.)
   - A CSV/tabular format for simpler import
3. **Data type mapping table** — Map the generic types (BOOL, INT, DINT, REAL, STRING) to the platform's type names
4. **Structured type / UDT templates** — For Motor, Valve, Cylinder, Analog Input at minimum
5. **Generation instructions** — Step-by-step for how the skill should produce the export
6. **Platform-specific naming rules** — Max length, allowed characters, conventions

After creating the file, add a reference to it in `SKILL.md`:

- Add the platform to the "Tag data types" table
- Add a bullet under "Exporting tags" pointing to the new reference file
- Add a line in the "Reference files" section at the bottom

---

## How to add a new screen template

Edit `references/screen-templates.md`.

Add a new section with:

1. **Machine type heading** and brief description
2. **Screen hierarchy table** — which screens, their purpose, target audience
3. **Main screen ASCII layout** — sized to a common panel (note which one)
4. **Key tags list** — the essential tags for this machine type

Keep ASCII layouts simple — they're guidance, not pixel-perfect specs. Use the notation from existing templates (box-drawing characters, state indicators).

---

## Editing the color palette

Edit `references/color-standards.md`.

If your site has a corporate or plant-wide color standard that differs from the ISA-101 defaults, update the tables. Just make sure every color assignment is documented with its meaning, and that the contrast ratios still meet the 4.5:1 minimum for industrial viewing conditions.

---

## Editing the main skill workflow

Edit `SKILL.md`.

The main file controls the overall design methodology — the questions Claude asks, the order of operations, the output format, and the quality checklist. If you change the workflow, make sure the "First steps" section still gathers all the information needed before design begins.

Keep `SKILL.md` under 500 lines. If you need to add substantial content, put it in a new reference file and point to it from the main skill.

---

## Testing your changes

After editing, the easiest way to test is to start a new Claude conversation and try a prompt that exercises your changes. For example, if you added a new panel model:

> "Design an HMI main screen for a packaging line. We're using a [YourNewModel] panel with a [PLC platform]."

Claude should look up the panel specs, use the correct resolution, and apply appropriate design constraints.

---

## Pull request checklist

- [ ] Markdown tables are properly formatted (pipes aligned, header separator row present)
- [ ] PPI values are calculated correctly
- [ ] New reference files are linked from SKILL.md
- [ ] Component code uses COLORS constants and data-tag attributes
- [ ] Export templates include data type mapping and generation instructions
- [ ] No proprietary or confidential information in committed files
