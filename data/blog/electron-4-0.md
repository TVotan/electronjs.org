---
title: Electron 4.0.0
author: BinaryMuse
date: '2018-12-18'
---

The Electron team is excited to announce that the stable release of Electron 4 is now available! You can install it from [electronjs.org](https://electronjs.org/) or from npm via `npm install electron@latest`. The release is packed with upgrades, fixes, and new features, and we can't wait to see what you build with them. Read more for details about this release, and please share any feedback you have as you explore!

---

## What's New?

A large part of Electron's functionality is provided by Chromium, Node.js, and V8, the core components that make up Electron. As such, a key goal for the Electron team is to keep up with changes to these projects as much as possible, providing developers who build Electron apps access to new web and JavaScript features. To this end, Electron 4 features major version bumps to each of these components; Electron v4.0.0 includes Chromium `69.0.3497.106`, Node `10.11.0`, and V8 `6.9.427.24`.

In addition, Electron 4 includes changes to Electron-specific APIs. You can find a summary of the major changes in Electron 4 below; for the full list of changes, check out the [Electron v4.0.0 release notes](TODO_RELEASE_NOTES).

### Breaking Changes

#### Single Instance Locking

Previously, to make your app a Single Instance Application (ensuring that only one instance of your app is running at any given time), you could use the `app.makeSingleInstance()` method. Starting in Electron 4.0, you must use `app.requestSingleInstanceLock()` instead. The return value of this method indicates whether or not this instance of your application successfully obtained the lock. If it failed to obtain the lock, you can assume that another instance of your application is already running with the lock and exit immediately.

For an example of using `requestSingleInstanceLock()` and information on nuanced behavior on various platforms, [see the documentation for `app.requestSingleInstanceLock()` and related methods](https://electronjs.org/docs/api/app#apprequestsingleinstancelock) and [the `second-instance` event](https://electronjs.org/docs/api/app#event-second-instance).

#### `win_delay_load_hook`

When building native modules for windows, the `win_delay_load_hook` variable in the module's `binding.gyp` must be true (which is the default). If this hook is not present, then the native module will fail to load on Windows, with an error message like `Cannot find module`. [See the native module guide](https://electronjs.org/docs/tutorial/using-native-node-modules#a-note-about-win_delay_load_hook) for more information.

#### Accessing Certain Modules from Sandboxed Renderer Processes

When using a sandboxed renderer process, the following modules are implemented using `remote.require`:

* `require('electron').screen`
* `require('child_process')`
* `require('fs')`
* `require('os')`
* `require('path')`

As a result, to use these modules in a sandboxed renderer process, you should explicitly require them via the `remote` module:

```javascript
// old
require('electron').screen
require('child_process')
require('fs')
require('os')
require('path')

// new
require('electron').remote.screen
require('electron').remote.require('child_process')
require('electron').remote.require('fs')
require('electron').remote.require('os')
require('electron').remote.require('path')
```

Another approach is to only use them in the main process and broker all requests from the renderer process [via IPC](https://electronjs.org/docs/api/ipc-main).

If you do use the `remote` approach, be sure to be aware of the performance and other implications from using the `remote` module. See [the documentation for the `remote` module](https://electronjs.org/docs/api/remote) for more information.

### Deprecations

The following breaking changes are planned for Electron 5.0, and thus are deprecated in Electron 4.0.

#### Node.js Integration Disabled for `nativeWindowOpen`-ed Windows

Starting in Electron 5.0, child windows opened with the `nativeWindowOpen` option will always have Node.js integration disabled.

#### `webPreferences` Default Values

When creating a new `BrowerWindow` with the `webPreferences` option set, the following `webPreferences` option defaults are deprecated in favor of new defaults listed below:

<div class="table table-ruled table-full-width">

| Property | Deprecated Default | New Default |
|----------|--------------------|-------------|
| `contextIsolation` | `false` | `true` |
| `nodeIntegration` | `true` | `false` |
| `webviewTag` | value of `nodeIntegration` if set, otherwise `true` | `false` |

</div>

Please note: there is currently [a known bug (#9736)](https://github.com/electron/electron/issues/9736) that prevents the `webview` tag from working if `contextIsolation` is on. Keep an eye on the GitHub issue for up-to-date information!

Learn more about context isolation, Node integration, and the `webview` tag in [the Electron security document](https://electronjs.org/docs/tutorial/security).

Electron 4.0 will still use the current defaults, but if you don't pass an explicit value for them, you'll see a deprecation warning. To prepare your app for Electron 5.0, use explicit values for these options. [See the `BrowserWindow` docs](https://electronjs.org/docs/api/browser-window#new-browserwindowoptions) for details on each of these options.

## App Feedback Program

The [App Feedback Program](https://electronjs.org/blog/app-feedback-program) we instituted during the development of Electron 3.0 was successful, so we've continued it during the development of 4.0 as well. We'd like to extend a massive thank you to Atlassian, Discord, MS Teams, OpenFin, Slack, Symphony, WhatsApp, and the other program members for their involvement during the 4.0 beta cycle. To learn more about the App Feedback Program and to participate in future betas, [check out our blog post about the program](https://electronjs.org/blog/app-feedback-program).

## What's Next

In the short term, you can expect the team to continue to focus on keeping up with the development of the major components that make up Electron, including Chromium, Node, and V8. Although we are careful not to make promises about release dates, our plan is release new major versions of Electron with new versions of those components approximately quarterly. [See our versioning document](https://electronjs.org/docs/tutorial/electron-versioning) for more detailed information about versioning in Electron.

For information on planned breaking changes in upcoming versions of Electron, [see our Planned Breaking Changes doc](https://github.com/electron/electron/blob/master/docs/api/breaking-changes.md).