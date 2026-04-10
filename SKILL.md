---
name: industrial-hmi-ux
description: "Designs industrial human-machine interfaces with interactive mockups and PLC tag exports. Use when the user asks for HMI design, operator screens, industrial dashboards, SCADA layouts, PLC tag tables, Codesys or Allen-Bradley screen design, machine control UI, alarm interfaces, or any industrial control panel mockup. Also trigger when the user mentions touch panels, operator panels, Weintek, Beijer, Siemens Comfort Panel, PanelView, Pro-face, Red Lion, HMI screen layout, motor control screen, process overview, or similar industrial display hardware. MANDATORY TRIGGERS: HMI, SCADA, PLC tags, operator panel, industrial UI, machine interface, control screen, alarm design, PanelView, Codesys visualization."
metadata:
  author: Greg Kuhlman
  version: 2.0.0
  category: design
  tags:
    - hmi
    - ux
    - industrial-design
    - scada
    - plc
    - codesys
    - allen-bradley
    - tag-export
---

# Industrial HMI UX

## Purpose

Create operator-focused industrial interfaces that are fast to understand, hard to misuse, and reliable in noisy high-pressure environments. Generate interactive screen mockups (React/HTML) with bound PLC tag tables exportable to Codesys or Allen-Bradley platforms.

## First steps — always do these before designing

Before producing any layout or mockup, gather this information. Ask the user if anything is unclear.

1. **Target hardware.** Which HMI panel or display? Read `references/hmi-panels.md` to look up the exact model's resolution, screen size, aspect ratio, and platform constraints. If the user names a model, use its specs. If they don't, ask or assume a 10" widescreen (1024×600) as a safe default.

2. **Machine type.** Packaging, CNC, press, conveyor, filling, assembly, welding cell, test stand, or other. This drives the whole screen hierarchy.

3. **User role.** Operator, maintenance tech, supervisor, or engineer. Different roles need different screen depth and control access.

4. **PLC platform.** Codesys, Allen-Bradley (Studio 5000 / RSLogix), Siemens (TIA Portal), Beckhoff, or other. This determines tag naming rules and export format.

5. **Communication protocol.** EtherNet/IP, Modbus TCP, PROFINET, OPC-UA, or other. This affects data types and addressing.

6. **Safety scope.** Are any functions safety-rated (SIL/PLe)? Safety-critical controls must never appear as standard HMI pushbuttons — they require hardwired e-stop circuits or safety PLCs and should only show status on the HMI, not control.

Make assumptions explicit. When in doubt, choose the conservative, operator-first option.

## Design principles

1. **Prioritize the operator task.**
   Design around what the operator must do in the next 5–30 seconds. Make the primary action obvious. Remove decorative or nonfunctional elements. Every pixel should earn its place.

2. **Minimize error risk.**
   Prevent accidental destructive actions. Separate monitoring from control. Require confirmation for high-risk actions (start, stop, reset, valve open/close). Surface system state before allowing control changes. Disable controls when the machine is not in a valid state and show why.

3. **Make status legible at a glance.**
   Use clear hierarchy for normal, warning, and critical states. Read `references/color-standards.md` for the ISA-101 aligned color palette. Always pair color with text labels or icons — never rely on color alone. Keep critical information visible without scrolling.

4. **Support harsh environments.**
   Assume glare, vibration, dust, gloves, noise, and partial attention. Touch targets must be at least 20×20mm (roughly 44px at typical HMI densities). Keep text concise — abbreviations are fine when they are industry-standard. Favor high contrast and simple layouts. Avoid interactions that require precision (no sliders for setpoints — use increment/decrement buttons or numeric entry).

5. **Favor recoverability.**
   Make faults easy to diagnose. Show what changed, why it failed, and what to do next. Provide clear reset and recovery paths. Preserve context after alarms or navigation changes so operators don't lose their place.

## Screen structure

Use this hierarchy unless the task clearly requires something else:

1. **Header bar** — Machine name, current state (Running / Stopped / Faulted), active alarm count, date/time, navigation.
2. **Alarm banner** — Active critical and warning alarms with timestamp and short description. Always visible.
3. **Process graphic / primary view** — Simplified representation of the machine or process showing key status indicators and primary controls.
4. **Trend / supporting data** — Live values, small trend charts, setpoint displays.
5. **Footer / navigation** — Screen navigation tabs, user login, manual/auto mode indicator.

