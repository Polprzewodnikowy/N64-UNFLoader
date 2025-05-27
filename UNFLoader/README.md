# UNFLoader Program

This folder holds the source code for the UNFLoader program itself. This README contains information on how to use, and how to build UNFLoader for Windows, macOS, and Linux operating systems. It also contains instructions for building the flashcart handling library, so that you can use it in your own projects.
</br>
</br>
### Table of Contents
* [System Requirements](#system-requirements)
* [How to use UNFLoader](#how-to-use-unfloader)
* [How to Build UNFLoader for Windows](#how-to-build-unfloader-for-windows)
* [How to Build UNFLoader for macOS](#how-to-build-unfloader-for-macos)
* [How to Build UNFLoader for Linux](#how-to-build-unfloader-for-linux)
* [Building only the Flashcart Library](#building-only-the-flashcart-library)
</br>

### System Requirements
<details><summary>Windows</summary>
<p>
    
* Windows XP or higher
    * Windows XP users should be on the latest Service Pack
* [The Windows version of this FDTI driver](https://www.ftdichip.com/Drivers/D2XX.htm). If you are on Windows XP, be sure you download the XP driver and not the first one.
</p>
</details>
    
<details><summary>Linux</summary>
<p>
    
* Ubuntu (Haven't tested with others)
* Run the included `installer_linux.sh` script to set up everything for you. If you are unable to launch the script, remember to mark it as executable.

If you do not wish to run the script, then you must:
* You must run UNFLoader with `sudo`.
</details>

<details><summary>macOS</summary>
<p>
    
![The macOS System Information window showing an FTDI device connected to a USB3 port](../Content/macos_system_report.png)
* Connect your flashcart to your computer via a Micro-USB cable. Confirm that the corresponding FTDI USB device appears in **System Information** window.
* If you grabbed a pre-built UNFLoader binary (AKA you didn't compile it yourself from the source code), you'll have to deal with the fact that macOS prevents running unsigned binaries by default. To fix this, go to the Security & Privacy panel in the System Preferences menu and explicitly allow UNFLoader to be run.
</p>
</details>
</br>

### How to use UNFLoader
Simply execute the program for a full list of commands. If you run the program with the `-help` argument, you have access to even more information (such as how to upload via USB with your specific flashcart). 
The most basic usage is `UNFLoader.exe -r PATH/TO/ROM.n64`. 

Append `-d` to enable debug mode, which allows you to receive/send input from/to the console (Assuming you're using the included USB+debug libraries). If you wrap a part of a command in '@' characters, the data will be treated as a file and will be uploaded to the cart. When uploading files in a command, the filepath wrapped between the '@' characters will be replaced with the size of the data inside the file, with the data in the file itself being appended after. For example, if there is a file called `file.txt` with 4 bytes containing `abcd`, sending the following command: `commandname arg1 arg2 @file.txt@ arg4` will send `commandname arg1 arg2 @4@abcd arg4` to the console. UNFLoader only supports sending 1 file per command.

Append `-l` to enable listen mode, which will automatically reupload a ROM once a change has been detected. **For listen mode to work, the console needs to be in a safe state**. This means that 64Drive users should have the console turned off, EverDrive users should have the console turned on and waiting on the menu, etc...

Append `-g` to open a GDB server. By default, the address `127.0.0.1:8080` is used. You can specify the address by adding it to the argument: `-g 192.168.1.68:27015`. You can also just specify a port: `-g 69420` or address `-g 192.168.1.68`. 
</br>
</br>
### How to Build UNFLoader for Windows
Simply load the project solution file in Visual Studio 2019 or higher. 

If you want to compile the 32-Bit version of the program, you will need to use Visual Studio 2019 and have the <a href="https://docs.microsoft.com/en-us/cpp/build/configuring-programs-for-windows-xp?view=msvc-160">Windows XP toolset</a> installed. This is because the 32-Bit version of the program/libraries is designed to run on Windows XP. If Windows XP support is not a concern, then build the 64-Bit version instead.

**The Include folder should already have everything you need for Windows.**
<details><summary>Updating libs to latest version (if required)</summary>

**pdcurses.lib**
* Grab the latest version of PDCurses from [here](https://github.com/wmcbrine/PDCurses).
* Extract the contents of the zip (preferrably somewhere with no spaces in the file path, like `c:\pdcurses`).
* Open the Visual Studio Command Prompt (Tools->Command Line->Developer Command Prompt).
* Run the command `set PDCURSES_SRCDIR=c:\PATH\TO\pdcurses`, obviously replacing the path with your one.
* CD into the `pdcurses/wincon` folder.
* Run the command `nmake -f Makefile.vc` to build pdcurses.
* Copy the `pdcurses.lib` that was compiled from the wincon folder to `UNFLoader/Include`, replacing the pdcurses library in there.
* Copy the `curses.h`, `curspriv.h`, and `panel.h` from the pdcurses directory and put them in `UNFLoader/Include`.
* Open `curses.h` and uncomment the line with `#define MOUSE_MOVED` to fix a warning due to `wincon.h`.

**ftd2xx.lib**
* Download the FTDI driver provided in the **Requirements** section and extract the executable from the zip.
* This is a self extracting executable, meaning you can open the .exe with with a zip program. 
* Grab `ftd2xx.h` and put it in `UNFLoader/Include`.
* Grab `ftd2xx.lib` from `i386` or `amd64` (depending on your CPU architecture) and put it in `UNFLoader/Include`.

**lodepng**
* Download the latest version of LodePNG from [here](https://lodev.org/lodepng/).
* Place `lodepng.cpp` and `lodepng.h` in `UNFLoader/Include`.

Once you have all of these files built and put in the `Include` folder, you're set to compile!
</details>
</br>

### How to Build UNFLoader for macOS
You will need to install libftdi in order to compile UNFLoader. Do this by invoking:
```
brew install libftdi
```

ncurses should come with your stock macOS, but it can be an old revision. If you want to update it, you can do so by invoking
```
brew install ncurses
```

Once the dependencies are installed, simply execute the makefile:

```
make
```

**The Include folder should already have everything you need for macOS.**
<details><summary>Updating libs to latest version (if required)</summary>

**ftd2xx + WinTypes**
* Download the FTDI driver provided in the **Requirements** section and extract the zip.
* Go into the `release` folder.
* Grab `ftd2xx.h` and `WinTypes.h` and put it in `UNFLoader/Include`.

**lodepng**
* Download the latest version of LodePNG from [here](https://lodev.org/lodepng/).
* Place `lodepng.cpp` and `lodepng.h` in `UNFLoader/Include`.

Once you have all of these files built and put in the `Include` folder, you're set to compile!
</details>
</br>

### How to Build UNFLoader for Linux
First, you need to install the dependencies. These are ncurses, libftdi, and libusb. Those can be installed in Ubuntu like so:

```
sudo apt-get install libncurses5-dev libncursesw5-dev libftdi1-dev libusb-1.0-0-dev libudev-dev
```

Once the dependencies are installed, simply execute the makefile:

```
make
```

If you haven't done so previously, it is recommended that you run `installer_linux.sh`. 

You can optionally install UNFLoader to `/usr/local/bin` (which will let you execute the tool in any folder, just remember to use `sudo`!) by calling:
```
sudo make install
```

and uninstall with:
```
sudo make uninstall
```
</br>

**The Include folder should already have everything you need for Linux.**
<details><summary>Updating libs to latest version (if required)</summary>

**lodepng**
* Download the latest version of LodePNG from [here](https://lodev.org/lodepng/).
* Place `lodepng.cpp` and `lodepng.h` in `UNFLoader/Include`.

Once you have all of these files built and put in the `Include` folder, you're set to compile!
</details>

### Building only the Flashcart Library

The flashcart handling part of the code can be compiled separately and then linked into your own separate project. When compiling the UNFLoader tool, it will compile a static library and then link it into the final executable. However if you wish to only compile the library, either a static or dynamic/shared form, that can be done as well. When linking this library into your own custom tools, you also need to link their dependencies. On Windows, this will be the D2XX library, while on MacOS and Linux it will be libftdi and libusb.

Compiling on Windows is as simple as loading and compiling either the "FlashcartLib_Dynamic" or "FlashcartLib_Static" Visual Studio project. On Linux and macOS, you can use `make static` or `make shared` respectively.