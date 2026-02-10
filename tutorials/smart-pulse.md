# Smart Pulse Tutorial

This tutorial walks through building a tiny **Smart Pulse** plugin that toggles a repeating toast message. It is a simple way to practice plugin initialization, commands, and cleanup.

## 1. Create `plugin.json`

Add a manifest that points to your plugin entry file and readme.

```json
{
  "id": "com.example.smart-pulse",
  "name": "Smart Pulse",
  "main": "main.js",
  "version": "1.0.0",
  "readme": "readme.md",
  "icon": "icon.png",
  "minVersionCode": 292
}
```

## 2. Build `main.js`

Use `acode.setPluginInit` to register a command and `acode.setPluginUnmount` to clean up the interval when the plugin unloads.

```javascript
const toast = acode.require('toast');

let pulseTimer = null;
let pulseEnabled = false;

const startPulse = () => {
  pulseTimer = setInterval(() => {
    toast('Smart Pulse: editor is alive!', 1200);
  }, 5000);
};

const stopPulse = () => {
  clearInterval(pulseTimer);
  pulseTimer = null;
};

acode.setPluginInit('com.example.smart-pulse', () => {
  const { commands } = editorManager.editor;

  commands.addCommand({
    name: 'smart-pulse:toggle',
    bindKey: { win: 'Ctrl-Alt-P', mac: 'Command-Alt-P' },
    exec: () => {
      pulseEnabled = !pulseEnabled;

      if (pulseEnabled) {
        startPulse();
        toast('Smart Pulse enabled', 2000);
      } else {
        stopPulse();
        toast('Smart Pulse disabled', 2000);
      }
    },
  });
});

acode.setPluginUnmount('com.example.smart-pulse', () => {
  const { commands } = editorManager.editor;
  commands.removeCommand('smart-pulse:toggle');
  stopPulse();
});
```

## 3. Try it out

1. Install the plugin.
2. Trigger the command **Smart Pulse: Toggle** with `Ctrl-Alt-P` (Windows/Linux) or `Command-Alt-P` (macOS).
3. Watch for periodic toast messages.

## 4. Next ideas

- Swap the toast for a custom UI component.
- Make the interval configurable with a settings UI.
- Add a “Pulse once” command for quick feedback.
