# HMI Component Library

Reusable React component patterns for industrial HMI mockups. Use these as building blocks — adapt sizing and layout to the target panel resolution.

All components use Tailwind utility classes. Every interactive or data-bound element includes a `data-tag` attribute for PLC tag binding.

## Color constants

Import the color palette from `color-standards.md`. Use these CSS custom properties or Tailwind classes consistently:

```jsx
const COLORS = {
  // Machine states
  running: '#22c55e',      // green-500 — actively running
  stopped: '#6b7280',      // gray-500 — stopped / idle
  faulted: '#ef4444',      // red-500 — faulted
  warning: '#f59e0b',      // amber-500 — warning condition
  disabled: '#d1d5db',     // gray-300 — control disabled

  // Backgrounds
  panelBg: '#1e293b',      // slate-800 — main background
  cardBg: '#334155',       // slate-700 — card/section background
  headerBg: '#0f172a',     // slate-900 — header bar

  // Text
  textPrimary: '#f8fafc',  // slate-50
  textSecondary: '#94a3b8', // slate-400
  textOnAlert: '#ffffff',

  // Alarm severities
  alarmCritical: '#dc2626', // red-600
  alarmWarning: '#d97706',  // amber-600
  alarmInfo: '#2563eb',     // blue-600
  alarmCleared: '#16a34a',  // green-600
};
```

---

## Header Bar

The top bar present on every screen. Shows machine identity, state, alarm summary, and time.

```jsx
function HeaderBar({ machineName, machineState, alarmCount, currentScreen }) {
  const stateColors = {
    Running: COLORS.running,
    Stopped: COLORS.stopped,
    Faulted: COLORS.faulted,
    Warning: COLORS.warning,
  };

  return (
    <div className="flex items-center justify-between px-4 py-2"
         style={{ backgroundColor: COLORS.headerBg }}>
      <div className="flex items-center gap-3">
        <span className="text-lg font-bold" style={{ color: COLORS.textPrimary }}>
          {machineName}
        </span>
        <span
          className="px-3 py-1 rounded text-sm font-semibold"
          style={{
            backgroundColor: stateColors[machineState] || COLORS.stopped,
            color: COLORS.textOnAlert
          }}
          data-tag="Machine_State_STS"
        >
          {machineState}
        </span>
      </div>

      <div className="flex items-center gap-4">
        {alarmCount > 0 && (
          <span
            className="px-2 py-1 rounded text-sm font-bold animate-pulse"
            style={{ backgroundColor: COLORS.alarmCritical, color: COLORS.textOnAlert }}
            data-tag="Active_Alarm_Count_PV"
          >
            {alarmCount} ALARM{alarmCount > 1 ? 'S' : ''}
          </span>
        )}
        <span className="text-sm" style={{ color: COLORS.textSecondary }}>
          {currentScreen}
        </span>
        <span className="text-sm" style={{ color: COLORS.textSecondary }}>
          {new Date().toLocaleTimeString()}
        </span>
      </div>
    </div>
  );
}
```

---

## Alarm Banner

Sits below the header. Shows the most recent critical/warning alarm. Always visible.

```jsx
function AlarmBanner({ alarms }) {
  const activeAlarm = alarms.find(a => a.active && !a.acknowledged);

  if (!activeAlarm) {
    return (
      <div className="px-4 py-1 text-sm"
           style={{ backgroundColor: COLORS.cardBg, color: COLORS.textSecondary }}>
        No active alarms
      </div>
    );
  }

  const bgColor = activeAlarm.severity === 'critical'
    ? COLORS.alarmCritical
    : COLORS.alarmWarning;

  return (
    <div
      className="flex items-center justify-between px-4 py-2 animate-pulse"
      style={{ backgroundColor: bgColor, color: COLORS.textOnAlert }}
    >
      <div className="flex items-center gap-3">
        <span className="font-bold text-sm">{activeAlarm.severity.toUpperCase()}</span>
        <span className="text-sm">{activeAlarm.message}</span>
      </div>
      <div className="flex items-center gap-2">
        <span className="text-xs">{activeAlarm.timestamp}</span>
        <button
          className="px-3 py-1 rounded text-xs font-bold border border-white"
          data-tag={`${activeAlarm.tagPrefix}_ALM_ACK_CMD`}
          onClick={() => {/* acknowledge handler */}}
        >
          ACK
        </button>
      </div>
    </div>
  );
}
```

