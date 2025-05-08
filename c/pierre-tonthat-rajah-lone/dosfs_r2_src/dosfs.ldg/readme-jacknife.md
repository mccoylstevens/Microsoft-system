# README-jacknife

## Jacknife

#### (formely known as "Total Commander .ST/.MSA packer plugin v0.03")

This little plugin enables [Total Commander](https://www.ghisler.com) and compatible programs ([Double Commander](https://doublecmd.sourceforge.io), [TC4Shell](https://www.tc4shell.com)) to open and extract Atari ST .ST and .MSA disk images that have a valid file system in it.

### Feature list

#### What's already there

|                                                                                               | `.ST` | `.MSA` | `.DIM`[<sup>1</sup>](readme-jacknife.md#f1) | `.AHD`[<sup>2</sup>](readme-jacknife.md#f1) |
| --------------------------------------------------------------------------------------------- | :---: | :----: | :-----------------------------------------: | :-----------------------------------------: |
| Opening image (directory listing)                                                             |   ✓   |    ✓   |                      ✓                      |                      ✓                      |
| Extracting files[<sup>3</sup>](readme-jacknife.md#f3)                                         |   ✓   |    ✓   |                      ✓                      |                      ✓                      |
| Adding files[<sup>3</sup>](readme-jacknife.md#f3)                                             |   ✓   |    ✓   |                      ✓                      |                      ✓                      |
| Creating new folders[<sup>3</sup>](readme-jacknife.md#f3)                                     |   ✓   |    ✓   |                      ✓                      |                      ✓                      |
| Deleting files[<sup>3</sup>](readme-jacknife.md#f3)                                           |   ✓   |    ✓   |                      ✗                      |                      ✓                      |
| Deleting folders[<sup>3</sup>](readme-jacknife.md#f3)                                         |   ✓   |    ✓   |                      ✗                      |                      ✓                      |
| Deleting source files (move to archive)                                                       |   ✓   |    ✓   |                      ✗                      |                      ✓                      |
| Creating new image[<sup>3</sup>](readme-jacknife.md#f3) [<sup>4</sup>](readme-jacknife.md#f4) |   ✓   |    ✓   |                      ✗                      |                      ✗                      |
| Progress bar info during operations                                                           |   ✓   |    ✓   |                      ✓                      |                      ✓                      |

<sup>1</sup>Fastcopy & E-Copy

<sup>2</sup>Hard disk images, AHDI 3.00 compatible and ICD style primary partitions schemes only for now

<sup>3</sup> Recursively

<sup>4</sup> Resizing image until the files fit, or maximum length reached (in which case an error is generated)

#### What's missing, but planned (in order of severity/implementation likelihood)

* Writing outside the partitions (i.e. the folder where "0", "1" etc folders exist) causes a crash
* Extended (XGM) partition support
* `.ini` file with various settings (specify if creating a HD disk image is allowed, customisable hard disk image extension, etc)
* Writing .DIM images (only opening and extracting is possible at the moment)
* Support PK\_PACK\_SAVE\_PATHS
* (IN PROGRESS - check [SamariTan](readme-jacknife.md#samaritan) below) Stand-alone command line version for scripting (adding files, deleting files, creating new disk images, extract deleted files -because tIn insisted-, monitoring directory and if it changes sync the differences with the image)
* Integrating into other programs (for example PiSCSI, zeST etc)

### Installation

#### Windows

Pre-built binaries are supplied.

#### Linux/OSX

Starting with v0.05 pre-built binaries are supplied in the releases. To install, follow the instructions below for Double Commander.

There is a possibility that on OSX a message like "Cannot Be Opened Because the Developer Cannot be Verified". If that happens, the only course of action is to implicitly "trust the developer". Several guides exist on the internet about how to achieve this.

To build manually, use the `build_linux_mac.sh` script to build the plugin. Then see the Double Commander section below on how to install the plugin.

#### Total Commander

There are _two_ ways of installing this extension depending on your use case:

**"Open file directly" use case**

If you want to open .ST/.MSA disk images directly by pressing Return on a selected disk image: just open **jacknife.zip** in Total Commander and follow the install procedure. The plugin will be associated with .ST and .MSA extensions. Enter disk images like folders/ZIP files.

**"Open file with CTRL+Pagedown" use case**

Say you already have e.g. an emulator associated with the .ST/.MSA extensions and you want to start them in that emulator by simply pressing return like you're used to by now - then you'll need another way to enter the images in Total Commander.\
Just open **jacknife\_ctrlpagedownonly.zip** in Total Commander and follow the install procedure. Now the plugin is associated with some gibberish extension you'll probly never use and that keeps the extension out of your way in TC when .ST and .MSA filesare shown; but _at the same time_ you can simply open .ST/.MSA files by pressing **CTRL+Page Down** on a selected disk image. Magic!

#### Double Commander

Unlike Total Commander, Double Commander doesn't support automatic installation of plugins. Therefore a manual procedure is described below:

* Extract `jacknife.wcx` or `jacknife.wcx64` from `jacknife.zip` or `jacknife64.zip` to a location which is going to be permanent, i.e. not in your _Downloads_ folder. Preferably inside the Double Commander install folder
* Open Double Commander
* Navigate to menu _Configuration_->_Options_
* Go to subsection _Plugins_->_Plugins WCX_
* Click _Add_
* Navigate to the location where the extracted `jacknife.wcx` or `jacknife.wcx64` exists and select that
* In the pop-up dialog that asks which extensions to associate plugin, type `st msa dim img` and press Ok. Or If you wish to open images using CTRL+Pagedown (as described in the Total Commander section above) then just type any nonsense text, like `atariextension`

#### TC4Shell

Again, this program requires a manual installation:

* Extract `jacknife.wcx` or `jacknife.wcx64` from `jacknife.zip` or `jacknife64.zip` to a location which is going to be permanent, i.e. not in your _Downloads_ folder. Preferably inside "\Program Files\TC4Shell"
* After installing TC4Shell, navigate to Control Panel (for Windows 10 onwards just press the Start button and type `control` and enter). There should be a _TC4Shell plugins_ button, click that
* Right click on the empty space of the window below the installed plugins. A context menu should pop up
* Select _Install plugin_
* Select _WCX plugins_ in the file extension drop down menu
* Navigate to the location where the extracted `jacknife.wcx` or `jacknife.wcx64` exists and select that
* Click _Install_
* You can now right click on disk image files on Windows Explorer and select "Open as folder". A new window should open with the disk contents (assuming a valid image)

## SamariTan

This is a no-frills command line version of Jacknife that aims to expose the same functionality, but from the command line. Currently it is in early stages of development and works on Windows only (but this is easy to fix). There is little to no feedback, especially if things fail. It is highly encouraged to not use this tool on files that are not backed up yet. You have been warned.

The current supported set of commands and their syntax is as follows:

* `samaritan c <archive name> <list of files to add>`: This will create an image file with the name _archive name_ and will proceed in adding all files in the _list of files to add_. Note that the _archive name_ must not exist. Also, for the time being do _not_ supply absolute paths. The only properly tested mode is to supply files relative to the current path, as they will be added with all the subfolders they might be in. Also note that starting a relative path with a `..` to move up one diretcory will most likely result in an error.
* `samaritan d <archive name> <list of files to delete>`: This will attempt to delete all files in _list of files to delete_ from the archive `archive name`.

### Credits

* FAT12/16/32 reader "PetitFAT" used in older versions taken from here: http://www.elm-chan.org/fsw/ff/00index\_p.html (slightly modified)
* DOSFS library obtained from http://www.larwe.com/zws/products/dosfs/index.html (appears to be Public Domain licensed), modified and debugged (especially for FAT12)
* Original plugin code by \<a href=https://github.com/tin-nl>@tin-nl
* Extended and replaced FAT library by \<a href=https://github.com/ggnkua>@ggnkua
* Linux/Mac fix by \<a href=https://github.com/tattlemuss>@tattlemuss
* Uses _Really_ minimal PCG32 code / (c) 2014 M.E. O'Neill / \<a href=https://www.pcg-random.org>pcg-random.org. Licensed under Apache License 2.0 (NO WARRANTY, etc. see website)
* Some code borrowed from [SdFat libary](https://github.com/greiman/SdFat), which carries the following license:

````mit

Copyright (c) 2011..2020 Bill Greiman

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.```
````
