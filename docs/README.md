# crazy-practical
Crazyflie hardware practical for the Aerial Robots course at EPFL.

In this practical, you will learn how to program a [Crazyflie](https://www.bitcraze.io/) to find and precisely land on a platform with the help of minimal sensory information. Additionally, you will use sensor readings to avoid the obstacles present in the environment.

<p align="center">
<img width=700 src="https://github.com/dronecourse-epfl/crazy-practical-2022/blob/master/docs/pictures/crazyfly_objective_figure.png"/>
</p>

For more details on the task, submission, schedule and grading, please refer to the [moodle](https://moodle.epfl.ch/course/view.php?id=15799) page of the course.

## Requirements
For this practical, each team is required to use one of their personal laptops. Bitcraze supports the installation of the software on Windows, Linux, OS X and Virtual Machines. However, we tested the installation process only on:
* Windows 10
* Ubuntu 20.04
* Ubuntu 18.04

Other software requirements are:
* Python 3.7+

## 1. Unpacking
<a name="unpacking"></a>
At the beginning of the practical, every team will receive a box with the necessary hardware. This includes:
1. One [Crazyflie package](https://store.bitcraze.io/collections/kits/products/crazyflie-2-1)
  * 1 x Crazyflie 2.1 with all components mounted
  * 1 x Crazy-radio dongle
  * 2 x LiPo battery (240mAh)
  * 1 x Battery charger
  * 1 x Battery deck
  * 1 x USB cable
2. One spare parts set
  * 1 x spare motor
  * 2 x spare motor mounts
  * 3 x spare propellers CW 
  * 3 x spare propellers CCW
4. One [flow deck v2](https://store.bitcraze.io/collections/decks/products/flow-deck-v2)
5. One [multi-ranger deck](https://www.bitcraze.io/products/multi-ranger-deck/)
<!-- 6. 4 x pattern carpet piecs of 60x60cm -->

## 2. Assembling
The Crazyflie has been already assembled. However, in case you need to replace the broken parts like propellers, refer to the instructions [here](https://www.bitcraze.io/getting-started-with-the-crazyflie-2-0/#assembling).

## 3. Installing the Pyhon library
For developing the code for the practical, you will first need to install [Python 3.7+](https://www.python.org/downloads/release/python-379/) if you don't have it yet. Then, you can clone and install the [Crazyflie Python Library](https://github.com/bitcraze/crazyflie-lib-python.git).

For cloning it, type:
```
git clone https://github.com/bitcraze/crazyflie-lib-python.git
cd crazyflie-lib-python
git checkout tags/0.1.18.1 -b v0.1.18.1-branch
```
[Optional] For installing the Crazyflie Python Library in a virtual environment, you first have to create the virtual environment by typing:

```
pip3.7 install virtualenv # install virtualenv
python3.7 -m virtualenv venv # create an environment called venv
source venv/bin/activate # activate the environment
```

Then, install the Crazyflie Python Library with the following command. `For Windows`, you need to install python, pip, and crazyradio driver according to the instruction [here](https://www.bitcraze.io/documentation/repository/crazyflie-clients-python/master/installation/install/), before running the following command. `In Windows`, if `pip3` command is not found, then you need to use `pip` instead.

```
pip3 install -e .
```

For data analysis and plot, install pandas with the command:
```
pip3 install pandas
```

To deactivate the virtualenv when you are done, type `deactivate`. However, you should have it active when working on the project.

## 4. Setting up the radio interface (only for Ubuntu)
You need to make sure that you have the right usb permission for the radio interface. You can find extensive instructions [here](https://github.com/bitcraze/crazyflie-lib-python#setting-udev-permissions).
On your terminal issue the following commands:
```
sudo groupadd plugdev
sudo usermod -a -G plugdev $USER
```
Then log out and log back in to update the plugdev group.

Create the following file (you may need `sudo` permissions):
```
touch /etc/udev/rules.d/99-crazyradio.rules
```

Then edit it with the text editor of your preference, and add the following lines:
```
# Crazyradio (normal operation)
SUBSYSTEM=="usb", ATTRS{idVendor}=="1915", ATTRS{idProduct}=="7777", MODE="0664", GROUP="plugdev"
# Bootloader
SUBSYSTEM=="usb", ATTRS{idVendor}=="1915", ATTRS{idProduct}=="0101", MODE="0664", GROUP="plugdev"
```

Reload the udev-rules using the following:
```
sudo udevadm control --reload-rules
sudo udevadm trigger
```

## 5. Installing the Crazyflie Client
Follow the instructions [here](https://www.bitcraze.io/getting-started-with-the-crazyflie-2-0/#inst-comp) to install the Crazyflie Client and connect to your Crazyflie. To install it from source on Ubuntu, follow these steps:

Clone the `crazyflie-clients-python` repository and install it:

```
git clone https://github.com/bitcraze/crazyflie-clients-python.git
cd crazyflie-clients-python
git checkout tags/2022.1 -b v2022.1
pip3 install -e .
```

Now, you can open the client with command:
```
cfclient
```

## 6. Testing the Crazyflie library
To test that the Python library has been installed properly, you can run one example from the `crazyflie-lib-python`. First, be sure that the crazyflie is on, the radio plugged and the address of the crazyflie is the default one. Then, type one of the following in your terminal:

```
python3 examples/logging/basiclog.py
```
## 7. Configuring the Crazyradio and the Crazyflie address
Every team will use a unique radio channel and Crazyflie address. 
Radio channel = 80 + 10*(mod(<team_number>,3) - 1)
Crazyflie address = 0xE7E7E7E7<team_number>

All you need to do is:
* Plug the Crazyradio in your pc
* Power on the Crazyflie
* Start the Crazyflie Client (with the command `cfclient`)
* Select your Crazyradio and 'Scan'
* Connect to your Crazyfie (default address is 0xE7E7E7E7E7)
* In the 'Connect' tab on top, select 'Configure 2.X'
* In the window that openned up, enter your channel number and radio address. Then, click 'Write'.
* Restart the Crazyflie and the client
* Connect to your Crazyfie using your assigned address

# Coding your algorithm
Now it's your time to code!
You will find example scripts for this practical in this repository.
First, you need to clone this repository and add your teammate as collaborators.
```
git clone https://github.com/dronecourse-epfl/crazy-practical-2022/
```

Several scrips are available: `position_commander.py` illustrates how to issue simple position commands, while `basiclog.py` 
shows how to acquire and log roll, pitch, and yaw angle estimates from the Crazyflie and save them in a `csv` file. Your logs are saved in the `logs` folder and you can analyze them with the example `logs_plotter.py`.
Finally, `logandfly.py` combines the two previous examples into one script, where your drone will fly through a simple maneuver and log its position estimated and sensor data. Again, you can visualized the acquired dataset with the `logs_plotter.py` script.

## Connect to your Crazyflie
Before launching each example python file, be sure that you updated the Crazyflie URI with the one of your drone, by editing the line of `uri = uri_helper.uri_from_env(default='radio://0/80/2M/E7E7E7E7E7')`.

To run the examples, call them from the `crazy-practical` folder like this:
```
python3 basiclog.py
```
## Sending commands
To send position commands to your Crazyflie, follow the example `position_commander.py`.

## Reading and saving sensor measurements
The file `basiclog.py` shows how to log your Crazyflie data. Add the variables to log in the `_connected` function.

A visualization of the multi-ranger measurements is available in `multiranger_pointcloud.py`. In this file, you can see how to adjust the distance threshold of your sensors, i.e. the distance within which you want to consider measurements.

## Flight analysis
The script `logandfly.py` allows to perform a simple manevuer and log position estimates and sensor readings.

## Visualizing logs
For visualizing the logs content, follow the example `logs_plotter.py`.
