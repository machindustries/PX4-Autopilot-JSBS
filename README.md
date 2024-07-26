# PX4 Autopilot with JSBSim

This repository holds the [PX4](http://px4.io) flight control solution for drones, with the main applications located in the [src/modules](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules) directory. It also contains the PX4 Drone Middleware Platform, which provides drivers and middleware to run drones.

PX4 is highly portable, OS-independent and supports Linux, NuttX and MacOS out of the box. For out purposes at Mach, we will be configuring PX4 to run with our custom implementation of JSBSim with support for Viper.

The [PX4 User Guide](https://docs.px4.io/main/en/) explains how to assemble [supported vehicles](https://docs.px4.io/main/en/airframes/airframe_reference.html) and fly drones with PX4. The relevant steps from that guide are outlined below. The links will take you to any relevant pages from the guide but all the steps you'll need are shown. These instructions are **only for Ubuntu Linux** at this time. Refer to the links at the bottom of the doc to find the MacOS and Windows instructions.





## <span style="color: #ffcc33 ;">Installations/Setup</span>

### 1. Clone the forked PX4-Autopilot repo from Mach's GitHub
```bash
git clone git@github.com:machindustries/PX4-Autopilot-JSBS.git
```

### 2. Setup the Development Environment

#### For [Ubuntu Linux](https://docs.px4.io/main/en/dev_setup/dev_env_windows_wsl.html):
```bash
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
# restart your machine upon completion of this command
```
<!--
#### For [MacOS](https://docs.px4.io/main/en/dev_setup/dev_env_mac.html) (untested):
<details>
  <summary>Apple M1 Macbook users!</summary>

    If you have an Apple M1 Macbook, make sure to run the terminal as x86 by setting up an x86 terminal:

    1. Locate the Terminal application within the Utilities folder (Finder > Go menu > Utilities)
    2. Select Terminal.app and right-click on it, then choose Duplicate.
    3. Rename the duplicated Terminal app, e.g. to x86 Terminal
    4. Now select the renamed x86 Terminal app and right-click and choose *Get Info
    5. Check the box for Open using Rosetta, then close the window
    6. Run the x86 Terminal as usual, which will fully support the current PX4 toolchain  ## 1.1. Subsection

</details>

```bash
# enable more open files by appending this to your ~/.zshenv file (create if necessary)
echo ulimit -S -n 2048 >> ~/.zshenv

# enforce Python 3 by appending the following to your ~/.zshenv
# Point pip3 to MacOS system python 3 pip
alias pip3=/usr/bin/pip3

# install Homebrew if you do not already have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# install common tools
brew tap PX4/px4
brew install px4-dev

# install required Python packages using pip3
python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema
# if this fails with a permissions error, your Python install is in a system path - use this command instead:
sudo -H python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema

```

#### For [Windows (via WSL2)](https://docs.px4.io/main/en/dev_setup/dev_env_windows_wsl.html) -->

### 3. [Download QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)

#### For Ubuntu Linux
```bash
# enter the following in the terminal
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
sudo apt install libfuse2 -y
sudo apt install libxcb-xinerama0 libxkbcommon-x11-0 libxcb-cursor0 -y

# logout and log back in to enable the change to user permissions

# download the QGroundControl application
cd ~/Downloads
sudo wget https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl.AppImage

# make the file executable and run
chmod +x ./QGroundControl.AppImage
./QGroundControl.AppImage  (or double click)

# once it runs you can kill it
```

<!-- #### For MacOS
```bash
# download the QGroundControl application
brew install wget
wget https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl.dmg
# double click the .dmg file to mount it
# drag it to your Application folder
``` -->

### 4. [Install default JSBSim](https://docs.px4.io/main/en/sim_jsbsim/)

#### For Ubuntu Linux (22.04)
```bash
# download the JSBSim development release
sudo wget https://github.com/JSBSim-Team/jsbsim/releases/download/Linux/JSBSim-devel_1.2.1.dev1-1342.jammy.amd64.deb
# install the package
dpkg -i  JSBSim-devel_1.2.1.dev1-1342.jammy.amd64.deb

# (optional) install FlightGear which we may try to use for visualization
# TODO ADD INSTRUCTIONS IF WE ACTUALLY USE THIS
```
For other versions of Ubuntu, download and install the appropriate file from the [JSBSim release page](https://github.com/JSBSim-Team/jsbsim/releases/tag/Linux).








## <span style="color: #ffcc33 ;">Building PX4</span>

### 1. Build PX4 w/ default JSBSim

#### Ensure that you can build and launch PX4 with JSBSim

```bash
# WINDOW 1: Launch QGC
cd ~/Downloads
./QGroundControl.AppImage
```

```bash
# WINDOW 2: Build and run PX4 with JSBSim
cd ~/PX4-Autopilot-JSBS
make
HEADLESS=1 make px4_sitl jsbsim
```

Without the `HEADLESS=1`, you will get a popup saying "The requested aircraft (Rascal110-JSBSim) could not be found. No aircraft paths are configured." This environment variable instructs PX4 to not use the visualization.


### 2. Build PX4 with our custom JSBSim

**working on this now ; )**

```bash
# remove existing JSBSim installation
sudo dpkg --remove jsbsim-devel
```


```bash
# add some symbolic links
sudo ln -s /usr/local/lib/libJSBSim.a /usr/lib/libJSBSim.a
sudo ln -s /usr/local/include/JSBSim /usr/include/JSBSim
```








## Links

- [PX4 simulation page](https://docs.px4.io/main/en/simulation/)
- [PX4 JSBSim page](https://docs.px4.io/main/en/sim_jsbsim/)
- [Original PX4 repo](https://github.com/PX4/PX4-Autopilot)
- [PX4 Development - Getting Gtarted (refer here to find setup instructions for MacOS and Windows)](https://docs.px4.io/main/en/dev_setup/getting_started.html)
- [QGroundControl Download/Install page](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)

