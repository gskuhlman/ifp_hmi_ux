# Allen-Bradley Logix Tag Export Reference

Templates for exporting HMI tag tables into formats compatible with Studio 5000 Logix Designer (ControlLogix / CompactLogix) and FactoryTalk View ME/SE.

## Export format 1: L5X tag import snippet

L5X is the native XML interchange format for Logix controllers. Tags can be defined in a partial L5X file and imported into an existing project via Studio 5000's import feature.

### Single tag template

```xml
<Tag Name="{TagName}" TagType="Base" DataType="{DataType}" Radix="{Radix}" Constant="false"
     ExternalAccess="Read/Write">
  <Description>
    <![CDATA[{Description}]]>
  </Description>
  <Data Format="Decorated">
    <DataValue DataType="{DataType}" Radix="{Radix}" Value="{DefaultValue}"/>
  </Data>
</Tag>
```

### Data type mapping

| Generic type | Logix DataType | Radix | Default value |
|-------------|---------------|-------|---------------|
| Boolean | BOOL | Decimal | 0 |
| Integer (16-bit) | INT | Decimal | 0 |
| Integer (32-bit) | DINT | Decimal | 0 |
| Float (32-bit) | REAL | Float | 0.0 |
| String | STRING | — | '' |
| Timer | TIMER | — | (structured) |

### Complete L5X tag block example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<RSLogix5000Content SchemaRevision="1.0" SoftwareRevision="33.00"
                     TargetName="{ControllerName}" TargetType="Controller"
                     ContainsContext="true" ExportDate="2026-04-10" ExportOptions="DecoratedData">
<Controller Name="{ControllerName}">
<Tags>

  <!-- ============================================ -->
  <!-- HMI Tags — {ScreenName}                      -->
  <!-- Generated for: {MachineName}                 -->
  <!-- Platform: Allen-Bradley ControlLogix/CompactLogix -->
  <!-- Date: {GeneratedDate}                        -->
  <!-- ============================================ -->

  <!-- Commands (HMI → PLC) -->
  <Tag Name="PKG_Conv01_Start_CMD" TagType="Base" DataType="BOOL" Radix="Decimal"
       Constant="false" ExternalAccess="Read/Write">
    <Description><![CDATA[Operator start command — Conveyor 01]]></Description>
  </Tag>

  <Tag Name="PKG_Conv01_Stop_CMD" TagType="Base" DataType="BOOL" Radix="Decimal"
       Constant="false" ExternalAccess="Read/Write">
    <Description><![CDATA[Operator stop command — Conveyor 01]]></Description>
  </Tag>

  <Tag Name="PKG_Conv01_Reset_CMD" TagType="Base" DataType="BOOL" Radix="Decimal"
       Constant="false" ExternalAccess="Read/Write">
    <Description><![CDATA[Fault reset command — Conveyor 01]]></Description>
  </Tag>

  <!-- Feedback (PLC → HMI) -->
  <Tag Name="PKG_Conv01_Run_FB" TagType="Base" DataType="BOOL" Radix="Decimal"
       Constant="false" ExternalAccess="Read Only">
    <Description><![CDATA[Running feedback — Conveyor 01]]></Description>
  </Tag>

  <Tag Name="PKG_Conv01_Fault_ALM" TagType="Base" DataType="BOOL" Radix="Decimal"
       Constant="false" ExternalAccess="Read Only">
    <Description><![CDATA[Fault active — Conveyor 01]]></Description>
  </Tag>

  <!-- Process Values (PLC → HMI) -->
  <Tag Name="PKG_Conv01_Speed_PV" TagType="Base" DataType="REAL" Radix="Float"
       Constant="false" ExternalAccess="Read Only">
    <Description><![CDATA[Current speed [FPM] — Conveyor 01]]></Description>
  </Tag>

  <Tag Name="PKG_Conv01_Current_PV" TagType="Base" DataType="REAL" Radix="Float"
       Constant="false" ExternalAccess="Read Only">
    <Description><![CDATA[Motor current [A] — Conveyor 01]]></Description>
  </Tag>

  <!-- Setpoints (HMI → PLC) -->
  <Tag Name="PKG_Conv01_Speed_SP" TagType="Base" DataType="REAL" Radix="Float"
       Constant="false" ExternalAccess="Read/Write">
    <Description><![CDATA[Speed setpoint [FPM] — Conveyor 01]]></Description>
  </Tag>

  <!-- Status -->
  <Tag Name="PKG_Conv01_State_STS" TagType="Base" DataType="DINT" Radix="Decimal"
       Constant="false" ExternalAccess="Read Only">
    <Description><![CDATA[Machine state: 0=Idle, 1=Starting, 2=Running, 3=Stopping, 4=Faulted, 5=E-Stop]]></Description>
  </Tag>