Keep the most important action above the fold. If the user needs to act during an alarm, bring the action and the reason into the same view.

## Alarm design rules

- Reserve the strongest visual treatment (red background, flashing) for the most urgent states only.
- Define three severity levels maximum: Critical (immediate action), Warning (attention needed soon), Info (logging only).
- Prevent alarm flooding — suppress repeated instances of the same alarm within a configurable deadband.
- Distinguish alarm states clearly: Active-Unacknowledged (flashing), Active-Acknowledged (steady), Cleared-Unacknowledged (slow flash), Cleared (removed from active list).
- Include plain-language recovery guidance: not just "Motor Overload" but "Motor Overload — Check thermal relay OL1 on drive panel, reset when cool."
- When multiple alarms fire, group by severity and show count. Let the operator drill into a full alarm list.

## Interaction rules

- Use explicit, unambiguous labels: Start, Stop, Reset, Acknowledge, Open Valve, Close Valve, Jog Forward, Jog Reverse.
- Avoid vague labels: Run, Engage, Process, Execute, Go, Do It.
- Require confirmation dialogs for destructive or irreversible actions (emergency stop is the exception — it must act instantly).
- Disable controls when the system is not in a valid state. Show the reason: "Start disabled — Safety gate open" or "Reset disabled — Fault active."
- Keep navigation shallow: Home → Screen is the deepest path for operators. Maintenance and engineering screens can go one level deeper.
- Physical-world metaphors work well: a valve icon that visually opens/closes, a conveyor that shows direction.

## Generating mockups

When the user asks for a screen design, produce an interactive React component (single .jsx file) that renders the HMI layout. Read `references/components.md` for the reusable component patterns and `references/color-standards.md` for the color palette.

### Mockup rules

- Target the resolution and aspect ratio of the selected HMI panel (from `references/hmi-panels.md`). Render at native resolution in the preview.
- Use Tailwind utility classes for layout. No external CSS files.
- Simulate live data with React state and useEffect intervals so the mockup feels alive (fluctuating analog values, blinking alarm indicators, state transitions on button press).
- Every interactive element (button, indicator, numeric display, valve graphic) must have a `data-tag` attribute containing its PLC tag name. This makes the tag binding visible in the mockup and extractable for documentation.
- Include a collapsible "Tag Table" panel at the bottom of the mockup that lists all bound tags with their name, data type, direction, and description.
- Use the component patterns from `references/components.md` — don't reinvent motor blocks, valve symbols, or indicator lights each time.

### Mockup structure

```jsx
// Header: machine name, state, alarm count, time
// Alarm banner: active alarms
// Main area: process graphic with status indicators and controls
// Trend area: key analog values
// Footer: navigation tabs
// Collapsible: Tag binding table
```

## Tag binding

Every screen element that reads from or writes to the PLC gets a tag entry. Generate a tag table alongside every mockup.

### Tag naming convention

Default pattern: `{Area}_{Equipment}_{Function}_{Qualifier}`

| Qualifier | Meaning | Data direction |
|-----------|---------|----------------|
| _CMD | Command (operator writes) | HMI → PLC |
| _FB | Feedback (PLC confirms) | PLC → HMI |
| _PV | Process value (measured) | PLC → HMI |
| _SP | Setpoint (operator sets) | HMI → PLC |
| _STS | Status word / enum | PLC → HMI |
| _ALM | Alarm bit or word | PLC → HMI |
| _EN | Enable | HMI → PLC |
| _RST | Reset | HMI → PLC |

Examples:
- `PKG_Conv01_Run_CMD` — Operator presses Start for Conveyor 01
- `PKG_Conv01_Run_FB` — PLC confirms Conveyor 01 is running
- `PKG_Conv01_Speed_PV` — Current speed reading
- `PKG_Conv01_Speed_SP` — Operator-entered speed setpoint
- `PKG_Conv01_Overload_ALM` — Motor overload alarm

Let the user override the naming convention. If they have an existing standard, follow it.

### Tag data types

Map to the target platform's type system:

| Generic type | Codesys | Allen-Bradley (Logix) |
|-------------|---------|----------------------|
| Boolean | BOOL | BOOL |
| Integer (16-bit) | INT | INT |
| Integer (32-bit) | DINT | DINT |
| Float (32-bit) | REAL | REAL |
| String | STRING | STRING |
| Enum / state | INT + constants | DINT or UDT |
| Timer value | TIME | TIMER (use .ACC for display) |

