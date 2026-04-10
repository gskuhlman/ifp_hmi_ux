# Codesys Tag Export Reference

Templates for exporting HMI tag tables into formats that Codesys IDE can import or that can be pasted directly into a Codesys project.

## Export format 1: Global Variable List (GVL) declaration

This is the most common approach for Codesys-based HMIs. Generate a `.TcGVL` or plain structured text file that declares all HMI tags as global variables.

### Template

```iecst
VAR_GLOBAL
    // ============================================
    // HMI Tags — {ScreenName}
    // Generated for: {MachineName}
    // Platform: Codesys 3.5
    // Date: {GeneratedDate}
    // ============================================

    // --- Commands (HMI → PLC) ---
    {Area}_{Equipment}_Start_CMD        : BOOL := FALSE;    // Operator start command
    {Area}_{Equipment}_Stop_CMD         : BOOL := FALSE;    // Operator stop command
    {Area}_{Equipment}_Reset_CMD        : BOOL := FALSE;    // Fault reset command
    {Area}_{Equipment}_Ack_CMD          : BOOL := FALSE;    // Alarm acknowledge command

    // --- Feedback (PLC → HMI) ---
    {Area}_{Equipment}_Run_FB           : BOOL := FALSE;    // Running feedback
    {Area}_{Equipment}_Ready_STS        : BOOL := FALSE;    // Ready status
    {Area}_{Equipment}_Fault_ALM        : BOOL := FALSE;    // Fault active

    // --- Process Values (PLC → HMI) ---
    {Area}_{Equipment}_Speed_PV         : REAL := 0.0;      // Current speed [RPM]
    {Area}_{Equipment}_Current_PV       : REAL := 0.0;      // Motor current [A]
    {Area}_{Equipment}_Temp_PV          : REAL := 0.0;      // Temperature [°C]

    // --- Setpoints (HMI → PLC) ---
    {Area}_{Equipment}_Speed_SP         : REAL := 0.0;      // Speed setpoint [RPM]

    // --- Status Words (PLC → HMI) ---
    {Area}_{Equipment}_State_STS        : INT := 0;         // Machine state enum
    {Area}_{Equipment}_Mode_STS         : INT := 0;         // Mode: 0=Manual, 1=Semi, 2=Auto
END_VAR
```

### State enum pattern

When a tag represents a state machine, define the states as constants:

```iecst
VAR_GLOBAL CONSTANT
    // Machine State Enum — {Equipment}
    ST_IDLE         : INT := 0;
    ST_STARTING     : INT := 1;
    ST_RUNNING      : INT := 2;
    ST_STOPPING     : INT := 3;
    ST_FAULTED      : INT := 4;
    ST_ESTOP        : INT := 5;

    // Mode Enum
    MODE_MANUAL     : INT := 0;
    MODE_SEMI_AUTO  : INT := 1;
    MODE_AUTO       : INT := 2;
END_VAR
```

## Export format 2: Structured type (DUT) for per-device tag groups

When multiple instances of the same equipment exist (e.g., Conveyor 01 through 05), define a structured type and instantiate it:

```iecst
TYPE ST_Motor :
STRUCT
    // Commands (HMI → PLC)
    Start_CMD       : BOOL := FALSE;
    Stop_CMD        : BOOL := FALSE;
    Reset_CMD       : BOOL := FALSE;
    Jog_Fwd_CMD     : BOOL := FALSE;
    Jog_Rev_CMD     : BOOL := FALSE;

    // Feedback (PLC → HMI)
    Run_FB          : BOOL := FALSE;
    Ready_STS       : BOOL := FALSE;
    Fault_ALM       : BOOL := FALSE;
    Overload_ALM    : BOOL := FALSE;

    // Process values (PLC → HMI)
    Speed_PV        : REAL := 0.0;      // [RPM]
    Current_PV      : REAL := 0.0;      // [A]
    Temp_PV         : REAL := 0.0;      // [°C]

    // Setpoints (HMI → PLC)
    Speed_SP        : REAL := 0.0;      // [RPM]

    // Status
    State_STS       : INT := 0;         // State enum
    Mode_STS        : INT := 0;         // Mode enum
END_STRUCT
END_TYPE
```

Then instantiate in the GVL:

```iecst
VAR_GLOBAL
    PKG_Conv01      : ST_Motor;
    PKG_Conv02      : ST_Motor;
    PKG_Conv03      : ST_Motor;
    PKG_MainDrive   : ST_Motor;
END_VAR
```

### Common structured types to offer

