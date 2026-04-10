# Screen Templates for Common Machine Types

Starter layouts for common industrial machine categories. Use these as a starting point and adapt based on the specific machine, user role, and panel size.

Each template defines the screen hierarchy (which screens to build), the primary screen layout, and the key tags needed.

---

## Packaging Line

A typical packaging line: product infeed, filling/forming, sealing, labeling, outfeed conveyor, case packer.

### Screen hierarchy

| Screen | Purpose | Audience |
|--------|---------|----------|
| Main Overview | Line status, run/stop, active alarms, current recipe | Operator |
| Station Detail | Per-station controls and status (one screen per station) | Operator |
| Recipe Select | Load, view, compare recipes; setpoint groups | Operator |
| Alarm List | Active + historical alarms with acknowledge | Operator |
| Trends | Production rate, reject rate, temperature, pressure over time | Operator / Supervisor |
| Production Count | Good count, reject count, batch progress, OEE | Supervisor |
| Maintenance | Runtime hours, cycle counts, calibration due dates | Maintenance |
| I/O Diagnostics | Raw I/O states for troubleshooting | Maintenance / Engineer |

### Main overview layout (10" panel, 1024×600)

```
┌─────────────────────────────────────────────────────────┐
│ HEADER: "Packaging Line 01"  [RUNNING]  [2 ALARMS]  12:34 │
├─────────────────────────────────────────────────────────┤
│ ALARM BANNER: "WARNING: Labeler low ribbon — replace soon" │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   ┌──────┐   ┌──────┐   ┌──────┐   ┌──────┐   ┌──────┐ │
│   │INFEED│──▶│FILLER│──▶│SEALER│──▶│LABEL │──▶│OUTFED│ │
│   │  ●   │   │  ●   │   │  ●   │   │  ◐   │   │  ●   │ │
│   │ 120  │   │ 118  │   │ 119  │   │ 115  │   │ 120  │ │
│   │ ppm  │   │ ppm  │   │ ppm  │   │ ppm  │   │ ppm  │ │
│   └──────┘   └──────┘   └──────┘   └──────┘   └──────┘ │
│                                                         │
│  ┌─────────┐  ┌─────────┐  ┌──────────────────────────┐ │
│  │ [START] │  │ [STOP]  │  │  Recipe: PROD-2847       │ │
│  │         │  │         │  │  Batch: 1247/5000        │ │
│  └─────────┘  └─────────┘  │  Good: 1198  Reject: 49 │ │
│                             └──────────────────────────┘ │
├─────────────────────────────────────────────────────────┤
│ [Main] [Stations] [Recipe] [Alarms] [Trends] [Maint]   │
└─────────────────────────────────────────────────────────┘
```

Station indicators: ● green = running, ◐ amber = warning, ○ gray = stopped, ● red = fault.

### Key tags

- `PKG_Line01_Start_CMD`, `_Stop_CMD`, `_Reset_CMD`, `_State_STS`
- Per station: `PKG_{Station}_Run_FB`, `_Speed_PV`, `_Fault_ALM`
- Production: `PKG_Line01_GoodCount_PV`, `_RejectCount_PV`, `_BatchTarget_SP`
- Recipe: `PKG_Line01_RecipeID_STS`, `_RecipeName_STS`

---

## Conveyor System

A multi-segment conveyor with zones, merge/divert points, and accumulation.

### Screen hierarchy

| Screen | Purpose |
|--------|---------|
| System Overview | All conveyor segments with flow direction and status |
| Zone Detail | Per-zone controls, speed, photo-eye status |
| Alarm List | Jams, motor faults, photo-eye faults |
| Diagnostics | I/O status, encoder counts, VFD parameters |

### System overview layout (12" panel, 1280×800)

