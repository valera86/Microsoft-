This page serves as a list of some of the more commonly encountered issues while using the Terminal.

In addition to this FAQ, please make sure to refer to the [official docs](https://docs.microsoft.com/en-us/windows/terminal). There you can find more detailed info on features of the Terminal, the available settings and how they work, and various tips and tricks for using the Terminal.

<hr>

<!-- Protip: https://ecotrust-canada.github.io/markdown-toc/ to generate this -->

#### Table of Contents
- [General](#general)
  * [Where can I find the settings file?](#where-can-i-find-the-settings-file-)
  * [The Terminal window disappears immediately on launch!](#the-terminal-window-disappears-immediately-on-launch-)
  * [Can I use the Terminal on (Windows 7, Windows Server 2019, any other down-level Windows version)](#can-i-use-the-terminal-on--windows-7--windows-server-2019--any-other-down-level-windows-version-)
  * [What about porting the Windows Terminal to MacOS or Linux?](#What-about-porting-the-Windows-Terminal-to-MacOS-or-Linux)
  * [`wt.exe` stopped working](#-wtexe--stopped-working)
  * [Windows Terminal fails to run as Administrator](#windows-terminal-fails-to-run-as-administrator)
- [Transparency](#transparency)
  * [Why does acrylic not work?](#why-does-acrylic-not-work-)
  * [Can I have non-blurred transparency?](#can-i-have-non-blurred-transparency-)
- [Quake Mode & Global Summon](#quake-mode---global-summon)
  * [What is "Global Summon"](#what-is--global-summon-)
  * [What is "Quake Mode"?](#what-is--quake-mode--)
  * [I can't use the `quakeMode`/`globalSummon` keybinding unless I've already launched the Terminal](#i-can-t-use-the--quakemode---globalsummon--keybinding-unless-i-ve-already-launched-the-terminal)
  * [How do I set the size of the `quakeMode` window?](#how-do-i-set-the-size-of-the--quakemode--window-)
  * [The `quakeMode` window doesn't have any tabs / How can I have tabs in the quake window](#the--quakemode--window-doesn-t-have-any-tabs---how-can-i-have-tabs-in-the-quake-window)
  * [I want to use a different hotkey to summon different profiles](#i-want-to-use-a-different-hotkey-to-summon-different-profiles)
  * [I want the Terminal to hide on minimize / minimize to the tray](#i-want-the-terminal-to-hide-on-minimize---minimize-to-the-tray)
- [Other](#other)
  * [Why do we avoid changing CMD.exe?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#cmd)
  * [Why is typing-to-screen performance better than every other app?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#screenPerf)
  * [How are the Windows graphics/messaging stack assembled?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#gfxMsgStack)
  * [Output Processing between "Far East" and "Western"](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#fesb)
  * [Why do we not backport things?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#backport)
  * [Why can't we have mixed elevated and non-elevated tabs in the Terminal?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#elevation)
  * [What's the difference between a shell and a terminal?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#shell-vs-terminal)
<hr>

### General

#### Where can I find the settings file?

The settings file can be found in the following location:
* Windows Terminal (Stable): `%localappdata%\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json`
* Windows Terminal (Preview): `%localappdata%\Packages\Microsoft.WindowsTerminalPreview_8wekyb3d8bbwe\LocalState\settings.json`

Additionally in that directory is the `state.json` file, which contains additional state that the Terminal generates at runtime.

#### The Terminal window disappears immediately on launch!

Before filing a bug, please check your settings file to see if you have `"closeOnExit": "always"` set. It's possible that the Terminal window is closing when the shell application closed immediately, or it's possible that the commandline failed to launch entirely. `"closeOnExit": "graceful"` will help debug if that's the case.

#### Can I use the Terminal on (Windows 7, Windows Server 2019, any other down-level Windows version)

Unfortunately no, and we have no plans to make the Terminal available on operating systems below version 1903. There are some important operating system features we depend on. Namely:
* XAML Islands is the technology we use to host our XAML UI in a Win32 process. Without that, we'd be unable to display anything. Since XAML Islands is only complete as of 1903, there's nothing we can do about it.
* 1903 Also added support for side-by-side WinRT component activation, something deep in the COM stack that lets us find our DLLs when they're right next to our EXE.

These are unfortunately features that aren't going to be back-ported to earlier versions of Windows, so we won't be able to bring the Terminal to those versions either.

#### What about porting the Windows Terminal to MacOS or Linux?
<!-- #2331 #504 #563 #624 #2466 #699 #498 #6074 #4235 -->
The answer is actually basically the same as the above. Windows Terminal requires a good number of Windows-specific technologies. We unfortunately won't be supporting it on Mac or anywhere else any time soon. There are some really good terminals on OS X, including iTerm and Hyper, and an uncountable number of good terminals on Linux.

#### `wt.exe` stopped working

This is something that can happen intermittently whenever the Terminal is updated. Something in the upgrade process causes the execution alias to stop working correctly. You might get an error message like:

> Windows cannot find 'C:\Users\username\AppData\Local\Microsoft\WindowsApps\wt.exe'. Make sure you've typed the name correctly, then try again.

Most of the time, you can resolve this by toggling the App Execution Alias for WT off then on the screen at https://stackoverflow.com/a/66539884/1743

#### Windows Terminal fails to run as Administrator

This can happen often if you're running the Terminal from a user profile that is _not_ an administrator, **AND** the Terminal isn't installed for your Admin account on this machine. You might see an error message like:

![image](https://user-images.githubusercontent.com/31540828/121568349-86d38600-ca17-11eb-8735-c9c02912f603.png)

This happens because packaged applications need to be installed for the Admin account to be able to be run elevated. **To fix this**: install the Terminal for your Admin account as well. There are more details in [#7806]. We're working with the platform team to try and fix this issue, and get the fix brought to downlevel Windows versions. We'll update this if this issue is resolved. 

#### How do changes to the console in this repo get to `conhost.exe` in the OS?

Every couple of weeks (time permitting), one of our team members merges the changes from this repository's `main` branch into an internal mirror's `inbox` branch. Once that happens, another tool called `git2git` migrates the tree from that internal mirror into a directory on our team's branch in the Windows OS git repository. Some weeks later, that branch's content has made its way to `main`, which is approximately where Insider builds come from. The time it takes to get from our team's branch to the Windows `main` branch is dependent on many factors, so it can range from 2-6 weeks. 

#### Unable to set Windows Terminal as the Default Terminal on Windows 11

Currently, this is by design. You can set the Preview version of the Windows Terminal as the Default Terminal, but not the Stable version. We're currently working through a few more bugs that we'd like to get sorted before allowing the Stable version to be set as the Default Terminal on Windows.

### Transparency

#### Why does acrylic not work?

As a system-wide policy, acrylic is only enabled for the foreground window. So if you activate any other window than the Terminal, the Terminal's acrylic will turn off.

There are other system policies that control when acrylic is or isn't enabled. For example, if your laptop is in power saver mode, or you're accessing your machine through RDP, then acrylic will be disabled. Before filing a bug, make sure that acrylic works in other apps, like Calculator, or the Start Menu.

We're currently using [#7158] to track adding support for "enable acrylic even for inactive Terminal windows" - if you're passionate about this, we'd love your contribution!

#### Can I have non-blurred transparency?

At the moment, no. The thread with the most up-to-date investigation tracking this is [#603]. In [#603 (comment)](https://github.com/microsoft/terminal/issues/603#issuecomment-552470153), we experimented with whole-window unblurred transparency, but as you can see, the prototype wasn't particularly polished. 

At current, the Terminal team is waiting until after Terminal 2.0 to migrate to WinUI 3. We're working with the WinUI team to get this added in to WinUI 3.0. Upstream, this is being tracked in [microsoft/microsoft-ui-xaml#1247]. This is the path we'll be pursuing to get this feature added to the Terminal, because driving this solution also means driving an important dev platform feature for the entire platform, one that'll help improve other applications on Windows as well. This solution will allow us to make individual elements of the Terminal window transparent, rather than the entire window.

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

### Other

This is a small list of FAQ-like questions that have had lengthy replies in the past. They're all preserved in the doc [`niksa.md`](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md)

- [Why do we avoid changing CMD.exe?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#cmd)
- [Why is typing-to-screen performance better than every other app?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#screenPerf)
- [How are the Windows graphics/messaging stack assembled?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#gfxMsgStack)
- [Output Processing between "Far East" and "Western"](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#fesb)
- [Why do we not backport things?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#backport)
- [Why can't we have mixed elevated and non-elevated tabs in the Terminal?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#elevation)
- [What's the difference between a shell and a terminal?](https://github.com/microsoft/terminal/blob/main/doc/Niksa.md#shell-vs-terminal)

[#603]: https://github.com/microsoft/terminal/issues/603
[#5727]: https://github.com/microsoft/terminal/issues/5727
[#7158]: https://github.com/microsoft/terminal/issues/7158
[#7806]: https://github.com/microsoft/terminal/issues/7806
[#8888]: https://github.com/microsoft/terminal/issues/8888
[#9992]: https://github.com/microsoft/terminal/issues/9992
[#9996]: https://github.com/microsoft/terminal/issues/9996
[microsoft/microsoft-ui-xaml#1247]: https://github.com/microsoft/microsoft-ui-xaml/1247