# Documentation for RF source (SGS100A)    

This device is a microwave source, which is used ito produce a microwave at a particular frequency and power. Signal from this and AWG are input to mixer. Following is the python code used in the lab, to connect to it remotely, _given your system is **in** lab network_ .

* In lab's python script is commonly referred as ```src_sgs1``` or ```src_rs1``` or ```src_cavity``` or ```src_down```.
   
## Information & Parameters:   

User manual of this microwave source can be found [here](https://scdn.rohde-schwarz.com/ur/pws/dl_downloads/dl_common_library/dl_manuals/gb_1/s/sgs100a_1/SGS100A_UserManual_en_12.pdf).   
   

This instrument can be used in 2 output modes:   
* IQ - modulated signals.   
* Pure LO (local oscillator) in freq between (1Mhz to 12.75GHz).    

It also have ALC - Automatic Level Control, which can be used for almost all applications, especailly I/Q modulation. This is had 3 states:   
* on
* off
* auto    

There 2 modes of pulse modulation if it is enabled:    
* internal (internal pulse generator is used).    
* external (external pulse signal is used).
___
## Python Script for using SGS100A (SQD lab specific):    

### Creating the object and connecting to AWG:   

**NOTE**: Please close all connections before establishing new ones, which is done using line 2:   

```python
from qcodes.instrument_drivers.sqdlab.SGS100A import RohdeSchwarz_SGS100A
RohdeSchwarz_SGS100A.close_all() # CLOSE ALL previous connections.
src_sgs1 = RohdeSchwarz_SGS100A('src_sgs1', 'TCPIP::<IP ADDRESS>::INSTR', terminator='\n')
```

___
### Set _Reference Oscillator_ parameters:   

There can be 2 sources of oscillator with different outputs as follows:   
* Internal (_input_: 10Mhz signal by default, _output_: 10MHz, 1GHz).
* External (_input_: 10MHz, 13MHz, 100MHz, 1GHz, _output_: < input freq > or 1GHz) .   


#### Set reference oscillator:    
This can be internal or external.   

```python
src_sgs.ref_osc_source.set('EXT')
```

#### Set reference oscillator input frequency:    

```python
src_sgs.ref_osc_external_freq.set('10MHz' if src_sgs==src_sgs1 else '1000MHz')
```

#### Set reference oscillator output frequency:

```python
src_sgs.ref_osc_output_freq.set('1000MHz')
```

___
### Set _General_ device paramters:    

#### Set Power:    
The units are in dBm.    

```python
src_sgs4.power.set(0)
```

#### Set frequency:   
The units are in Hz.   

```python
src_sgs4.frequency.set(1250e6)
```

#### Set phase:   
The units are in degrees.   

```python
src_sgs4.phase.set(45)
```

#### Set ALC:   

```python
src_sgs4.alc.set('off')
```

___
### Set _Pulse modulation_ parameter.
This is optional, required if enabled.   

#### Enable pulse modulation:    

```python
src_cavity.pulsemod_state.set('on')
```

#### Set pulse modulation source:
This can be internal or external.    

```python
src_cavity.pulsemod_source.set('EXT')
```

___
## Sample python Script:   
A sample python configuration script is attached below:  
   
```python
# R&S microwave generators
# from qcodes.instrument_drivers.rohde_schwarz.SGS100A import RohdeSchwarz_SGS100A
from qcodes.instrument_drivers.sqdlab.SGS100A import RohdeSchwarz_SGS100A
RohdeSchwarz_SGS100A.close_all()
src_sgs1 = RohdeSchwarz_SGS100A('src_sgs1', 'TCPIP::192.168.1.115::INSTR', terminator='\n')
src_sgs2 = RohdeSchwarz_SGS100A('src_sgs2', 'TCPIP::192.168.1.116::INSTR', terminator='\n')
src_sgs3 = RohdeSchwarz_SGS100A('src_sgs3', 'TCPIP::192.168.1.117::INSTR', terminator='\n')
src_sgs4 = RohdeSchwarz_SGS100A('src_sgs4', 'TCPIP::192.168.1.118::INSTR', terminator='\n')
src_sgs5 = RohdeSchwarz_SGS100A('src_sgs5', 'TCPIP::192.168.1.119::INSTR', terminator='\n')
src_sgs6 = RohdeSchwarz_SGS100A('src_sgs6', 'TCPIP::192.168.1.109::INSTR', terminator='\n')

# set up reference 
for src_sgs in (src_sgs1, src_sgs2, src_sgs3, src_sgs4, src_sgs5, src_sgs6):
    src_sgs.ref_osc_source.set('EXT')
    src_sgs.ref_osc_external_freq.set('10MHz' if src_sgs==src_sgs1 else '1000MHz')
    src_sgs.ref_osc_output_freq.set('1000MHz')
    #src_sgs.ref_out_signal('REF')
    src_sgs.status('Off')

# pulse modulation
#Source for up conversion:
src_cavity = Instrument(src_sgs2, 'cavity')
src_cavity.pulsemod_state.set('on')
src_cavity.pulsemod_source.set('EXT')
src_cavity.status.set('off')
```
