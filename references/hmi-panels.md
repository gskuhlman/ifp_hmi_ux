# HMI Panel Hardware Database

Use this reference to look up panel specifications when the user names a specific model. Match the mockup resolution, aspect ratio, and design constraints to the target hardware.

If the user does not specify a panel, ask or default to a 10" widescreen (1024×600).

## How to use this data

1. Look up the panel model the user specifies.
2. Set the mockup viewport to the panel's native resolution.
3. Apply the minimum touch target size based on the panel's pixel density (PPI). The 20mm minimum translates to different pixel counts at different densities.
4. Note platform constraints — some panels run specific runtimes that limit what UI patterns are available in production (though the mockup itself is always React for preview purposes).

## Quick reference — minimum touch target by density

| PPI range | 20mm target in pixels |
|-----------|----------------------|
| 90–110 | 72–86 px |
| 110–130 | 86–102 px |
| 130–160 | 102–126 px |
| 160+ | 126+ px |

---

## Allen-Bradley PanelView Plus 7

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| 2711P-T4W22D8S | 4.3" | 480×272 | 16:9 | 128 | Resistive | FactoryTalk View ME | Very limited space. One task per screen. Large buttons only. |
| 2711P-T7C22D8S | 7" | 800×480 | 5:3 | 133 | Resistive | FactoryTalk View ME | Good for single-machine HMI. Keep layouts simple. |
| 2711P-T10C22D8S | 10" | 1024×600 | ~16:9 | 118 | Resistive | FactoryTalk View ME | Most common size. Good balance of space and readability. |
| 2711P-T12W22D8S | 12" | 1280×800 | 16:10 | 125 | Resistive | FactoryTalk View ME | Room for process graphics and trend panels. |
| 2711P-T15C22D8S | 15" | 1366×768 | 16:9 | 105 | Resistive | FactoryTalk View ME | Supervisor-level. Can show overview + detail. |
| 2711P-T19C22D8S | 19" | 1440×900 | 16:10 | 90 | Resistive | FactoryTalk View ME | Control room use. Multiple zones possible. |
| 2711P-T22C22D9P | 22" | 1920×1080 | 16:9 | 100 | Projected Cap | FactoryTalk View ME/SE | Full HD. Multi-touch. Good for SCADA overview screens. |

**Platform notes:** FactoryTalk View ME runs on the panel locally. Tags communicate via EtherNet/IP to ControlLogix/CompactLogix PLCs. Tag names must match the PLC program exactly. ME supports global objects, parameter files, and faceplates for reusable screen components.

---

## Allen-Bradley PanelView 5000

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| 2715P-T7CD | 7" | 800×480 | 5:3 | 133 | Projected Cap | Studio 5000 View Designer | Integrated with Logix. Tags defined in controller project. |
| 2715P-T9CD | 9" | 1024×600 | ~16:9 | 125 | Projected Cap | Studio 5000 View Designer | Mid-range. Good default choice for AB shops. |
| 2715P-T12CD | 12" | 1280×800 | 16:10 | 125 | Projected Cap | Studio 5000 View Designer | Solid workhorse. Room for graphics. |
| 2715P-T19CD | 19" | 1440×900 | 16:10 | 90 | Projected Cap | Studio 5000 View Designer | Large format. Can host complex process views. |

**Platform notes:** PanelView 5000 uses Studio 5000 View Designer (not FactoryTalk View ME). Tags are defined directly in the Logix controller project — no separate tag database. Supports multi-touch gestures.

---

## Siemens SIMATIC Comfort Panels

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| KTP400 Comfort | 4.3" | 480×272 | 16:9 | 128 | Resistive + Keys | WinCC Comfort | 4 function keys. Good for simple machine control. |
| KTP700 Comfort | 7" | 800×480 | 5:3 | 133 | Resistive + Keys | WinCC Comfort | 8 function keys. Versatile single-machine panel. |
| KTP900 Comfort | 9" | 800×480 | 5:3 | 104 | Resistive + Keys | WinCC Comfort | Same resolution as 7" but larger — bigger targets. |
| KTP1200 Comfort | 12" | 1280×800 | 16:10 | 125 | Resistive + Keys | WinCC Comfort | Good balance. Hardware keys useful for frequent actions. |
| TP1500 Comfort | 15" | 1366×768 | 16:9 | 105 | Resistive | WinCC Comfort | No function keys. Pure touch. |
| TP1900 Comfort | 19" | 1366×768 | 16:9 | 83 | Resistive | WinCC Comfort | Same resolution as 15" at larger size — very readable. |
| TP2200 Comfort | 22" | 1920×1080 | 16:9 | 100 | Resistive | WinCC Comfort | Full HD. Control room or line overview use. |

