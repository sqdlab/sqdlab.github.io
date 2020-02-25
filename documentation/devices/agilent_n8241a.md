# Documentation for AWG (Agilent N8241A)    

This device is a AWG (arbitrary waveform generator), following is the python code used in the lab, to connect to it remotely, _given your system is **in** lab network_   

___
## Information & Parameters:      

User manual of this AWG can be found [here](https://drive.google.com/file/d/1o1-JMvFjlx5lxqX2LfHPpMJ9IjT5u6t2/view?usp=sharing)

Each AWG contains few key things:

1. Channels: This output the given signal
2. Marker: Used for triggering and syncing with other type of instruments
3. Trigger: Used to put multiple AWGs in master-slave configuration
4. Ext_CLK: Used to sync with external clock.

Also **this** AWG (channels) has 2 modes of operation:

1. Burst Mode (The sequence repeats indefinitely or until an event trigger is received.)
2. Continous mode (The sequence is repeated a predefined number of times. This mode requires a
start trigger.)   

___
## Python Script for using AWG (SQD lab specific):    

### Creating the object and connecting to AWG:    

**Note**: Make sure to install the IVI foundation software to control the AWG.   

```python

from qcodes.instrument_drivers.sqdlab.Agilent_N8241A import Agilent_N8241A

awg_agilent1 = Agilent_N8241A('awg_agilent1', ivi_dll=r'C:\Program Files\IVI Foundation\IVI\Bin\AGN6030A.dll', address='TCPIP::<IP ADDRESS>::INSTR', reset=True) 
```

### Resetting previously used AWGs:    

**NOTE** : Resetting is done in reversed order, because Slave AWGs are reset first.   

```python
#awgs = [awg_agilent0, awg_agilent1, awg_agilent2]
awgs = [awg_agilent1]

# reset
for awg in reversed(awgs):
    #print('resetting {0:s}'.format(awg.get_name()))
    try:
        awg.reset()
    except Exception as e:
        print(e)
```
___
## NOTE:   

There are 2 type of paramters:    
1. AWG specific paramters.    
2. Channel specific paramters.    

Rest of the Documentation is divided into these two sections.

___
### Setting _AWG_ paramters:    

#### Set reference clock:    
It can be "internal" or "external".

```python
awg.ref_clock_source('External')
# Internal Clock Stuff:
# 1.2GHz clock from Tektronix AWG
#awg_tek.run()
```

#### Set sample clock:    
Source = 0(internal) or 1(external)   
Freq = 1.25e9 (fixed for internal) or 100e6 to 1.25e9 (external)

**NOTE for external clock**: Make sure to supply the "sample clock ext in" with an appropriate signal. A microwave source (like the Rhode and Schwarz SGS100A) at 0 dBm power does the trick. Make sure to split the same source to all AWGs.

```python
# For internal:
awg.configure_sample_clock(source=0, freq=1.25e9)

# For external:
# Here, the freq is being taken from Master AWG.
awg.configure_sample_clock(source=1, freq=awgs[0].get_clock_frequency())
```

#### Set clock Sync:     
It is **False** or **True** depending upon Master or Slave configuration.

```python
# For master configuration:
awg.configure_clock_sync(enabled=False, master=True)
```

#### Set trigger threshold:
This should be set when using an external clock or master AWG for trigger .
Ususaly the value is between -4.5volt to 4.5volt.

```python
awg.trigger_threshold_A(1.)
```

For **using marker as a trigger**:
```python
awg.m4.source('Hardware Trigger 1')
```
   
#### Set marker:
This is set when using AWG as a Master.
```python
awg.m1.source('Channel 1 Marker 1')
awg.m2.source('Channel 1 Marker 2')
awg.m3.source('Channel 2 Marker 1')
awg.m4.source('Channel 2 Marker 2')
```
___
### Setting _Channel_ paramters:   

#### Mode of operation:   
It can be **burst** or **Continuous** , as decribed above.   


```python
awg.ch1.operation_mode('Burst')
```

#### Setting burst count:   
For **burst** mode, burst count = no.of times the given sequence is repeated after each trigger.

```python
awg.ch1.burst_count(1)
```

#### Output Mode:   
Various output mode of channel are :   
* differential output    
* single ended output    
* amplified output

A differential voltage is “floating”, meaning that it has no reference to ground. The measurement is taken as the voltage difference between the two wires.   

A single-ended measurement is taken as the voltage difference between a wire and ground.   

More about Differential output vs single ended output [here](https://www.apogeeinstruments.com/differential-vs-single-ended-measurements/
)

```python
awg.ch1.output_configuration('Amplified')
````

#### Predistortion:    
The predistortion function compensates for the variation in the magnitude of the
output response as a function of frequency. This variation is the result of the sin x/x (sinc) roll-off of the internal DAC and the frequency response of the reconstruction filter. The correction method uses filters to level the amplitude response and to create a linear phase response at the front panel of the AWG. This process attenuates the signal as a function of frequency, but cannot increase the signal above the maximum output voltage. Therefore, it is necessary to attenuate the lower frequency signals. This results in a reduced output voltage and dynamic range at all frequencies, but with uniform response across the full frequency range.    

```python
awg.predistortion(False)
```

#### Output Filters & filter bandwidth:
Output filter is just a boolean value (True or False), which dictate the behavior of filter on the output. If set to enable, user must specifiy the bandwidth.   

```python
awg.ch1.output_filter(False)
#awg.set_ch1_output_bandwidth(500e6) <only enbale is required>
```
    
#### Gain:
This is specified in terms of volts.   

```python
awg.ch1.gain(0.5)
```

#### Output Impedance:
This is usually 50ohms.   
 
```python
awg.ch1.output_impedance(50)
```   

#### Trigger source:
For a given channel, the trigger source can be set using:

```python
awg.ch1.configure_trigger_source(1|1026|1028|1032|1040)
```   

Here, the numbers mean:

```
1: External Trigger
2: Software Trigger
1024: No Trigger Flag
1025: Software 1 Flag
1056: Software 2 Flag
1088: Software 3 Flag
1026: External 1 Flag
1028: External 2 Flag
1032: External 3 Flag
1040: External 4 Flag
4195328: LXI Trig1 Flag
```        

#### Enable Channel output:

```python
awg.ch1.output(True)
```    
___
## Sample python Script:   
A sample python configuration script is attached below:   

```python
from qcodes.instrument_drivers.sqdlab.Agilent_N8241A import Agilent_N8241A

awg_agilent1 = Agilent_N8241A('awg_agilent1', ivi_dll=r'C:\Program Files\IVI Foundation\IVI\Bin\AGN6030A.dll', address='TCPIP::127.0.0.1::INSTR', reset=True) 

#awgs = [awg_agilent0, awg_agilent1, awg_agilent2]
awgs = [awg_agilent1]

# reset
for awg in reversed(awgs):
    #print('resetting {0:s}'.format(awg.get_name()))
    try:
        awg.reset()
    except Exception as e:
        print(e)

# awgs clocking and syncronization
for awg in awgs[:1]:
    # use external 10MHz ref quratz clock
    awg.ref_clock_source('External')
    #awg.configure_sample_clock(source=1, freq=1.20e9)
    # 1GHz clock from microwave source
    #awg.configure_sample_clock(source=1, freq=1e9)
    # 1.25GHz internal clock
    awg.configure_sample_clock(source=0, freq=1.25e9)
    awg.configure_clock_sync(enabled=False, master=True)

# Below should be used for setting up Master AWG
for awg in awgs[1:]:
    awg.configure_sample_clock(source=1, freq=awgs[0].get_clock_frequency())
    awg.configure_clock_sync(enabled=True, master=False)

# channel settings
for awg in awgs:
    awg.ch1.operation_mode('Burst')
    awg.ch1.burst_count(1)
    awg.ch1.output_configuration('Amplified')
    awg.predistortion(False)
    awg.ch1.output_filter(False)
    #awg.set_ch1_output_bandwidth(500e6)
    awg.ch1.gain(0.5)
    awg.ch1.output_impedance(50)
    awg.ch1.output(True)
    awg.m1.source('Channel 1 Marker 1')
    awg.m2.source('Channel 1 Marker 2')
    awg.m3.source('Channel 2 Marker 1')
    awg.m4.source('Channel 2 Marker 2')
    #awg.set_ref_clock_source('Internal')
    #awg.ch1.configure_trigger_source(1024) # No Trigger flag
    #awg.ch2.configure_trigger_source(1024) # No Trigger flag
    awg.ch1.configure_trigger_source(1|1026|1028|1032|1040) # any hardware trigger input
    awg.trigger_threshold_A(0.7)

# triggered by hardware trigger 1 of awg #1 -- this must be set after switching to burst mode
for awg in awgs[:1]:
    awg.trigger_threshold_A(1.)
    awg.m4.source('Hardware Trigger 1')
```
