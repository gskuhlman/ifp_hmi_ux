# Color Standards for Industrial HMI

Aligned with ISA-101 (Human Machine Interfaces for Process Automation Systems) and common industrial conventions. These are recommendations — always defer to the user's site or corporate standards if they have them.

## Core principle

Color communicates state, never decoration. Every color assignment must have a clear, consistent meaning across all screens. Pair every color with a text label or icon — never rely on color alone (operators may be color-blind, and screen tinting from glare or protective overlays can shift perceived colors).

## Machine / Process State Colors

| State | Color | Hex | Tailwind | Usage |
|-------|-------|-----|----------|-------|
| Running / Active | Green | #22c55e | green-500 | Equipment running, process active, valve open and confirmed |
| Stopped / Idle | Gray | #6b7280 | gray-500 | Equipment stopped normally, process idle, no demand |
| Faulted | Red | #ef4444 | red-500 | Equipment faulted, trip condition, interlock active |
| Warning | Amber | #f59e0b | amber-500 | Approaching limit, maintenance due, degraded performance |
| Transitioning | Blue | #3b82f6 | blue-500 | Starting, stopping, filling, draining, in-motion between states |
| Disabled / Offline | Dark Gray | #374151 | gray-700 | Equipment taken out of service, not available |
| Manual Mode | Cyan | #06b6d4 | cyan-500 | Equipment under manual/local control (not auto) |

## Alarm Severity Colors

| Severity | Background | Text | Hex | Behavior |
|----------|-----------|------|-----|----------|
| Critical | Red | White | #dc2626 on bg | Flashing when unacknowledged, steady when acknowledged |
| Warning | Amber | Black | #d97706 on bg | Flashing when unacknowledged, steady when acknowledged |
| Info / Advisory | Blue | White | #2563eb on bg | Steady. No flash. For logging and awareness only. |
| Cleared | Green | White | #16a34a on bg | Brief display then removed from active list |

### Alarm flash pattern

- **Active + Unacknowledged**: Fast blink (0.5s on / 0.5s off) using `animate-pulse` or a custom CSS animation
- **Active + Acknowledged**: Steady color, no blink
- **Cleared + Unacknowledged**: Slow blink (1s on / 1s off) — alerts operator that something cleared but wasn't acknowledged
- **Cleared + Acknowledged**: Removed from active alarm display, moved to history

## Background and Surface Colors

Use a dark theme as the default for industrial HMIs. Dark backgrounds reduce glare, power consumption on OLED panels, and visual fatigue during long shifts. Light themes are acceptable for office/control-room environments but should be explicitly requested.

| Surface | Color | Hex | Tailwind |
|---------|-------|-----|----------|
| Screen background | Dark slate | #1e293b | slate-800 |
| Card / section background | Medium slate | #334155 | slate-700 |
| Header / footer bar | Very dark | #0f172a | slate-900 |
| Elevated element | Lighter slate | #475569 | slate-600 |
| Border / divider | Gray | #4b5563 | gray-600 |

## Text Colors

| Context | Color | Hex | Tailwind |
|---------|-------|-----|----------|
| Primary text | Near white | #f8fafc | slate-50 |
| Secondary / label text | Medium gray | #94a3b8 | slate-400 |
| Disabled text | Dark gray | #64748b | slate-500 |
| Text on colored background | White | #ffffff | white |
| Value in warning range | Amber | #f59e0b | amber-500 |
| Value in alarm range | Red | #ef4444 | red-500 |

## Control Element Colors

| Element | Normal | Pressed/Active | Disabled |
|---------|--------|---------------|----------|
| Start button | Green #22c55e | Bright green #4ade80 | Gray #9ca3af |
| Stop button | Red #ef4444 | Bright red #f87171 | Gray #9ca3af |
| Reset button | Blue #3b82f6 | Bright blue #60a5fa | Gray #9ca3af |
| Acknowledge button | Amber #f59e0b | Bright amber #fbbf24 | Gray #9ca3af |
| Navigation button (active) | Slate #475569 | — | — |
| Navigation button (inactive) | Transparent | — | — |
| Generic action button | Slate #475569 | Lighter slate #64748b | Gray #9ca3af |

## Piping and Process Graphic Colors

For process flow diagrams and piping graphics:

| Element | Color | Hex |
|---------|-------|-----|
| Water / coolant | Blue | #3b82f6 |
| Steam | Light gray | #d1d5db |
| Air / gas | Cyan | #06b6d4 |
| Oil / hydraulic | Yellow | #eab308 |
| Product flow | Green | #22c55e |
| Waste / drain | Brown | #92400e |
| Electrical | Orange | #f97316 |
| Pipe outline (inactive) | Gray | #6b7280 |
| Pipe outline (active/flowing) | White | #e2e8f0 |

## Contrast requirements

Minimum contrast ratios for readability in industrial environments (stricter than WCAG AA due to glare and viewing distance):

- **Primary text on dark background**: 7:1 or better (slate-50 on slate-800 = ~13:1)
- **Status text on colored indicator**: 4.5:1 minimum (white on green/red/blue all pass)
- **Labels on card backgrounds**: 4.5:1 minimum

## Light theme variant

If the user requests a light theme (control room, office, or engineer workstation):

| Surface | Color | Hex |
|---------|-------|-----|
| Screen background | Light gray | #f1f5f9 (slate-100) |
| Card background | White | #ffffff |
| Header bar | Dark slate | #1e293b (slate-800) |
| Primary text | Dark | #1e293b (slate-800) |
| Secondary text | Medium | #64748b (slate-500) |

State and alarm colors remain the same in light theme — only surfaces and text change.

## Do not use

- **Pure black** (#000000) as a background — too harsh, causes eye strain. Use dark slate instead.
- **Bright/saturated backgrounds** for large areas — reserve saturated color for indicators and alerts.
- **Gradients** on state indicators — solid colors are faster to read.
- **More than 6–7 distinct colors** on any single screen — more than this creates confusion.
- **Red for anything other than fault/stop/critical** — red must always mean "problem" or "stop."
- **Green for anything other than running/OK/go** — green must always mean "good" or "active."
