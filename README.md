# node-winreg-utf8 #

node module that provides access to the Windows Registry through the REG commandline tool.

This repo is originally forked from [node-winreg](https://github.com/fresc81/node-winreg). Additional UTF-8 support is added now.


## Installation ##

The following command installs node-winreg.

```shell
npm install winreg-utf8 
```

## Example Usage ##

Let's start with an example. The code below lists the autostart programs of the current user.

```javascript
var Registry = require('winreg')
,   regKey = new Registry({                                       // new operator is optional
      hive: Registry.HKCU,                                        // open registry hive HKEY_CURRENT_USER
      key:  '\\Software\\Microsoft\\Windows\\CurrentVersion\\Run' // key containing autostart programs
    })

// list autostart programs
regKey.values(function (err, items /* array of RegistryItem */) {
  if (err)
    console.log('ERROR: '+err);
  else
    for (var i=0; i<items.length; i++)
      console.log('ITEM: '+items[i].name+'\t'+items[i].type+'\t'+items[i].value);
});
```

## Troubleshooting ##


### Access to restricted keys ###

Since Windows Vista access to certain Registry Hives (HKEY_LOCAL_MACHINE or short HKLM for example) is restricted to processes that run in a security elevated context even if the user that starts the process is an admin. You can start a console within that context by right clicking the console shortcut and selecting the item with the shield icon called "Run as administrator" from the context menu.

Under some rare circumstances access to Registry Hives or particular keys may also be blocked by some antivirus programs or the Windows Group Policy Editor (google for gpedit.msc).

You can also use the regedit.exe tool shipped with Windows to check if you actually have access.


### Processing UTF-8 data ###

The Microsoft Windows console isn't capable of handling UTF-8 encoded text unless you set it up properly. If you see weird question marks for certain characters, it's probhably a problem with the encoding.

By default the console is setup to use an encoding that suits the language of the Windows operating system installation. Windows uses codepages to specify encodings for the console. The codepage is a unique number which is assigned to each encoding.

If you want to query the currently selected codepage you can type the command <code>chcp</code> (w/o parameters). To set a new codepage (UTF-8 for this example) you pass the codepage number as the only argument to <code>chcp</code>. The codepage value for UTF-8 is 65001.

You can easily do this from within your nodejs script by using the <code>child_process.execSync(...)</code> function like the following example shows.

```javascript
var execSync = require('child_process').execSync;
console.log(execSync('chcp').toString());
console.log(execSync('chcp 65001').toString());
```
An even better approach would be to extract and store the value returned by a call to <code>chcp</code> prior setting the console to UTF-8 and resetting the codepage after your script is done.

In case where a child process doesn't inherit the changed code page, e.g. an Electron application, you can simply create the Registry object with `utf8: true` option, then `chcp 65001` is executed each time before it operates the Registry.
```javascript
regKey = new Registry({
  hive: Registry.HKCU,                                        // open registry hive HKEY_CURRENT_USER
  key:  '\\Software\\Microsoft\\Windows\\CurrentVersion\\Run' // key containing autostart programs
  utf8: true                                                  // decode value using utf-8
});
// ...
```

## License ##

This project is released under [BSD 2-Clause License](http://opensource.org/licenses/BSD-2-Clause).

Copyright (c) 2016, Paul Bottin All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

 * Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
 * Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
