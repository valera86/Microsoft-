This page serves as a list of some of the more commonly encountered issues while using the Terminal.

In addition to this FAQ, please make sure to refer to the [official docs](https://docs.microsoft.com/en-us/windows/terminal). There you can find more detailed info on features of the Terminal, the available settings and how they work, and various tips and tricks for using the Terminal.

### General

#### Where can I find the settings file?

The settings file can be found in the following location:
* Windows Terminal (Stable): `%localappdata%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json`
* Windows Terminal (Preview): `%localappdata%\Packages\Microsoft.WindowsTerminalPreview_8wekyb3d8bbwe\LocalState\settings.json`

#### The Terminal window disappears immediately on launch!

Before filing a bug, please check your settings file to see if you have `"closeOnExit": "always"` set. It's possible that the Terminal window is closing when the shell application closed immediately, or it's possible that the commandline failed to launch entirely. `"closeOnExit": "graceful"` will help debug if that's the case.

#### Can I use the Terminal on (Windows 7, Windows Server 2019, any other down-level Windows version)

Unfortunately no, and we have no plans to make the Terminal available on operating systems below version 1903. There are some important operating system features we depend on. Namely:
* XAML Islands is the technology we use to host our XAML UI in a Win32 process. Without that, we'd be unable to display anything. Since XAML Islands is only complete as of 1903, there's nothing we can do about it.
* 1903 Also added support for side-by-side WinRT component activation, something deep in the COM stack that lets us find our DLLs when they're right next to our EXE.

These are unfortunately features that aren't going to be back-ported to earlier versions of Windows, so we won't be able to bring the Terminal to those versions either.

### Quake Mode & Global Summon

Please make sure to check out [#8888], which is tracking all the quake-mode and `globalSummon` related issues.

#### What is "Global Summon"

"Global Summon" refers to the `globalSummon` action. This action allows you to bind a shortcut _systemwide_ to activate the Terminal window. This means that you can bind something like <kbd>win+\`</kbd>, and press that _anywhere_ in the OS to instantly activate the Terminal window. `globalSummon` supports a ton of different parameters to control its behavior, so please make sure to [check out the docs](https://docs.microsoft.com/en-us/windows/terminal/customize-settings/actions#global-commands).

#### What is "Quake Mode"?

"Quake Mode" is a specific version of `globalSummon`. It summons a window that's named `_quake`, and the window named `_quake` has certain special properties. Check out [the docs](https://docs.microsoft.com/en-us/windows/terminal/tips-and-tricks#quake-mode) for more details.

#### I can't use the `quakeMode`/`globalSummon` keybinding unless I've already launched the Terminal

That's correct - the Terminal needs to be running to be able to register the global hotkeys. You can configure the Terminal to launch on machine startup with `"startOnUserLogin":  true`. We're also using [#9996] to track "Allow the Terminal to start up and process global hotkeys without creating a window".

#### How do I set the size of the `quakeMode` window? 

Right now, you can't. The window named `_quake` will always open on the top half of the monitor. 

What you _can_ do, though, is rebind <kbd>win+\`</kbd> to a different `globalSummon` action. The following will be equivalent to the `quakeMode` action, but without the requirement that the window's name is `_quake`:

```json
{ "keys": "win+`", "command": { "action": "globalSummon", "dropdownDuration": 200, "toggleVisibility": true, "monitor": "toCursor", "desktop": "toCurrent" } }
```

Then, your window will still obey your standard `initalPosition`, `initialRows`, etc. settings.

Additionally, [#9992] is the issue we're tracking for "Allow configuring global settings per-window name". That means you'll be able to use that to change the settings for the window named `_quake`.

#### The `quakeMode` window doesn't have any tabs / How can I have tabs in the quake window

The `_quake` window always opens in ["focus mode"](https://docs.microsoft.com/en-us/windows/terminal/customize-settings/actions#toggle-focus-mode) by default. That doesn't mean that the quake window doesn't _have_ tabs, just that they're hidden. You can use the [Command Palette](https://docs.microsoft.com/en-us/windows/terminal/command-palette) to disable focus mode if you want to see the tabs again. 

If you don't want the global hotkey to summon the window in quake mode, there are two options:
* Either re-bind it to a different `globalSummon` action, like the above, without `"name": "_quake"`.
* Wait patiently for [#9992] to allow changing the settings of the `_quake` window name.

#### I want to use a different hotkey to summon different profiles

This is another scenario that'll have to wait for [#9992]. What you'd end up with is different window names for each profile you want a specific hotkey for. The `defaultProfile` for those windows would be set to whatever profile you want. Then, you'd bind `globalSummon` actions, with the `name` set to each of those window names. 

#### I want the Terminal to hide on minimize / minimize to the tray

This is a feature that's commonly associated with Quake Mode. Unfortunately, it didn't quite make the cut for 1.9. Never fear! We're working on it currently. Please follow [#5727] for updates on adding this functionality to the Terminal.

[#5727]: https://github.com/microsoft/terminal/issues/5727
[#8888]: https://github.com/microsoft/terminal/issues/8888
[#9992]: https://github.com/microsoft/terminal/issues/9992
[#9996]: https://github.com/microsoft/terminal/issues/9996