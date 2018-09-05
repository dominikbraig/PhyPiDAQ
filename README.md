# PhyPiDAQ

Data Acquisition and analysis for Physics education with Raspberry Pi

This *python3*  code provides some basic functionality for data acquisition and visualization like data logger, bar-chart, XY- or oscilloscope display. The visualization depends on *matplotlib.pyplot*, *Tkinter* and *pyQt5*.

In addition to the GPIO inputs/outputs of the Raspberry Pi, the analog-to-digital converters MCP3008 and ADS1115 and PicoScope USB-Oscilloscopes are supported as input devices for analog data, as well as a number of digital sensors using protocols like I²C or SPI. 

## Quick-start guide

After installation - see below - a number of unified classes for data acquisition is available from the sub-directory `./phypidaq`.
Each device needs a specific configuration, which is read from configuration files in sub-directory `./config`. The overall configuration is given in files of type `.daq`, specifying which devices and display modules to use, the readout rate, calibrations or analytical formulae to be applied to recorded data, or ranges and axis labels of the graphical output. 

A graphical user interface `phypi.py` aids in the administration of the configuration options and can be used to start data acquisition. In this case, configurations and produced data files are stored in a dedicated sub-directory in `$HOME/PhyPi`.The name is derived from a user-defined tag and the current date and time.

Data acquisition may also be started via the command line:

    run_phypi.py <config_file_name>.daq

If no configuration file is given, the default `PhyPiConf.daq` is used.

The sub-directory `./examples` contains a number of simple *python* scripts illustrating the usage of data acquisition and display modules with minimalist code.

## Konfiguration

The script run_phypi.py allows users to perform very general measurement tasks without the need to write custom code. The options for configuration of input devices and their channels as well as for the display and data storage modules are specified in a global configuration file of type `.daq` (in yaml markup
language), which contains references to device configuration files of type `.yaml`.

A typical, commented example is shown here:

### file PhyPi.daq

    # Configuration Options for PhyPiDAQ

    # device configuration files
    DeviceFile: config/ADS1115Config.yaml
    #DeviceFile: config/MCP3008Config.yaml
    #DeviceFile: config/PSConfig.yaml
    #DeviceFile: config/MAX31865Config.yaml
    #DeviceFile: config/GPIOCount.yaml

    ## an example for multiple devices
    #DeviceFile: [config/ADS1115Config.yaml, config/ GPIOCount.yaml]

    DisplayModule: DataLogger
    # DisplayModule: DataGraphs  # text, bar-graph, history and xy-view
    Interval: 0.1                     # logging interval
    XYmode:     false                 # enable/disable XY-display

    # channel-specific information
    ChanLabels: [(V), (V) ]          # names and/or units for channels 
    ChanColors: [darkblue, sienna]    # channel colours in display

    # eventually overwrite Channel Limits obtained from device config 
    ##ChanLimits: 
    ## - [0., 1.]   # chan 0
    ## - [0., 1.]   # chan 1
    ## - [0., 1.]   # chan 2

    #ChanCalib:
    #  - null    or  - <factor> or  - [ [ <true values> ], [ <raw values> ] ] 
    #  - 1.                       # chan0: simple calibration factor
    #  - [ [0.,1.], [0., 1.] ]    # chan1: interpolation: [true]([<raw>] )
    #  - null                     # chan2: no calbration

    # apply formulae to calibrated channel values
    #ChanFormula:
    #  - c0 + c1  # chan0
    #  - c1          # chan1
    #  - null        # chan2 : no formula

    # name of output file
    DataFile:   null                  # file name for output file 
    #DataFile:   testfile             # file name for output file 

The device configuration file for the analog-to-digital converter **ADS1115**
specifies the active channels and their ranges:

### file ADS1115Config.yaml

    # example of a configuration file for ADC ADS1115

    DAQModule: ADS1115Config    # phypidaq module to be loaded

    ADCChannels: [0, 3]         # active ADC-Channels
                            # possible values: 0, 1, 2, 3
                            # when using differential mode:
                                #    -  0 = ADCChannel 0 
                                #            minus ADCChannel 1
                                #    -  1 = ADCChannel 0 
                                #            minus ADCChannel 3
                                #    -  2 = ADCChannel 1 
                                #            minus ADCChannel 3
                                #    -  3 = ADCChannel 2 
                                #            minus ADCChannel 3

    DifModeChan: [true, true] # enable differential mode for Channels

    Gain: [2/3, 2/3]          # programmable gain of ADC-Channel
                              #   possible values for Gain:
                              #     - 2/3 = +/-6.144V
                              #     -   1 = +/-4.096V
                              #     -   2 = +/-2.048V
                              #     -   4 = +/-1.024V
                              #     -   8 = +/-0.512V
                              #     -  16 = +/-0.256V
    sampleRate: 860           # programmable Sample Rate of ADS1115
                              #    possible values for SampleRate: 
                              #    8, 16, 32, 64, 128, 250, 475, 860

Examples for other devices like the PicoTech USB-scope PicoScope, the analog-to-digital converter MCP3008 or for rate measurements via the GPIO pins are contained in the configuration directory, `PSConfig.yaml`, `MCP3008Config.yaml and `GPIOcount.yaml`, respectively.


## Installation

This package relies on code from some other packages that need to be installed first:

```html
- Adafruit Pyhon MCP3008 library, <https://github.com/adafruit/Adafruit_Python_MCP3008>
- Adafruit Python ADX1x15 library, <https://github.com/adafruit/Adafruit_Python_ADS1x15>
- http://github.com/GuenterQuast/picoDAQ
- the  *python* bindings of the *pico-python* project by Colin O'Flynn  
   <https://github.com/colinoflynn/pico-python> and
- the low-level drivers contained in the Pico Technology Software Development Kit <https://www.picotech.com/downloads>
```

For conveniencs, installation files for external packages in pip wheel format are provided in subrirectory *.whl*.

After setting up your Raspberry Pi with the actual
stable debian release *stretch*, the following steps should be taken to update and install all necessary packages:

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install python3-scipy
sudo apt-get install python3-matplotlib
sudo apt-get install python3-pyqt5

sudo pip3 install pyyaml

# PicoTech base drivers for picoScope usb devices
#   see https://www.picotech.com/support/topic14649.html
# after inclusion of the picotech raspbian repository:  
sudo apt-get install libps2000a
# allow access of user pi to usb port
sudo usermod -a -G tty pi

# get PhyPiDAQ code and dependencies
mkdir git
cd git
git clone https://GuenterQuast/PhyPiDAQ
cd PhyPicDAQ/whl
sudo pip3 install *.whl
```
## Recommended Sensors and Devices

## Description of the examples