| Type name | Use case |
|-----------|----------|
| ST_Motor | Any motor: conveyor, pump, fan, spindle |
| ST_Valve | On/off or modulating valve |
| ST_Cylinder | Pneumatic or hydraulic cylinder |
| ST_Analog_Input | Any analog sensor (temp, pressure, level, flow) |
| ST_Analog_Output | Any analog output (speed ref, position ref) |
| ST_Alarm | Individual alarm with ack/reset |

#### ST_Valve template

```iecst
TYPE ST_Valve :
STRUCT
    Open_CMD        : BOOL := FALSE;
    Close_CMD       : BOOL := FALSE;
    IsOpen_FB       : BOOL := FALSE;
    IsClosed_FB     : BOOL := FALSE;
    Fault_ALM       : BOOL := FALSE;
    Transit_STS     : BOOL := FALSE;    // TRUE while transitioning
    Position_PV     : REAL := 0.0;      // 0-100% for modulating valves
    Position_SP     : REAL := 0.0;      // Setpoint for modulating valves
END_STRUCT
END_TYPE
```

#### ST_Cylinder template

```iecst
TYPE ST_Cylinder :
STRUCT
    Extend_CMD      : BOOL := FALSE;
    Retract_CMD     : BOOL := FALSE;
    Extended_FB     : BOOL := FALSE;    // Prox sensor - extended position
    Retracted_FB    : BOOL := FALSE;    // Prox sensor - retracted position
    Fault_ALM       : BOOL := FALSE;    // Timeout or sensor fault
    Transit_STS     : BOOL := FALSE;
END_STRUCT
END_TYPE
```

#### ST_Analog_Input template

```iecst
TYPE ST_Analog_Input :
STRUCT
    PV              : REAL := 0.0;      // Process value (engineering units)
    Raw             : INT := 0;         // Raw ADC value
    HiHi_ALM        : BOOL := FALSE;   // High-high alarm
    Hi_ALM          : BOOL := FALSE;    // High alarm
    Lo_ALM          : BOOL := FALSE;    // Low alarm
    LoLo_ALM        : BOOL := FALSE;    // Low-low alarm
    Fault_ALM       : BOOL := FALSE;    // Sensor fault / out of range
    HiHi_Limit      : REAL := 0.0;     // Alarm limits (configurable)
    Hi_Limit        : REAL := 0.0;
    Lo_Limit        : REAL := 0.0;
    LoLo_Limit      : REAL := 0.0;
    Unit            : STRING(8) := '';  // Engineering unit label
END_STRUCT
END_TYPE
```

## Export format 3: CSV for Codesys symbol import

Some Codesys-based HMI tools (CODESYS Visualization, Weintek EasyBuilder with Codesys driver) accept CSV tag imports:

```csv
TagName,DataType,Address,Description,Direction
{Area}_{Equipment}_Start_CMD,BOOL,GVL.{Area}_{Equipment}_Start_CMD,Operator start command,HMI_TO_PLC
{Area}_{Equipment}_Run_FB,BOOL,GVL.{Area}_{Equipment}_Run_FB,Running feedback,PLC_TO_HMI
{Area}_{Equipment}_Speed_PV,REAL,GVL.{Area}_{Equipment}_Speed_PV,Current speed [RPM],PLC_TO_HMI
{Area}_{Equipment}_Speed_SP,REAL,GVL.{Area}_{Equipment}_Speed_SP,Speed setpoint [RPM],HMI_TO_PLC
```

## Generation instructions

When generating a Codesys export:

1. Collect all tags from the mockup's `data-tag` attributes.
2. Determine each tag's data type from the tag table.
3. Group tags by equipment — if multiple instances share the same structure, generate a DUT.
4. Generate the GVL with all tag declarations and descriptive comments.
5. If structured types were used, generate the DUT definitions first, then the GVL instances.
6. Include the header comment block with machine name, screen name, and date.
7. Save as `{machine_name}_tags.gvl` (for the variable list) and `{type_name}.dut` (for structured types).
8. Optionally generate the CSV version as `{machine_name}_tags.csv`.

## Codesys Visualization tag binding

If the target is CODESYS Visualization (the built-in Codesys HMI), tags are referenced in visualization elements using the full path:

```
GVL.PKG_Conv01.Run_FB          (for structured type instances)
GVL.PKG_Conv01_Run_FB           (for flat GVL variables)
```

Elements in CODESYS Visualization use these property bindings:
- **Toggle variable**: For buttons — the tag that gets toggled on press
- **Variable**: For indicators — the tag that drives the display
- **Text variable**: For numeric displays — the tag whose value is shown
- **Color variable**: For state-dependent coloring