```
┌──────────────────────────────────────────────────────────────┐
│ HEADER: "Conveyor System"  [RUNNING]  [0 ALARMS]  12:34     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   ═══[Z1]═══►═══[Z2]═══►═══[Z3]═══►═══[Z4]═══►            │
│    45 FPM      45 FPM      60 FPM      60 FPM               │
│                      │                                       │
│                      ▼                                       │
│                 ═══[Z5]═══►═══[Z6]═══►                      │
│                  30 FPM      30 FPM                          │
│                                                              │
│   Legend: ● Running  ○ Stopped  ● Faulted  ◐ Accumulating   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐    │
│  │ MODE: [MANUAL] [SEMI-AUTO] [AUTO]                    │    │
│  │                                                      │    │
│  │ [START ALL]  [STOP ALL]  [RESET]  [E-STOP STATUS: OK]│    │
│  └──────────────────────────────────────────────────────┘    │
├──────────────────────────────────────────────────────────────┤
│ [Overview] [Zone Detail] [Alarms] [Diagnostics]              │
└──────────────────────────────────────────────────────────────┘
```

### Key tags per zone

- `CONV_Z{n}_Run_CMD`, `_Stop_CMD`, `_Run_FB`
- `CONV_Z{n}_Speed_SP`, `_Speed_PV`
- `CONV_Z{n}_PhotoEye_STS` (product detected)
- `CONV_Z{n}_Jam_ALM`, `_MotorFault_ALM`
- `CONV_Z{n}_Accumulate_STS` (zone holding product)

---

## CNC / Machining Center

Single spindle CNC with tool changer, coolant system, and part program management.

### Screen hierarchy

| Screen | Purpose |
|--------|---------|
| Main Status | Spindle state, active program, position, cycle time |
| Program Management | Load/select part programs, view program list |
| Tool Management | Tool life, offsets, tool change status |
| Alarm / Message | Active alarms, operator messages, M-code prompts |
| Maintenance | Spindle hours, axis lubrication, filter status |

### Main status layout (10" panel, 1024×600)

```
┌─────────────────────────────────────────────────────────┐
│ HEADER: "CNC Mill 03"  [CYCLE ACTIVE]  [0 ALARMS]      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────┐  ┌────────────────────────────────┐    │
│  │  SPINDLE     │  │  POSITION                     │    │
│  │  ● 8500 RPM  │  │  X:  125.432 mm              │    │
│  │  Load: 42%   │  │  Y:   87.215 mm              │    │
│  │  Tool: T07   │  │  Z:  -12.500 mm              │    │
│  └─────────────┘  │  Feed: 2400 mm/min            │    │
│                    └────────────────────────────────┘    │
│  ┌──────────────────────────────────────────────────┐   │
│  │  Program: O1234 "BRACKET-A"                      │   │
│  │  Block: N0450                                    │   │
│  │  Cycle: 2:34 / 4:12 est     Parts: 47/100       │   │
│  └──────────────────────────────────────────────────┘   │
│                                                         │
│  [CYCLE START]  [FEED HOLD]  [RESET]  [DOOR: CLOSED]   │
├─────────────────────────────────────────────────────────┤
│ [Status] [Programs] [Tools] [Alarms] [Maint]           │
└─────────────────────────────────────────────────────────┘
```

---

## Press / Stamping

Hydraulic or mechanical press with tonnage monitoring, die protection, and stroke control.

### Screen hierarchy

| Screen | Purpose |
|--------|---------|
| Main | Press state, tonnage, stroke position, cycle count |
| Die Setup | Die number, shut height, tonnage limits, counterbalance |
| Tonnage Monitor | Real-time tonnage curve, channel readings, limits |
| Alarm List | Die protection, overload, lubrication faults |
| Production | Parts count, good/reject, downtime tracking |

### Main layout (7" panel, 800×480)