---

## Pushbutton (Command Button)

Standard operator pushbutton for commands like Start, Stop, Reset.

```jsx
function PushButton({ label, tagName, color, disabled, disabledReason, onPress, requireConfirm }) {
  const [confirming, setConfirming] = useState(false);

  const handlePress = () => {
    if (requireConfirm && !confirming) {
      setConfirming(true);
      setTimeout(() => setConfirming(false), 3000); // auto-cancel after 3s
      return;
    }
    setConfirming(false);
    onPress?.();
  };

  return (
    <div className="flex flex-col items-center gap-1">
      <button
        className="px-6 py-3 rounded-lg font-bold text-base transition-all"
        style={{
          backgroundColor: disabled ? COLORS.disabled : color,
          color: disabled ? '#9ca3af' : COLORS.textOnAlert,
          cursor: disabled ? 'not-allowed' : 'pointer',
          minWidth: '80px',
          minHeight: '48px',
        }}
        disabled={disabled}
        data-tag={tagName}
        onClick={handlePress}
      >
        {confirming ? 'CONFIRM?' : label}
      </button>
      {disabled && disabledReason && (
        <span className="text-xs text-center" style={{ color: COLORS.warning }}>
          {disabledReason}
        </span>
      )}
    </div>
  );
}
```

Usage:
```jsx
<PushButton
  label="START"
  tagName="PKG_Line01_Start_CMD"
  color={COLORS.running}
  disabled={!safetyOk}
  disabledReason="Safety gate open"
  requireConfirm={false}
/>
<PushButton
  label="STOP"
  tagName="PKG_Line01_Stop_CMD"
  color={COLORS.faulted}
  requireConfirm={true}
/>
<PushButton
  label="RESET"
  tagName="PKG_Line01_Reset_CMD"
  color="#3b82f6"
  disabled={!faultActive}
  disabledReason="No active fault"
/>
```

---

## Indicator Light

Round or square status indicator for binary states (on/off, running/stopped, open/closed).

```jsx
function IndicatorLight({ label, tagName, active, activeColor, inactiveColor, blinking }) {
  return (
    <div className="flex flex-col items-center gap-1">
      <div
        className={`w-8 h-8 rounded-full border-2 border-gray-600 ${blinking && active ? 'animate-pulse' : ''}`}
        style={{
          backgroundColor: active ? (activeColor || COLORS.running) : (inactiveColor || COLORS.disabled),
          boxShadow: active ? `0 0 8px ${activeColor || COLORS.running}` : 'none',
        }}
        data-tag={tagName}
      />
      <span className="text-xs text-center" style={{ color: COLORS.textSecondary }}>
        {label}
      </span>
    </div>
  );
}
```

Usage:
```jsx
<IndicatorLight label="RUNNING" tagName="PKG_Conv01_Run_FB" active={convRunning} activeColor={COLORS.running} />
<IndicatorLight label="FAULT" tagName="PKG_Conv01_Fault_ALM" active={convFault} activeColor={COLORS.faulted} blinking />
<IndicatorLight label="READY" tagName="PKG_Conv01_Ready_STS" active={convReady} activeColor="#3b82f6" />
```

---

## Motor Block

Composite component showing a motor's full status: run state, speed, current, fault.

