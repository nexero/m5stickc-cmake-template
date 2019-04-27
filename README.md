[![Build Status](https://travis-ci.org/nexero/m5stickc-cmake-template.svg?branch=master)](https://travis-ci.org/nexero/m5stickc-cmake-template)

# Template for M5StickC Development with ESP-IDF and CMake

Thanks to the CMake plugin, this also integrates nicely with Visual Studio Code (you'll have to select the `xtensa-esp32-elf-gcc` when opening the folder in VSCode, but first be sure to clone recursivly and install the Espressif ESP32 Compiler Toolchain!).

## Hardware & Pin Map

https://docs.m5stack.com/#/en/core/m5stickc

## Build instructions


### Clone recursivly

```bash
# Recursive clone is important!
git clone --recursive <this.repository>
cd <this.repository.folder>
# Install ESP-IDF Python dependencies
/usr/bin/python -m pip install --user -r esp-idf/requirements.txt
```

### Install Espressif ESP32 Compiler Toolchain

Like described in chapter **Step 1. Set up the Toolchain**:
https://docs.espressif.com/projects/esp-idf/en/latest/get-started/index.html#step-1-set-up-the-toolchain

***NOTE***: You'll only need Step 1. Ignore the following steps of the Espressif guide!

### CMake Configure

```bash
mkdir build; cd build; 
cmake .. -G Ninja
```

### Menuconfig

From within the `build` directory, execute
```bash
ninja menuconfig
```

### Compile & Upload
```bash
# M5StickC does't accept esptool.py's default 460800 bps, so you must lower it to 115200 bps!
ESPBAUD=115200 ninja flash
```

## Add New Dependencies

The repository is organized using `git submodule`:

```bash
cd components
# add new component with a specific branch to track:
git submodule add --branch <another.repository.branch.to.track> <another.repository>
cd ..
# eventually you'll have to initialize and update subsequent submodules
git submodule update --recursive --checkout --force --init components/<another.repository>
# if the new submodule also contains submodules:
git config -f .gitmodules submodule.components/<another.repository>.fetchRecurseSubmodules true
# better not track changes in the new submodule, if it doesn't belong to you:
git config -f .gitmodules submodule.components/<another.repository>.ignore untracked
# checkout specific tag or commit:
cd components/<another.repository>
git checkout <another.repositories.tag_or_commit>  # ignore the 'detached HEAD' warning!
cd ../..
git add components/<another.repository>
# commit new component
git commit -m "add new component <another.repository>@<another.repositories.tag_or_commit>"
```

## Update Dependency

First the update:

```bash
git submodule update --remote --recursive --force components/<another.repository>
```

Second test your build! Then commit your changes.

## Remove Dependency

```bash
git submodule deinit -f components/<another.repository>
git rm -rf components/<another.repository>
git commit -m "Removed component <another.repository>"
rm -rf .git/modules/component/<another.repository>
```

## Update ESP-IDF

```bash
# optional: set new branch to track
git config -f .gitmodules submodule.esp-idf.branch <new.Version.branch>
# update esp-idf main repo
git submodule update --remote --force esp-idf
# checkout submodules for the branch HEAD of esp-idf
git submodule update --checkout --force --recursive esp-idf
# update python dependencies
/usr/bin/python -m pip install --user -r esp-idf/requirements.txt --force
```

Again, double check compilation, then commit.
