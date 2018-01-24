# writing an electron app when you don't know javascript

##### started writing this document January 2018

## background

I've been a C# dev for a few years now, mostly doing Xamarin.Forms stuff with Azure or private backends.
Did some Android stuff a few years back, before that C++ (where my highlight was translating Fortran 77 to C++), and the decade before that was mostly web admin stuff, so mostly content management.
So while I've seen javascript I've never had to get my hands dirty much.

## the project

Rewrite an old app around Electron.
Easy...
This started in the third quarter of 2017, so these first steps are from my sketchy memory.

Started out trying Typescript; thought I might as well learn that if I could; but the transpiler(?) thing I couldn't quite get configured right after I added in the nfc and serial libraries (@pokusew/pcsclite and node-serialport respectively), so ended up dropping back to Javascript, commonjs even to get something about the versions of electron/node and platform libraries to play ball. I really wanted to be a bit more bleeding edge using Typescript transpiled down to a newer 'standard' of Javascript but alas, my knowledge was/is not there yet.

### the delay

Fourth quarter of 2017, get pulled off to work on another Xamarin.Forms project...

### starting again...kindof

So 2018 is well under way and I've just got back to this Electron app. First steps, get Continuous Integration (CI) build server stuff working in VSTS.
Easy...
First build command, npm install.

Fail on pcsclite building, can't find python...

Long story short, it doesn't acknowledge python 3, and after installing python 2 it then wants .net framework 2.0 sdk for vcbuild.exe.
Went with the easy fix (as I couldn't work out if, or which option, in Visual Studio 2017 would give me vcbuild.exe) of running "npm install -g windows-build-tools" on the build server.
Which pulls down an old python version (2.7.13, currently 2.7.14 is the latest download), and some windows build stuff. Unhelpfully it doesn't add python to the path, or create a PYTHON variable, so you need to do that yourself, after working out where they were downloaded to; hint, somewhere under the users folder.

Try build again, success.

Next step packaging

### package the project

To start with I had manually been running "./node_modules/.bin/electron-rebuild.cmd" (or its mac counterpart) to build the platform specific bits, but after trying a few package scenarios I've settled on electron-builder, which said electron-rebuild wasn't needed or shouldn't be in use too. So I removed that, and now "npm install" does the platform build steps too (who knows if I changed something or a version update has meant this now happens without me manually needing to build the platform parts too).

Anyway, so I'm using electron-builder, with https://medium.com/how-to-electron/a-complete-guide-to-packaging-your-electron-app-1bdc717d739f as my starting point.

Created an icon for windows, and ran "npm dist" and have things working now. Much more civilised way of doing things than manually pulling the current build of electron, and manually renaming electron.exe and giving it a new icon, plus it creates an asar file for you too without any config needed. I'm trying the NSIS route at present, we'll see if I need to switch to squirrel for incremental updates (not sure, but think incremental updates aren't an option with NSIS).

### electron updater

My current challenge is getting auto-update working.... using https://github.com/iffy/electron-updater-example as a ref at present....

###### © @redtahr 2017