```jsx
function MotorBlock({ name, tagPrefix, running, speed, current, faulted, speedUnit }) {
  const borderColor = faulted ? COLORS.faulted : running ? COLORS.running : COLORS.stopped;

  return (
    <div
      className="rounded-lg p-3 min-w-[160px]"
      style={{ backgroundColor: COLORS.cardBg, border: `2px solid ${borderColor}` }}
    >
      <div className="flex items-center justify-between mb-2">
        <span className="text-sm font-bold" style={{ color: COLORS.textPrimary }}>{name}</span>
        <IndicatorLight
          label=""
          tagName={`${tagPrefix}_Run_FB`}
          active={running}
          activeColor={COLORS.running}
        />
      </div>

      <div className="grid grid-cols-2 gap-1 text-xs">
        <span style={{ color: COLORS.textSecondary }}>Speed</span>
        <span className="text-right font-mono" style={{ color: COLORS.textPrimary }}
              data-tag={`${tagPrefix}_Speed_PV`}>
          {speed?.toFixed(1)} {speedUnit || 'RPM'}
        </span>

        <span style={{ color: COLORS.textSecondary }}>Current</span>
        <span className="text-right font-mono" style={{ color: COLORS.textPrimary }}
              data-tag={`${tagPrefix}_Current_PV`}>
          {current?.toFixed(1)} A
        </span>
      </div>

      {faulted && (
        <div className="mt-2 px-2 py-1 rounded text-xs text-center font-bold"
             style={{ backgroundColor: COLORS.faulted, color: COLORS.textOnAlert }}
             data-tag={`${tagPrefix}_Fault_ALM`}>
          FAULT
        </div>
      )}
    </div>
  );
}
```

---

## Valve Symbol

Visual representation of a valve with open/close state and command buttons.

```jsx
function ValveBlock({ name, tagPrefix, isOpen, transitioning, faulted }) {
  const bodyColor = faulted ? COLORS.faulted
    : transitioning ? COLORS.warning
    : isOpen ? COLORS.running
    : COLORS.stopped;

  return (
    <div className="flex flex-col items-center gap-2 p-2 rounded"
         style={{ backgroundColor: COLORS.cardBg }}>
      <span className="text-xs font-bold" style={{ color: COLORS.textPrimary }}>{name}</span>

      {/* Valve symbol — simplified bowtie */}
      <svg width="48" height="32" viewBox="0 0 48 32">
        <polygon points="4,2 24,16 4,30" fill={bodyColor} stroke="#fff" strokeWidth="1" />
        <polygon points="44,2 24,16 44,30" fill={bodyColor} stroke="#fff" strokeWidth="1" />
        <line x1="24" y1="0" x2="24" y2="32" stroke="#fff" strokeWidth="2"
              strokeDasharray={isOpen ? "0" : "4,2"} />
      </svg>

      <span className="text-xs font-mono" style={{ color: COLORS.textPrimary }}
            data-tag={`${tagPrefix}_Pos_STS`}>
        {faulted ? 'FAULT' : transitioning ? 'TRANSIT' : isOpen ? 'OPEN' : 'CLOSED'}
      </span>

      <div className="flex gap-2">
        <button className="px-2 py-1 rounded text-xs font-bold"
                style={{ backgroundColor: COLORS.running, color: COLORS.textOnAlert }}
                data-tag={`${tagPrefix}_Open_CMD`}>
          OPEN
        </button>
        <button className="px-2 py-1 rounded text-xs font-bold"
                style={{ backgroundColor: COLORS.faulted, color: COLORS.textOnAlert }}
                data-tag={`${tagPrefix}_Close_CMD`}>
          CLOSE
        </button>
      </div>
    </div>
  );
}
```

---

## Numeric Display

Shows a single analog value with label, units, and optional high/low limits.

```jsx
function NumericDisplay({ label, tagName, value, unit, hiLimit, loLimit, precision }) {
  const isHigh = hiLimit != null && value > hiLimit;
  const isLow = loLimit != null && value < loLimit;
  const valueColor = isHigh || isLow ? COLORS.warning : COLORS.textPrimary;

  return (
    <div className="flex flex-col items-center p-2 rounded min-w-[100px]"
         style={{ backgroundColor: COLORS.cardBg }}>
      <span className="text-xs" style={{ color: COLORS.textSecondary }}>{label}</span>
      <span className="text-xl font-mono font-bold" style={{ color: valueColor }}
            data-tag={tagName}>
        {value?.toFixed(precision ?? 1)}
      </span>
      <span className="text-xs" style={{ color: COLORS.textSecondary }}>{unit}</span>
    </div>
  );
}
```