**Platform notes:** WinCC Comfort runs on the panel. Communicates via PROFINET, MPI, or PROFIBUS to S7-1200/S7-1500. Tags (called "HMI tags") are linked to PLC tags in TIA Portal. KTP models include hardware function keys — use these for frequently-used actions like Acknowledge or Mode Select.

---

## Siemens SIMATIC Unified Comfort Panels

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| MTP700 Unified | 7" | 800×480 | 5:3 | 133 | Projected Cap | WinCC Unified | Web-based runtime. Modern UI patterns. |
| MTP1000 Unified | 10" | 1280×800 | 16:10 | 150 | Projected Cap | WinCC Unified | Higher density — sharper text and graphics. |
| MTP1500 Unified | 15" | 1366×768 | 16:9 | 105 | Projected Cap | WinCC Unified | Multi-touch. Supports swipe gestures. |
| MTP2200 Unified | 22" | 1920×1080 | 16:9 | 100 | Projected Cap | WinCC Unified | Full HD. Multi-touch. |

**Platform notes:** WinCC Unified uses a web-based rendering engine (HTML/SVG). Supports modern UI patterns including swipe, pinch-to-zoom, and multi-touch. Good match for React-style mockups since the production environment is also web-rendered.

---

## Weintek cMT Series

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| cMT2078X | 7" | 800×480 | 5:3 | 133 | Resistive | EasyBuilder Pro | Cost-effective. Very common in small machine OEM. |
| cMT2108X | 10.1" | 1280×800 | 16:10 | 149 | Resistive | EasyBuilder Pro | Good resolution for the size. Solid mid-range choice. |
| cMT2158X | 15.6" | 1366×768 | 16:9 | 100 | Resistive | EasyBuilder Pro | Widescreen. Good for process overviews. |

**Platform notes:** EasyBuilder Pro is the development environment. Communicates via Modbus TCP/RTU, EtherNet/IP, PROFINET, and many other protocols. Tag addresses are defined by protocol (e.g., Modbus register addresses, or AB tag names). The cMT architecture supports remote access via cMT Viewer — consider that screens may be viewed on phones/tablets at different resolutions.

---

## Weintek iE Series (legacy but still common)

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| MT8071iE | 7" | 800×480 | 5:3 | 133 | Resistive | EasyBuilder Pro | Budget. Very popular for small machines. |
| MT8102iE | 10.1" | 1024×600 | ~16:9 | 118 | Resistive | EasyBuilder Pro | Standard small-machine panel. |
| MT8150iE | 15" | 1024×768 | 4:3 | 85 | Resistive | EasyBuilder Pro | 4:3 aspect — design accordingly. |

---

## Beijer X2 Series

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| X2 base 5 | 5" | 800×480 | 5:3 | 187 | Resistive | iX Runtime | Compact. High density — fine detail is legible. |
| X2 base 7 | 7" | 800×480 | 5:3 | 133 | Resistive | iX Runtime | Standard. Good for dedicated-machine use. |
| X2 pro 7 | 7" | 800×480 | 5:3 | 133 | Projected Cap | iX Runtime | Multi-touch version of base 7. |
| X2 pro 10 | 10.1" | 1280×800 | 16:10 | 149 | Projected Cap | iX Runtime | Solid mid-range multi-touch. |
| X2 pro 12 | 12.1" | 1280×800 | 16:10 | 124 | Projected Cap | iX Runtime | Larger targets at same resolution as 10". |
| X2 pro 15 | 15" | 1366×768 | 16:9 | 105 | Projected Cap | iX Runtime | Good for complex process displays. |

**Platform notes:** iX Developer is the IDE. iX Runtime supports vector graphics and animations natively. Communicates via most industrial protocols. Supports .NET scripting for advanced logic.

---

