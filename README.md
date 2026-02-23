# Embedded Systems RP2350 Lab-Kit Instructions

The purpose of this repository is to give clearer information on how to setup and use the RP2350 Lab-Kit used in several courses at KTH. In particular, it helps with running projects on the board _without_ the use of VSCode, which otherwise is very short and unclear, as it mostly otherwise assumes you simply use VSCode for some reason.

The board is at the very least used in the courses IL2206, IL2212, and IL2235.

[Repo for the Lab Kit.](https://gits-15.sys.kth.se/mabecker/ES-Lab-Kit)

## Board Setup (without vscode (ew)!!!)
**Setup done on arch.** Should be very similar on other linux distros, just different command for installing the dependencies of course.

### Flashing Debugger Firmware (Only done once!)
* Download `debugprobe_on_pico2.uf2` from [here](https://github.com/raspberrypi/debugprobe/releases), or using this [direct link](https://github.com/raspberrypi/debugprobe/releases/download/debugprobe-v2.2.3/debugprobe_on_pico2.uf2).
* Hold the `BOOTSEL` button of the debugger (`SW3`). While the button is pressed, connect the USB-C cable to the right USB-C connector `J5`.
* The board will mount as a device called `RP2350`.
* Open the folder `RP2350` in the finder and copy the file `debugprobe_on_pico2.uf2` to it. 
* After the file is transferred, the board unmounts automatically.
* Now the board is ready to be used.

### Dependencies
`sudo pacman -S arm-none-eabi-gcc arm-none-eabi-newlib`

Might require other dependencies, but those should be very clear if it tells you that a command doesn't exist. Obvious ones are git, make, and cmake.

### pico-sdk
```
git clone git@github.com:raspberrypi/pico-sdk.git
export PICO_SDK_PATH=~/path/to/pico-sdk
```
Might have to add the export line to `~/.bashrc` instead if it's temporary. Alternatively, perform the export command inside each project. It will tell you during the `cmake ..` step if you need to export a path for `PICO_SDK_PATH`.

### openocd
```
git clone --recursive git@github.com:raspberrypi/openocd.git
cd openocd
./bootstrap
./configure --enable-internal-jimtcl --enable-cmsis-dap --disable-werror
make -j4
sudo make install
```
Test that openocd is correctly installed with `openocd --version`.

### Lab Setup
```
git clone --recursive git@gits-15.sys.kth.se:mabecker/ES-Lab-Kit.git
```
You can build projects under `/ES-Lab-Kit/Software`.

### Building
Try that everything works with the example project.

Go to `ES-Lab-Kit/Software/example/`.
```
mkdir build
cd build
cmake ..
make
```

In `build/` there should now be a file `LabKitTest.elf`.

### Downlading the binary to the board
Make sure the USB-C cable is connected to the debug port. If connected properly, `lsusb` should give a Device named _Raspberry Pi Debugprobe on Pico (CMSIS-DAP)_.

Within the build directory of the project (`e.g. ES-Lab-Kit/Software/example/build/`):
```
sudo openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg -c "adapter speed 5000" -c "program PROGRAM_NAME.elf verify reset exit"
```
(Replace PROGRAM_NAME.elf with e.g. `LabKitTest.elf` from the building step).

Example project should now be running on the board if everything was done correctly.
