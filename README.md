# Industrial HMI UX Skill

A Claude skill that helps engineers design industrial human-machine interfaces with interactive mockups and PLC tag exports.

## What it does

When installed, this skill automatically activates whenever you ask Claude about HMI design, operator screens, SCADA layouts, PLC tags, or industrial control panels. It guides Claude to:

- **Ask the right questions first** — target panel hardware, machine type, operator role, PLC platform, communication protocol, and safety scope.
- **Generate interactive screen mockups** — React components sized to your exact panel resolution with simulated live data.
- **Bind PLC tags to every screen element** — every button, indicator, and display gets a named tag with data type and direction.
- **Export tag tables for your platform** — structured text for Codesys (GVL declarations, structured types) or L5X XML and CSV for Allen-Bradley (Studio 5000 / FactoryTalk View).
- **Follow industrial UX best practices** — ISA-101 color standards, 20mm touch targets, alarm severity hierarchy, safety callouts.

## Supported HMI panels

The skill includes specs for 40+ panel models across these manufacturers:

- Allen-Bradley PanelView Plus 7 and PanelView 5000
- Siemens SIMATIC Comfort Panels and Unified Comfort Panels
- Weintek cMT and iE series
- Beijer X2 series
- Pro-face GP4000 series
- Red Lion CR series
- IDEC HG series
- Beckhoff Control Panels

See [`references/hmi-panels.md`](references/hmi-panels.md) for the full database. Adding new panels is straightforward — see [CONTRIBUTING.md](CONTRIBUTING.md).

## Supported PLC platforms

- **Codesys 3.5** — GVL declarations, structured types (DUT), CSV symbol export
- **Allen-Bradley (Studio 5000)** — L5X XML tag import, UDT definitions, FactoryTalk View ME CSV

## File structure

```
industrial-hmi-ux-skill/
├── SKILL.md                          # Main skill — design methodology and workflow
├── references/
│   ├── hmi-panels.md                 # Hardware panel database (resolution, PPI, platform)
│   ├── components.md                 # Reusable React component library for mockups
│   ├── color-standards.md            # ISA-101 aligned color palette
│   ├── codesys-export.md             # Codesys GVL/DUT/CSV export templates
│   ├── ab-logix-export.md            # Allen-Bradley L5X/UDT/CSV export templates
│   └── screen-templates.md           # Starter layouts for common machine types
├── README.md                         # This file
├── CONTRIBUTING.md                   # How to add panels, components, and platforms
├── INSTALLATION.md                   # How to install the skill
└── .github/
    └── PULL_REQUEST_TEMPLATE.md      # PR template for contributions
```

## Quick start

1. Install the skill (see [INSTALLATION.md](INSTALLATION.md))
2. Start a conversation with Claude
3. Ask something like: *"Design an HMI for a packaging line. We're using a PanelView Plus 7 10-inch and a CompactLogix PLC."*
4. Claude will ask clarifying questions, then generate a screen mockup with bound tags and offer to export the tag table

## Examples

**Simple machine screen:**
> "Design a main screen for a conveyor system with 4 zones. Target is a Weintek cMT2108X. PLC is Codesys."

**Alarm redesign:**
> "Our alarm screens are too noisy. We have a Siemens KTP1200 Comfort Panel on an S7-1500. Help us redesign the alarm layout."

**Supervisor dashboard:**
> "I need a supervisor overview for 3 packaging lines on a 22-inch PanelView. Should show OEE, production rate, and active alarms for each line."

**Tag export only:**
> "Generate an Allen-Bradley tag table for a filling station with 4 fill heads, each with a scale input, fill valve, and reject gate."

## License

Internal use. Modify and distribute freely within your organization.