</Tags>
</Controller>
</RSLogix5000Content>
```

### ExternalAccess settings

Use ExternalAccess to control HMI visibility:
- **Read/Write**: Commands and setpoints that the HMI writes to
- **Read Only**: Feedback, status, and process values that the HMI reads
- **None**: Internal PLC variables not exposed to HMI

## Export format 2: User-Defined Type (UDT)

For repeated equipment, define a UDT in L5X:

```xml
<DataType Name="UDT_Motor" Family="NoFamily" Class="User">
  <Description><![CDATA[Motor control and status structure]]></Description>
  <Members>
    <Member Name="Start_CMD" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Operator start command]]></Description>
    </Member>
    <Member Name="Stop_CMD" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Operator stop command]]></Description>
    </Member>
    <Member Name="Reset_CMD" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Fault reset command]]></Description>
    </Member>
    <Member Name="Jog_Fwd_CMD" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Jog forward command]]></Description>
    </Member>
    <Member Name="Jog_Rev_CMD" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Jog reverse command]]></Description>
    </Member>
    <Member Name="Run_FB" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[Running feedback]]></Description>
    </Member>
    <Member Name="Ready_STS" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[Ready status]]></Description>
    </Member>
    <Member Name="Fault_ALM" DataType="BOOL" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[Fault active]]></Description>
    </Member>
    <Member Name="Speed_PV" DataType="REAL" Dimension="0" Radix="Float" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[Current speed]]></Description>
    </Member>
    <Member Name="Current_PV" DataType="REAL" Dimension="0" Radix="Float" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[Motor current [A]]]></Description>
    </Member>
    <Member Name="Speed_SP" DataType="REAL" Dimension="0" Radix="Float" Hidden="false"
            ExternalAccess="Read/Write">
      <Description><![CDATA[Speed setpoint]]></Description>
    </Member>
    <Member Name="State_STS" DataType="DINT" Dimension="0" Radix="Decimal" Hidden="false"
            ExternalAccess="Read Only">
      <Description><![CDATA[State: 0=Idle, 1=Starting, 2=Running, 3=Stopping, 4=Faulted]]></Description>
    </Member>
  </Members>
</DataType>
```

Then create instances:

```xml
<Tag Name="PKG_Conv01" TagType="Base" DataType="UDT_Motor" Constant="false"
     ExternalAccess="Read/Write">
  <Description><![CDATA[Conveyor 01 — Packaging Line]]></Description>
</Tag>
<Tag Name="PKG_Conv02" TagType="Base" DataType="UDT_Motor" Constant="false"
     ExternalAccess="Read/Write">
  <Description><![CDATA[Conveyor 02 — Packaging Line]]></Description>