### Exporting tags

After generating the tag table, offer to export for the user's platform:

- **Codesys**: Read `references/codesys-export.md` for the GVL declaration format and structured text export.
- **Allen-Bradley**: Read `references/ab-logix-export.md` for the L5X XML snippet and CSV import format.

Generate the export file and provide it to the user.

## Output format

For every HMI design request, deliver:

1. **Context summary** — Machine type, user role, panel model, platform, key assumptions.
2. **Screen hierarchy** — Which screens exist, what each one shows, navigation flow.
3. **Interactive mockup** — React .jsx file targeting the panel's resolution with simulated data and `data-tag` attributes on all bound elements.
4. **Tag table** — Full list of tags with name, type, direction, description, and which screen element they bind to.
5. **Platform export** — Tag definitions formatted for import into Codesys or Studio 5000 (on request or when platform is known).
6. **Alarm strategy** — Severity definitions, alarm list design, recovery guidance.
7. **Design notes** — Accessibility considerations, environmental factors, risks, edge cases.

## Safety callouts

When a user asks for HMI controls that involve safety functions:

- **Never place safety-critical stop/start controls as HMI-only buttons.** E-stops, safety gate interlocks, and light curtain resets must be hardwired. The HMI can display their status but must not be the sole means of control.
- **Flag it clearly.** If the user asks for an e-stop button on the HMI, explain why this is not appropriate for a safety-rated function and suggest showing e-stop status (circuit healthy / tripped) as a read-only indicator instead.
- **Dual-channel confirmation.** For high-consequence actions that are not safety-rated but still risky (emptying a hopper, starting a high-speed cycle), use a two-step confirmation: press and hold, or press + confirm dialog.

## Quality checklist

Before finalizing any design, verify:

- [ ] Primary operator action is obvious and reachable within one tap.
- [ ] Accidental activation of destructive actions is prevented (confirmation, disable logic).
- [ ] Labels are clear, consistent, and unambiguous.
- [ ] State is communicated quickly through color + text + icon (never color alone).
- [ ] Layout works at the target panel's resolution without scrolling for primary content.
- [ ] Touch targets meet 20×20mm minimum.
- [ ] Alarm design follows three-severity model with distinct visual states.
- [ ] Recovery guidance is included for every alarm.
- [ ] Safety-critical functions are read-only indicators, not HMI-controlled.
- [ ] Tag table is complete — every interactive element has a bound tag.
- [ ] Tag names follow the naming convention consistently.
- [ ] Export format matches the target platform.

## Common issues and fixes

**Too much information on one screen.**
Operators don't need every sensor value at once. Keep the main screen task-focused — hide secondary data behind drill-down screens or expandable panels.

**Unclear alarm priority.**
Too many colors or equal-weight alerts cause alarm fatigue. Use a strict three-level severity hierarchy and reserve red + flash exclusively for critical conditions.

**Unsafe controls exposed to operators.**
Actions that are too easy to trigger accidentally. Add confirmation dialogs, disable invalid actions, and physically separate view-only areas from control areas on the screen.

**Hard-to-read screens in the field.**
Low contrast, small type, dense layouts. Increase contrast ratios to at least 4.5:1, use minimum 14pt text (16pt preferred), enlarge touch targets, and add whitespace between controls.

**Tag naming inconsistency.**
Tags named ad-hoc become unmaintainable. Establish the naming convention before designing the first screen and enforce it across all screens.

## Reference files

Read these as needed — they contain detail that would bloat this file:

- `references/hmi-panels.md` — Hardware panel database with model specs (resolution, size, platform, constraints). **Read this first when the user specifies a panel model.**
- `references/components.md` — Reusable React component patterns for HMI elements (buttons, indicators, motor blocks, valve symbols, numeric displays, alarm rows).
- `references/color-standards.md` — ISA-101 aligned color palette for process states, alarm severities, and UI elements.
- `references/codesys-export.md` — Codesys GVL declaration format, structured text templates, and CSV export for tag import.
- `references/ab-logix-export.md` — Allen-Bradley L5X XML tag snippets and CSV format for Studio 5000 tag import.
- `references/screen-templates.md` — Starter screen layouts for common machine types (packaging, conveyor, CNC, filling, press).