```
┌───────────────────────────────────────────────┐
│ HEADER: "Press 02"  [TOP DEAD CENTER]         │
├───────────────────────────────────────────────┤
│                                               │
│  ┌─────────┐  ┌────────────────────────────┐  │
│  │ TONNAGE │  │  Stroke: 0.0 mm (TDC)     │  │
│  │  0 TON  │  │  SPM: 45                  │  │
│  │  /150   │  │  Die: D-2847              │  │
│  └─────────┘  │  Count: 12,847            │  │
│               └────────────────────────────┘  │
│                                               │
│  [CYCLE START]  [STOP]  [INCH]  [RESET]       │
│  Mode: [STROKE] [CONT] [SINGLE]              │
├───────────────────────────────────────────────┤
│ [Main] [Die Setup] [Tonnage] [Alarms]         │
└───────────────────────────────────────────────┘
```

---

## Filling / Dispensing Station

Liquid or powder fill station with multiple fill heads, scale feedback, and recipe-driven fill targets.

### Screen hierarchy

| Screen | Purpose |
|--------|---------|
| Main | Fill status per head, target vs actual, line speed |
| Recipe | Fill targets, tolerances, product selection |
| Trends | Fill weight trend, reject trend, temperature |
| Alarm | Underfill, overfill, scale fault, valve fault |
| CIP (Clean-in-Place) | CIP sequence status, chemical levels, temperature |

---

## Supervisor / Multi-Asset Dashboard

For control rooms or line supervisors monitoring multiple machines.

### Layout guidance (15"+ panel or PC, 1366×768 minimum)

```
┌──────────────────────────────────────────────────────────────┐
│ HEADER: "Plant Floor Overview"  [4 lines]  [1 ALARM]  12:34 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐         │
│  │ LINE 01      │ │ LINE 02      │ │ LINE 03      │         │
│  │ ● RUNNING    │ │ ● RUNNING    │ │ ● FAULTED    │         │
│  │ 120 ppm      │ │ 95 ppm       │ │ 0 ppm        │         │
│  │ OEE: 87%     │ │ OEE: 72%     │ │ OEE: --      │         │
│  │ Batch: 78%   │ │ Batch: 45%   │ │ [VIEW]       │         │
│  └──────────────┘ └──────────────┘ └──────────────┘         │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐    │
│  │ PRODUCTION TREND (last 8 hours)                      │    │
│  │  ▁▂▃▅▇▇▆▇▅▃▁▁▂▃▅▇▇▆▅▇▇▆▅▃▂▁▁▂▃▅▆▇▇               │    │
│  └──────────────────────────────────────────────────────┘    │
│                                                              │
│  ACTIVE ALARMS:                                              │
│  ● CRITICAL: Line 03 — Main drive fault (12:31)             │
├──────────────────────────────────────────────────────────────┤
│ [Overview] [Line 01] [Line 02] [Line 03] [Alarms] [Reports] │
└──────────────────────────────────────────────────────────────┘
```

### Design rules for multi-asset views

- Each asset card shows: name, state (color-coded), primary metric (rate/speed), secondary metric (OEE/batch progress).
- Tap a card to drill into that asset's detail screens.
- Keep the overview to one screen — no scrolling. If more than 6–8 assets, use a grid layout with smaller cards.
- Show only the most critical alarm per asset on the overview. Full alarm list is on a separate screen.
- Trend chart shows aggregate production, not individual asset detail.

---

## Assembly Cell

Robotic or manual assembly cell with multiple stations, part tracking, and quality checks.

### Key screens

| Screen | Purpose |
|--------|---------|
| Cell Overview | Station status map, cycle time, part in progress |
| Station Detail | Per-station actuators, sensors, quality checks |
| Part Tracking | Current part serial, station history, pass/fail |
| Quality | SPC charts, reject reasons, measurement data |
| Robot Status | Joint positions, program active, cycle count (if robotic) |

---

## General guidance for all templates

- These layouts assume the operator's primary task is monitoring and occasional intervention. If the machine requires constant manual input (e.g., manual press, manual assembly), put controls front-and-center and reduce the status display area.
- Every template should include an Alarm screen and a way to acknowledge alarms from any screen (via the alarm banner).
- Navigation depth should not exceed 2 levels for operators. Maintenance and engineer screens can be 3 levels deep.
- Every screen should display the machine state in the header so the operator always knows what mode the machine is in, regardless of which screen they're viewing.