</Tag>
```

### Standard UDT names

| UDT name | Use case |
|----------|----------|
| UDT_Motor | Motor drive: conveyor, pump, fan, spindle |
| UDT_Valve | On/off or modulating valve |
| UDT_Cylinder | Pneumatic/hydraulic cylinder with extend/retract |
| UDT_Analog_In | Analog input with alarm limits |
| UDT_Analog_Out | Analog output with setpoint |
| UDT_Alarm | Alarm with severity, ack, reset |

## Export format 3: CSV for FactoryTalk View ME tag import

FactoryTalk View ME can import tags via CSV. This format creates HMI tags that are linked to the Logix controller tags by shortcut path.

### CSV format

```csv
"Tag Name","Type","Data Type","Description","Address","Initial Value"
"PKG_Conv01_Start_CMD","Alias","Digital","Operator start command — Conveyor 01","[PLC]PKG_Conv01_Start_CMD",""
"PKG_Conv01_Stop_CMD","Alias","Digital","Operator stop command — Conveyor 01","[PLC]PKG_Conv01_Stop_CMD",""
"PKG_Conv01_Run_FB","Alias","Digital","Running feedback — Conveyor 01","[PLC]PKG_Conv01_Run_FB",""
"PKG_Conv01_Fault_ALM","Alias","Digital","Fault active — Conveyor 01","[PLC]PKG_Conv01_Fault_ALM",""
"PKG_Conv01_Speed_PV","Alias","Analog","Current speed [FPM] — Conveyor 01","[PLC]PKG_Conv01_Speed_PV",""
"PKG_Conv01_Speed_SP","Alias","Analog","Speed setpoint [FPM] — Conveyor 01","[PLC]PKG_Conv01_Speed_SP",""
"PKG_Conv01_State_STS","Alias","Analog","Machine state enum — Conveyor 01","[PLC]PKG_Conv01_State_STS",""
```

### CSV column definitions

| Column | Description |
|--------|-------------|
| Tag Name | The HMI tag name (usually matches the PLC tag name) |
| Type | "Alias" for tags linked to a PLC, "Memory" for HMI-only tags |
| Data Type | "Digital" for BOOL, "Analog" for INT/DINT/REAL, "String" for STRING |
| Description | Tag description (max 128 chars in FTView) |
| Address | `[ShortcutName]PLC_Tag_Name` — the shortcut is configured in FTView ME to point to the Logix controller |
| Initial Value | Usually blank for alias tags |

### For UDT-based PLC tags

When the PLC uses UDTs, the FTView ME address references the UDT member:

```csv
"PKG_Conv01_Start","Alias","Digital","Start command","[PLC]PKG_Conv01.Start_CMD",""
"PKG_Conv01_RunFB","Alias","Digital","Running feedback","[PLC]PKG_Conv01.Run_FB",""
"PKG_Conv01_Speed","Alias","Analog","Current speed","[PLC]PKG_Conv01.Speed_PV",""
```

## Export format 4: FactoryTalk View SE tag import

For FTView SE (distributed / SCADA), tags are defined in the FactoryTalk Linx server and referenced by the full path:

```
{Area}\{Server}\{Controller}\PKG_Conv01.Run_FB
```

SE tags are typically configured through FactoryTalk Administration Console rather than CSV import, but the tag names and data types remain the same.

## Generation instructions

When generating an Allen-Bradley export:

1. Collect all tags from the mockup's `data-tag` attributes.
2. Determine each tag's Logix data type and ExternalAccess setting.
3. Group tags by equipment — if multiple instances share the same structure, generate a UDT.
4. Generate the L5X XML with proper formatting and descriptions.
5. Generate the FTView ME CSV with the `[ShortcutName]` prefix (ask the user for their shortcut name, default to `[PLC]`).
6. Include the header comment with machine name, screen name, and date.
7. Save as `{machine_name}_tags.L5X` (for Logix import) and `{machine_name}_hmi_tags.csv` (for FTView import).

## Tag naming rules for Logix

- Maximum 40 characters
- Start with a letter or underscore
- No spaces — use underscores
- No special characters except underscore
- Case-insensitive (but convention is PascalCase or underscore-separated)
- Array tags use square brackets: `Conveyor[0].Run_CMD`
- UDT member access uses dot notation: `PKG_Conv01.Start_CMD`