---

## Setpoint Entry

Numeric display with increment/decrement buttons (preferred over text input for gloved operation).

```jsx
function SetpointEntry({ label, tagName, value, unit, step, min, max, onChange }) {
  const increment = () => {
    const newVal = Math.min((max ?? Infinity), value + step);
    onChange?.(newVal);
  };
  const decrement = () => {
    const newVal = Math.max((min ?? -Infinity), value - step);
    onChange?.(newVal);
  };

  return (
    <div className="flex flex-col items-center p-2 rounded"
         style={{ backgroundColor: COLORS.cardBg }}>
      <span className="text-xs mb-1" style={{ color: COLORS.textSecondary }}>{label}</span>
      <div className="flex items-center gap-2">
        <button className="w-10 h-10 rounded font-bold text-lg"
                style={{ backgroundColor: '#475569', color: COLORS.textPrimary }}
                onClick={decrement}>
          −
        </button>
        <span className="text-xl font-mono font-bold min-w-[80px] text-center"
              style={{ color: COLORS.textPrimary }}
              data-tag={tagName}>
          {value?.toFixed(1)}
        </span>
        <button className="w-10 h-10 rounded font-bold text-lg"
                style={{ backgroundColor: '#475569', color: COLORS.textPrimary }}
                onClick={increment}>
          +
        </button>
      </div>
      <span className="text-xs mt-1" style={{ color: COLORS.textSecondary }}>{unit}</span>
    </div>
  );
}
```

---

## Alarm List Row

Single row in an alarm history or active alarm list.

```jsx
function AlarmRow({ timestamp, severity, message, acknowledged, tagName }) {
  const severityColors = {
    critical: COLORS.alarmCritical,
    warning: COLORS.alarmWarning,
    info: COLORS.alarmInfo,
  };

  return (
    <div className={`flex items-center gap-3 px-3 py-2 border-b border-gray-700
                      ${!acknowledged ? 'animate-pulse' : ''}`}
         style={{ backgroundColor: COLORS.cardBg }}>
      <div className="w-3 h-3 rounded-full flex-shrink-0"
           style={{ backgroundColor: severityColors[severity] || COLORS.alarmInfo }} />
      <span className="text-xs font-mono flex-shrink-0" style={{ color: COLORS.textSecondary }}>
        {timestamp}
      </span>
      <span className="text-sm flex-grow" style={{ color: COLORS.textPrimary }}
            data-tag={tagName}>
        {message}
      </span>
      {!acknowledged && (
        <span className="text-xs px-2 py-0.5 rounded"
              style={{ backgroundColor: severityColors[severity], color: COLORS.textOnAlert }}>
          NEW
        </span>
      )}
    </div>
  );
}
```

---

## Navigation Footer

Bottom navigation bar with screen tabs.

```jsx
function NavFooter({ screens, activeScreen, onNavigate }) {
  return (
    <div className="flex items-center justify-around px-2 py-1"
         style={{ backgroundColor: COLORS.headerBg }}>
      {screens.map(screen => (
        <button
          key={screen.id}
          className="px-4 py-2 rounded text-sm font-semibold"
          style={{
            backgroundColor: activeScreen === screen.id ? '#475569' : 'transparent',
            color: activeScreen === screen.id ? COLORS.textPrimary : COLORS.textSecondary,
          }}
          onClick={() => onNavigate(screen.id)}
        >
          {screen.label}
        </button>
      ))}
    </div>
  );
}
```

---

## Tag Table (Collapsible)

Renders at the bottom of every mockup. Lists all tags bound to screen elements.