## Pro-face GP4000 Series

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| GP-4301TW | 5.7" | 640×480 | 4:3 | 140 | Resistive | GP-Pro EX | Legacy 4:3. Still common in Japanese machine builders. |
| GP-4501TW | 10.4" | 800×600 | 4:3 | 96 | Resistive | GP-Pro EX | 4:3 aspect. Design with square-ish layout. |
| GP-4601T | 12.1" | 1024×768 | 4:3 | 106 | Resistive | GP-Pro EX | 4:3. Good for retrofit. |
| GP-4301T (PFXGP) | 5.7" | 320×240 | 4:3 | 70 | Resistive | GP-Pro EX | Very low resolution. Only basic status + few buttons. |

**Platform notes:** Pro-face uses GP-Pro EX development environment. Popular in Asian machine OEMs. 4:3 aspect ratio is common — do not assume widescreen. Communicates via Modbus, MELSEC, PROFINET.

---

## Red Lion CR Series

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| CR1000 | 7" | 800×480 | 5:3 | 133 | Resistive | Crimson | Built-in protocol conversion. Good for mixed-protocol sites. |
| CR3000 | 10" | 1024×600 | ~16:9 | 118 | Resistive | Crimson | Protocol gateway + HMI in one. |
| CR3000-15 | 15" | 1024×768 | 4:3 | 85 | Resistive | Crimson | 4:3 format. |

**Platform notes:** Crimson software. Red Lion panels are known for protocol conversion — they can talk to Allen-Bradley, Siemens, Modbus, and many others simultaneously. Useful in retrofit or mixed-vendor environments. Tag mapping is done through Crimson's data model.

---

## IDEC HG Series

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| HG2G-5FT22TF-B | 4.3" | 480×272 | 16:9 | 128 | Resistive | WindO/I-NV4 | Budget small panel. Simple status and control. |
| HG2G-5TT22TF-B | 5.7" | 640×480 | 4:3 | 140 | Resistive | WindO/I-NV4 | Compact 4:3. |
| HG4G-CJT22MF-B | 10.1" | 1024×600 | ~16:9 | 118 | Projected Cap | WindO/I-NV4 | Good value mid-range. |

---

## Beckhoff Control Panels

| Model | Screen | Resolution | Aspect | PPI | Touch | Runtime | Notes |
|-------|--------|-----------|--------|-----|-------|---------|-------|
| CP6606 | 7" | 800×480 | 5:3 | 133 | Resistive | TwinCAT HMI | Runs TwinCAT HMI (web-based). |
| CP6706 | 12" | 1280×800 | 16:10 | 125 | Projected Cap | TwinCAT HMI | HTML5-based UI matches React mockup approach. |
| CP6906 | 15.6" | 1366×768 | 16:9 | 100 | Projected Cap | TwinCAT HMI | Full TwinCAT 3 integration. |
| CP7906 | 19" | 1366×768 | 16:9 | 83 | Projected Cap | TwinCAT HMI | Large panel. Same resolution as 15.6" — bigger targets. |

**Platform notes:** TwinCAT HMI is web-based (HTML5/JS). Mockups translate very directly to production since TwinCAT HMI uses the same tech stack. Tags are TwinCAT 3 PLC variables accessed via ADS protocol.

---

## Design guidance by screen size

### 4.3" and under (480×272)
- One task per screen. No multi-panel layouts.
- Maximum 4–6 touch targets per screen.
- Status + 2–3 buttons is the practical limit.
- Use full-width elements. No side-by-side columns.

### 7" (800×480)
- Single-machine primary screen works well.
- Header + one main zone + footer navigation.
- 6–10 touch targets per screen.
- Small trend charts are possible but tight.

### 10" (1024×600 or 1280×800)
- The sweet spot for most single-machine HMIs.
- Header + alarm banner + main zone + small trend area + footer.
- Can support a 2-column layout in the main zone.
- 10–15 interactive elements per screen.

### 12–15" (1280×800 to 1366×768)
- Room for process graphics with embedded status.
- 2–3 column layouts work.
- Can show trend panels alongside controls.
- Good for supervisor views with drill-down.

### 19–22" (1440×900 to 1920×1080)
- Multi-zone layouts: overview + detail simultaneously.
- SCADA-style multi-asset views.
- Can display alarm lists, trends, and controls together.
- Consider whether this is operator-local or control-room use — design accordingly.

### 4:3 panels (special consideration)
Some panels (especially Pro-face, Red Lion, older Weintek) use 4:3 aspect ratios. Do not design widescreen layouts for these. Use a more square, vertically-balanced layout with a center-aligned process graphic.
