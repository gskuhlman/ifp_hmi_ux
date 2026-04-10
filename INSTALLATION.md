# Installation

## Option 1: Install from the .skill file

If someone shared the `industrial-hmi-ux.skill` file with you:

1. Open the Claude desktop app
2. Open the `.skill` file — Claude will show a "Save skill" button
3. Click "Save skill" to install

The skill is now active. Start a new conversation and ask about HMI design.

## Option 2: Install from this repository

Clone or download this repo, then copy the skill files into your Claude skills directory.

### Windows

```
xcopy /E /I "industrial-hmi-ux-skill\SKILL.md" "%USERPROFILE%\Documents\Claude\skills\industrial-hmi-ux\"
xcopy /E /I "industrial-hmi-ux-skill\references" "%USERPROFILE%\Documents\Claude\skills\industrial-hmi-ux\references\"
```

Or manually copy the `SKILL.md` file and the `references/` folder into:
```
C:\Users\<YourName>\Documents\Claude\skills\industrial-hmi-ux\
```

### macOS

```bash
mkdir -p ~/Documents/Claude/skills/industrial-hmi-ux
cp SKILL.md ~/Documents/Claude/skills/industrial-hmi-ux/
cp -r references/ ~/Documents/Claude/skills/industrial-hmi-ux/references/
```

### Linux

```bash
mkdir -p ~/.config/claude/skills/industrial-hmi-ux
cp SKILL.md ~/.config/claude/skills/industrial-hmi-ux/
cp -r references/ ~/.config/claude/skills/industrial-hmi-ux/references/
```

## Option 3: Repackage as a .skill file after editing

If you've made edits and want to distribute an updated `.skill` file:

1. Zip the skill folder (containing `SKILL.md` and `references/`):
   ```bash
   cd industrial-hmi-ux-skill
   zip -r ../industrial-hmi-ux.skill SKILL.md references/
   ```

   On Windows (PowerShell):
   ```powershell
   Compress-Archive -Path SKILL.md, references -DestinationPath ..\industrial-hmi-ux.skill
   ```

2. Share the resulting `industrial-hmi-ux.skill` file — recipients install it using Option 1 above.

## Verify installation

Start a new conversation with Claude and try:

> "Design an HMI for a small conveyor system with 3 zones. We're using a Weintek cMT2078X and a Codesys PLC."

Claude should:
- Recognize the Weintek panel and use 800×480 resolution
- Ask about operator role, communication protocol, and safety scope
- Produce a screen layout mockup
- Generate a tag table with Codesys-formatted exports

If Claude doesn't pick up the skill, check that `SKILL.md` is in the correct directory and restart the Claude app.

## Uninstall

Delete the skill folder from your Claude skills directory:

- **Windows**: Delete `Documents\Claude\skills\industrial-hmi-ux\`
- **macOS**: Delete `~/Documents/Claude/skills/industrial-hmi-ux/`
- **Linux**: Delete `~/.config/claude/skills/industrial-hmi-ux/`

## Updating

To update to a newer version, either:
- Replace the files in your skills directory with the updated versions from the repo
- Or install a new `.skill` file, which overwrites the existing installation

Your local edits (custom panels, components, etc.) will be overwritten by updates. If you've made local changes, back them up first or merge manually.