```jsx
function TagTable({ tags, expanded, onToggle }) {
  return (
    <div style={{ backgroundColor: COLORS.headerBg }}>
      <button
        className="w-full px-4 py-2 text-left text-sm font-bold flex justify-between"
        style={{ color: COLORS.textPrimary }}
        onClick={onToggle}
      >
        <span>Tag Binding Table ({tags.length} tags)</span>
        <span>{expanded ? '▼' : '►'}</span>
      </button>

      {expanded && (
        <table className="w-full text-xs" style={{ color: COLORS.textPrimary }}>
          <thead>
            <tr className="border-b border-gray-600">
              <th className="px-3 py-1 text-left">Tag Name</th>
              <th className="px-3 py-1 text-left">Data Type</th>
              <th className="px-3 py-1 text-left">Direction</th>
              <th className="px-3 py-1 text-left">Description</th>
              <th className="px-3 py-1 text-left">Element</th>
            </tr>
          </thead>
          <tbody>
            {tags.map((tag, i) => (
              <tr key={i} className="border-b border-gray-700">
                <td className="px-3 py-1 font-mono">{tag.name}</td>
                <td className="px-3 py-1">{tag.dataType}</td>
                <td className="px-3 py-1">{tag.direction}</td>
                <td className="px-3 py-1">{tag.description}</td>
                <td className="px-3 py-1">{tag.element}</td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </div>
  );
}
```

---

## Conveyor Segment (Animated)

Simple animated conveyor belt showing direction and speed.

```jsx
function ConveyorSegment({ name, tagPrefix, running, direction, speed }) {
  return (
    <div className="flex flex-col items-center gap-1 p-2">
      <span className="text-xs" style={{ color: COLORS.textSecondary }}>{name}</span>
      <div className="relative w-32 h-6 rounded overflow-hidden"
           style={{ backgroundColor: '#475569', border: `2px solid ${running ? COLORS.running : COLORS.stopped}` }}>
        {running && (
          <div className="absolute inset-0 flex items-center justify-center">
            <span className="text-xs font-mono animate-pulse"
                  style={{ color: COLORS.textPrimary }}
                  data-tag={`${tagPrefix}_Speed_PV`}>
              {direction === 'forward' ? '→→→' : '←←←'} {speed?.toFixed(0)} FPM
            </span>
          </div>
        )}
      </div>
    </div>
  );
}
```

---

## Mode Selector

Manual / Auto / Semi-Auto mode selector with visual indication of current mode.

```jsx
function ModeSelector({ tagName, currentMode, onModeChange }) {
  const modes = ['MANUAL', 'SEMI-AUTO', 'AUTO'];

  return (
    <div className="flex flex-col items-center gap-1 p-2 rounded"
         style={{ backgroundColor: COLORS.cardBg }}>
      <span className="text-xs" style={{ color: COLORS.textSecondary }}>MODE</span>
      <div className="flex gap-1" data-tag={tagName}>
        {modes.map(mode => (
          <button
            key={mode}
            className="px-3 py-2 rounded text-xs font-bold"
            style={{
              backgroundColor: currentMode === mode ? '#3b82f6' : '#475569',
              color: COLORS.textPrimary,
            }}
            onClick={() => onModeChange?.(mode)}
          >
            {mode}
          </button>
        ))}
      </div>
    </div>
  );
}
```

---

## Usage pattern

When building a complete screen, compose these components:

```jsx
export default function MachineMainScreen() {
  // State hooks for simulated data...

  return (
    <div className="flex flex-col h-screen" style={{ backgroundColor: COLORS.panelBg }}>
      <HeaderBar machineName="Packaging Line 01" machineState={state} alarmCount={alarmCount} />
      <AlarmBanner alarms={alarms} />

      <div className="flex-grow p-4 overflow-hidden">
        {/* Main process graphic area — compose MotorBlock, ValveBlock, etc. */}
      </div>

      <NavFooter screens={screens} activeScreen="main" onNavigate={setScreen} />
      <TagTable tags={allTags} expanded={showTags} onToggle={() => setShowTags(!showTags)} />
    </div>
  );
}
```
