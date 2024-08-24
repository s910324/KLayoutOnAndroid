# KLayoutOnAndroid
Setup guide/note for running KLayout on ARM based Android devices
Linux installation using distro provided by https://andronix.app/

| # | Steps                                                | Time takes | On android | On PC | Type         |
|---|------------------------------------------------------|------------|------------|-------|--------------|
| 1 | Download and install essential apps                  | 20 min     | V          | V     | Setup        |
| 2 | Fix Android 12 device compatability issue            | 5 min      | V          | V     | Setup        |
| 3 | Install Android-Ubuntu 20.04 in Termux               | 30 min     | V          |       | Installation |
| 4 | Install dependency in Android-Ubuntu                 | 10 min     | V          |       | Installation |
| 5 | Download KLayout source to Android-Ubuntu            | 10 min     | V          |       | Installation |
| 6 | Build KLayout from source on Android-Ubuntu          | 2 hr       | V          |       | Installation |
| 7 | Access Android-Ubuntu Klayout interface through RVNC | 1 min      | V          |       | Run          |


***

### 1. App installation
| # | Type        | App name  | Source      | Download link                                                                  | Remark                                                                |
|---|-------------|-----------|-------------|--------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| 1 | PC app      | adb Tools | Google      | https://developer.android.com/tools/releases/platform-tools                    | Fix for "Signal-9" Error for Android 12 and above                     |
| 2 | Android app | Termux    | F-Droid     | https://f-droid.org/en/packages/com.termux/                                    | Terminal emulator for Linux installation                              |
| 3 | Android app | RVNC      | Google play | https://play.google.com/store/apps/details?id=com.realvnc.viewer.android&hl=en | Access remote desktop, in this case Linux installed locally in Termux |

Install Apps listed above to corrosponded devices

***

### 2. Android 12 compatability
Full instruction can refer to Androix's article
https://docs.andronix.app/android-12/andronix-on-android-12-and-beyond#install-adb

* 2.1\. Enable Android `Developer Options`
* 2.2\. Enable Android `USB Debugging` in `Developer Options`
* 2.3\. Connect Android device to PC through cable
* 2.4\. use adb command below interminal to solve Phantom process issue

```
adb shell "/system/bin/device_config set_sync_disabled_for_tests persistent"
adb shell "/system/bin/device_config put activity_manager max_phantom_processes 2147483647"
adb shell settings put global settings_enable_monitor_phantom_procs false
```

***

### 3. Install Linux on Android device

* 3.1\. Open Termux
* 3.2\. Long press the screen and hit the `more` option, and choose `Keep screen on` to 
* 3.3\. Paste command below and hit enter, this pulls and installs Ubuntu-XFCE 20.04 from `Andronix`, higher version is not recommended due to some of the some package dependency is not fully avaliable
```
pkg update -y && pkg install wget curl proot tar -y && wget https://raw.githubusercontent.com/AndronixApp/AndronixOrigin/master/Installer/Ubuntu20/ubuntu20-xfce.sh -O ubuntu20-xfce.sh && chmod +x ubuntu20-xfce.sh && bash ubuntu20-xfce.sh
```
* 3.4\. Occationally it'll requires a user input `Y` or choose region and key boardlayout during the Andriod-Ubuntu intallation process

***

### 4. Install dependency in Android-Ubuntu  
* 4.1\. install build dependency through apt-get install, the `libgit2-dev` is required, otherwise will run into `git2 Fatal error`
```
apt-get update&&apt-get install -y qt5ct qt5-qmake gcc g++ make libz-dev libqt5xmlpatterns5-dev qtmultimedia5-dev libqt5multimediawidgets5 libqt5svg5-dev ruby ruby-dev python3 python3-dev libgit2-dev
```

***

### 5. Download KLayout source to Android-Ubuntu 
* 5.1\. Download and unzip the source from KLayout, by paste following commands in Termux.
Following script uses klayout version 0.29.6 as an example, get latest version from : https://www.klayout.de/build.html
```
wget https://www.klayout.org/downloads/source/klayout-0.29.6.tar.gz
tar -xvzf klayout-0.29.6.tar.gz
```

***

### 6. Build KLayout from source
* 6.1\. Move working directory to unzipped folder and starts the build process
```
cd klayout-0.29.6
./build.sh -without-qtbinding -j4 -prefix /usr/bin/klayout
```
