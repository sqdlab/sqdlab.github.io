# Documentation for PulseConfig module.    

This is **NOT** a real instrument, but is treated as virtual instrument (added as one in qcodes). This is used to describe various parameters of the microwave pulse.   

___
## Creating object:

It **requires** a pulse delay generator (DG645, aka ```pulser```) object, PulseConfig object (```pc```) and FPGA object ```fpga```.   

```python
from qcodes.instrument_drivers.sqdlab.PulseConfig import PulseConfig
pulse_config = pc = PulseConfig('pulse_config')
```
___
## Information & Parameters:    

There are 3 key componenet (classes) for whose parameters can configured:   

* PulseConfig (main)
* Channel
* ChannelPair

Additionally, storing info about AWG is also done within the main class.   

**NOTE** Please read additional info (at the end) about how it is being used in existing software stack, in  you want to modify or it.

___
## Following are the list of paramters that can be set for each components:   

### PulseConfig Parameters:   
   
Essentials:    

Parameters | Additional Info
-----------|----------------
pattern_length | (seconds)
fixed_point | (seconds)
low | (default: -1)
high | (default: 1)
seperation | (time between 2 consecutive pulses) (seconds)   

Optionals (Only works when channel parameter "optimal control" is True):      

Parameters | Additional Info
-----------|----------------
sampling_rate_multiplier | (default: 10) (The scaling between the sampling rate of vector `x` and vector `y`. The wanted pulse shape `y` at the output of the linear device is sampled with a higher sampling rate to receive more accurate fits.)   
prepend | (The pulse is zero padded before the pulse for `prepend` seconds.)
append | (The pulse is zero padded after the pulse for `append` seconds.)
use_pinv | (pseudo-inverse) (bool)
ir_tlim | (start and stop time of impulse reponse used for matrix inversion)
    
The above mentioned paramters can be directly set. For eg:

```python

pc.pattern_length(10.1e-6)
pc.fixed_point(10e-6)
pc.separation(5e-9)
```

Additional info:   

There are 3 functions in this class:

* get_config()
* get_channel_config()
* get_channel_pair_config()

___
### Channel Parameters:
    
>All the parameters of pulseconfig can also be set for each channel also!
    
Additional parameters are listed below:

Parameters | Additional Info
-----------|----------------
channel_type | (it can be single or mixer)
sampling_rate | (Hz) (default)
delay_m0 or delay_m1 | (**marker** output delay)
use_optimal_control | (bool)
impulse_response | (path to file containing measured impulse response of awg channel)
    
**Note**: There are FIR filter implemented in the module, but that is done using pulsegen module. That should NOT be used, hence they are exempted from this documentation. Please use awg's inbuilt FIR filter implemented using reikna library, for faster processing using GPU.

A sample script for setting parameters:

```python
pc.sampling_rate_ch0.set(1.25e9)
```

___
### ChannelPair Parameters:

>All the parameters of pulseconfig can also be set for each channelpair also!
   
Additional parameters are listed below:

Parameters | Additional Info
-----------|----------------
mixer_power | (dBm , default=16)
sigma | (seconds, default 2e-8)
truncate | (truncated gaussian pulse, default=5)
qscale | (Quadrature Scale factor, default=0.5)
anharmonicity | (rad/s, default=2 * pi * 300e6)
length | (seconds, default = 20e-9)
pulse_shape | (default='GaussianPulse')
pi_amp | (amplitude of pi pulse created by pix() and piy())
pi_half_amp | (amp of pi/2 pulse created by pihalfx() and pihalfy())
mixer_calibration | (matrix)
if_freq | (rad/s)

Following are the type of pulse or 'pulse_shape' possible (extracted from _pulsegen_):

* ZeroPulse
* SquarePulse
* GaussianPulse
* ArbitraryPulse
* DRAGGaussPulse

A sample script for setting parameters:

```python
#Pulse Configuration 
pc.set('truncate_chpair0', 2)
pc.set('truncate_chpair1', 2)
#pc.set('truncate_chpair2', 2)
pc.set('sigma_chpair0', 32e-9 / 4.)
pc.set('sigma_chpair1', 32e-9 / 4.)
#pc.set('sigma_chpair2', 20e-9 / 4.)
pc.pulse_shape_chpair0.set('GaussianPulse')
pc.pulse_shape_chpair1.set('SquarePulse')

pc.if_freq_chpair0
pc.get('mixer_power_chpair0')
pc.get('truncate_chpair{0}'.format(ge_chpair))
pc.get('sigma_chpair{0}'.format(ge_chpair))
pc.get('pi_amp_chpair{0}'.format(ge_chpair))
```

___
## AWG functions: 

**NOTE**: Information about AWG, along with information about pulse is passed onto **UQ tools** function which are actually responsible for deployment & control of AWG at the backend.

Following are the functions for awgs:

* add_awg()
* get_awg()
* remove_awg()

A sample script for setting parameters:

```python
# set AWG
pc.add_awg(awg, 'Agilent_N824x', 1.25e9)
# get AWG
pc.get_awgs()
```

___
## Additional Information:    
    
Few points to keep in consideration ,while understanding the working of this module with the rest of the software stack:
    
* Pulseconfig is ONLY used for generating and keeping pulse configuration. It was meant to be a upgrade to ```pulsegen``` module, _with which it interfaces internally_, to generate the pulse configuration.  

* It does NOT drive the AWGs, that is done by _UQ tools_ library's ```programAWG``` function written in ```AWG`` module.

* The information passed to object of this library are: 1. Pulse Configuration, 2. AWG., both which are later on passed to ```TimingConfig``` modules, which internally passes the information to ```AWG``` module for triggering of AWG, with given pulsen configuration.
