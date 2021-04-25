# overload-debug
This repository contains the mono.dll for debugging Overload/olmod with dnSpy. See below for context.

## Overview
This guide explains how you can run Overload and olmod game code in debug mode by using **dnSpy on Windows**. There may be other ways to achieve the same result with other tools and/or on other operating systems, but this is not covered here. 

## TL;DR
- Backup mono.dll under Overload\Overload_Data\Mono\EmbedRuntime. Download the debug version of mono.dll [Win 32](https://github.com/marlowe-fw/overload-debug/tree/main/win32) / [Win 64](https://github.com/marlowe-fw/overload-debug/tree/main/win64) and copy it to Overload\Overload_Data\Mono\EmbedRuntime.
- Get [dnSpy](https://github.com/dnSpy/dnSpy/releases) and run it
- Add the executable and assemblies to the dnSpy Assemblies Explorer (left pane) in dnSpy. 
  - For debugging olmod, this would be your debug build of olmod.exe and GameMod.dll alongside Overload\Overload_Data\Managed\Assembly-CSharp.dll. 
  - If you are after the unmodified code, add instead Overload.exe and Assembly-CSharp.dll. 
- Debug in dnSpy: Select olmod.exe or Overload.exe in the Assembly Explorer and hit Start. Make sure to select the Unity debugger type from the "Debug Program" dialog.

### Why debug?
Debugging can help speed up the development cycle. Without the option for debugging, you may have to rely on issuing debug statements to log files. With code you can debug, you can set breakpoints, inspect the game state, and step through specific parts of the code. 

## 1. Replace mono.dll with a debug-build version
Overload is based on the Unity game engine. Unity in turn uses [Mono](https://www.mono-project.com/) a cross-platform, open source .NET framework. Since Mono is open source, debug builds of the framework exist. We can take advantage of this by replacing the release mode mono.dll file, i.e. the one which shipped with Overload, with a version that contains debug symbols.

The production version of Overload was compiled with Unity version 2017.4.10. As a consequence, you need the debug-mode mono.dll which was referenced by that particular Unity build. Here is how you can obtain the debug build of mono.dll:

- Download: [Win 32](https://github.com/marlowe-fw/overload-debug/tree/main/win32) / [Win 64](https://github.com/marlowe-fw/overload-debug/tree/main/win64)
- or [build it yourself](https://github.com/dnSpy/dnSpy-Unity-mono).

For more information, please refer to the [dnSpy Wiki](https://github.com/dnSpy/dnSpy/wiki/Debugging-Unity-Games#debugging-release-builds)

Once you have the file open the Overload\Overload_Data\Mono\EmbedRuntime folder. Back up mono.dll, then copy the downloaded mono.dll into the folder.

## 2. Get dnSpy
Download dnSpy from the [official release page on github](https://github.com/dnSpy/dnSpy/releases). Launch it.

## 3. Debug until you overload

### Debug the unmodified game code
This can be useful for understanding the original source code undisturbed by any modding logic kicking in.

- Add Overload.exe and Overload\Overload_Data\ManagedAssembly-CSharp.dll to the Assembly Explorer in dnSpy
- Set breakpoints as needed
- Click on/select Overload.exe in the Assmbly Explorer. 
- Select "Debug" - "Start Debugging (F5)" from the menu, or just hit "Start"
- Select Unity as the debug engine from the "Debug Program" dialog
- Hit OK

### Debug the modded game code
This will be your typical setup to debug olmod.

- Add olmod.exe produced, by your debug build, and Overload\Overload_Data\ManagedAssembly-CSharp.dll to the Assembly Explorer in dnSpy
- Add GameMod.dll to the Assembly Explorer in dnSpy
    - Make sure this is the GameMod.dll (debug build) which is referenced by the olmod.exe you added in the previous step
- Set breakpoints as needed
- Click on/select olmod.exe in the Assmbly Explorer. 
- Select "Debug" - "Start Debugging (F5)" from the menu, or just hit "Start"
- Select Unity as the debug engine from the "Debug Program" dialog
- Hit OK

### Debug server vs client - standalone or at the same time
- If you want to debug the game itself, you can start debugging as described, no paramters required.
- If instead you want to debug the Overload server, you add `-nographics -batchmode` into the parameters field after bringing up the debug dialog within dnSpy.
- You can run multiple instances of dnSpy. This means you can have one instance debugging the client and one instance debugging the server.
    - To connect to the local server, when in the game instance, select Multiplayer - LAN Game - Open Game

## Questions and Troubleshooting

**I have set a breakpoint but it is never hit**

First, make sure you select Unity as the debugger type when you start debugging. 
To debug any code changes you make within olmod, you must add the olmod.exe and GameMod.dll produced by your debug builds. 
Hover over the breakpoint in dnSpy after you start debugging. If it displays in solid red and there is no warning, the debug symbols should have been loaded and your breakpoint may simply not be hit 
because it is not triggered by the execution path. Try setting it another place where you can be sure it must be hit. This will allow you to verify that the overall setup is correct.
