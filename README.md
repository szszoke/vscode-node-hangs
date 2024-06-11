### Node seems to hang when it is invoked from a `JavaScript Debug Terminal` inside a Devcontainer.

The issue started when I upgraded VS Code to `1.90.0`. Below is the full version information:

```
Version: 1.90.0
Commit: 89de5a8d4d6205e5b11647eb6a74844ca23d2573
Date: 2024-06-04T19:43:07.605Z
Electron: 29.4.0
ElectronBuildId: 9593362
Chromium: 122.0.6261.156
Node.js: 20.9.0
V8: 12.2.281.27-electron.0
OS: Linux x64 6.9.3-arch1-1
```

#### Steps to reproduce:

1. Clone this repo, open it in VS Code and reopen it in the Devcontainer
2. When it is up and running, open a new `JavaScript Debug Terminal` and run `node -i`

The current behavior is that the process hangs and nothing seems to be happening.

As a sanity check, run `node -i` in a regular console. You should see this output:

```bash
$ node -i
Welcome to Node.js v20.2.0.
Type ".help" for more information.
>
```

#### Additional context

There are two relevant env variables set in the debugger console: `NODE_OPTIONS` and `VSCODE_INSPECTOR_OPTIONS`.

The values are below (they might be different on your machine):

```bash
$ echo $NODE_OPTIONS
 --require /vscode/vscode-server/bin/linux-x64/89de5a8d4d6205e5b11647eb6a74844ca23d2573/extensions/ms-vscode.js-debug/src/bootloader.js  --inspect-publish-uid=http
```

```bash
$ echo $VSCODE_INSPECTOR_OPTIONS
:::{"inspectorIpc":"/tmp/node-cdp.399-6edca8ba-0.sock","deferredMode":false,"waitForDebugger":"","execPath":"/usr/local/bin/node","onlyEntrypoint":false,"autoAttachMode":"always","mandatePortTracking":true,"fileCallback":"/tmp/node-debug-callback-e419525d30a18a1d"}
```

Running `node -i` in a regular terminal but setting `NODE_OPTIONS` doesn't seem to trigger the issue:

```bash
$ export NODE_OPTIONS=" --require /vscode/vscode-server/bin/linux-x64/89de5a8d4d6205e5b11647eb6a74844ca23d2573/extensions/ms-vscode.js-debug/src/bootloader.js  --inspect-publish-uid=http"
$ node -i
Welcome to Node.js v20.2.0.
Type ".help" for more information.
>
```

Setting both environment variables doesn't seem to trigger the issue either:

```bash
$ export NODE_OPTIONS=" --require /vscode/vscode-server/bin/linux-x64/89de5a8d4d6205e5b11647eb6a74844ca23d2573/extensions/ms-vscode.js-debug/src/bootloader.js  --inspect-publish-uid=http"
$ export VSCODE_INSPECTOR_OPTIONS=":::{"inspectorIpc":"/tmp/node-cdp.399-6edca8ba-0.sock","deferredMode":false,"waitForDebugger":"","execPath":"/usr/local/bin/node","onlyEntrypoint":false,"autoAttachMode":"always","mandatePortTracking":true,"fileCallback":"/tmp/node-debug-callback-e419525d30a18a1d"}"
$ node -i
Welcome to Node.js v20.2.0.
Type ".help" for more information.
>
```

Running `VSCODE_INSPECTOR_OPTIONS="" node -i` in a `JavaScript Debug Terminal` seems to work but of course, no debugging session is picked up by VS Code:

```bash
$ VSCODE_INSPECTOR_OPTIONS="" node -i
Welcome to Node.js v20.2.0.
Type ".help" for more information.
>
```

The issue doesn't seem to happen on my host machine.