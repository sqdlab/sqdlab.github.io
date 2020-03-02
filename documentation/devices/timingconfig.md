# Documentation for PulseConfig module.    

This is **NOT** a real instrument, but is treated as virtual instrument (added as one in qcodes). This is reponsible for timing of various instrument using pulse delay generator DG645 (thus it also uses ```DG645``` module interally to give tirggers to different instruments accoding to given timings parameters.)
      
* In lab's python script is commonly referred as ```timing```
   
___
## Creating object:

It **requires** a pulse delay generator (DG645, aka ```pulser```) object, PulseConfig object (```pc```) and FPGA object ```fpga```.   

```python
from qcodes.instrument_drivers.sqdlab.TimingConfig import TimingConfig
timing = TimingConfig(
    'timing', pulser, 
    fixed_point_get_cmd=pc.fixed_point,
    awg_duration_get_cmd=pc.pattern_length,
    acq_duration_get_cmd=lambda: 10e-9*fpga.tv_samples.get()*fpga.tv_decimation.get() if fpga.app.get() == b'tvmodev02' else None
)
```

___
## Information & Parameters:    

There are 2 key componenet (classes) for whose parameters can configured:   

* TimingConfig (main)
* TimingChannel

Internally, TimingConfig adds 4 channels (sub_modules):    
>Usually, these channels are one of the channels or channel pairs of DG645

* 'awg'
* 'acq'
* 'readout'
* 'aux1'
* 'aux2'   

For each of these channels, there are _Channel_ parameters.

There is also an option for setting reference points. There are 3 reference point possible:    

Reference point name | values
---------------------|-------
zero | 0
awg output start | awg.trigger_delay.get()
fixed point | awg output start + self.fixed_point_get_cmd()
   
___
## Following are the list of paramters that can be set for each components:   
    
### _TimingConfig_ paramters:
    
Essentials:    
   
Parameters | Additional Info | Default Value   
-----------|-----------------|--------------
clock_interval | Interval between triggers RECEIVED BY the pulse generator | 200e-9
repetition_time | Interval of the main trigger output (T0) by the pulse | (min_val = 0)

Additionally, there are few function present in this class:

* ```get_repetition_time(self):```   
Returns ```clock_interval * pulser.trigger_prescale_factor```.   
?Basically, it tells number of times pulser will send trigger per clock cycle.
    
* ```set_repetition_time(self, value):```   
The _value_ should be divisible by ```clock_interval``` .
    
* ```check_repetition_time(self):```
Returns ```True or False``` depending upon ```self.pulser.T0.duration.get() > self.repetition_time.get()```.   
That is, the trigger cycle of pulser shoud NOT exceed the repetition time.
    
* ```get_ref_points(self):```   
Returns a dictionary of all the three reference points mentioned above.
    
* ```get_timings(self):```    
Returns a dictionary of all channels with their timings.
    
* ```check_timing(self):```    
Internally checks for any inconsistency in timings for triggering various instruments.   
    
* ```update(self):```   
Push all the timing configuration to pulse delay generator (DG645).  

* ```plot(self):```   
Generate a representation of the timing configuration with matplotlib.   

#### Sample Python Script for these parameters is below:   

```python
timing.clock_interval.set(1/5e6)
timing.update()
timing.plot()
timing.repetition_time.set(36e-6)
```

### _TimingChannel_ Parameters:
    
#### Set channel to pulser channel:    
For each channel, there is the common parameter```channel``` , which can be set using:    
    
```python
timing.<channel name>_channel.set(<pulser/DG645 channel>)

# Example:
timing.acq_channel.set(pulser.AB)
```

##### Set Trigger delay:   

```python
timing.<channel name>_trigger_delay.set(<time in sec>)

# Example:
timing.awg_trigger_delay.set(310e-9)
```

##### Set Trigger duration:   

```python
timing.<channel name>_trigger_duration.set(<time in sec>)

# Example:
timing.awg_trigger_delay.set(310e-9)
```

##### Set Reference:   
This is the Time relative to which the in/output starts.
```python
# Example:
timing.aux1.reference('awg output start')
```  

* ```offset:```   
In/output time offset from reference point.

* ```trigger_holdoff:```   
Minimum time required before a trigger can be processed after in/output finishes.

**If duration is NOT zero**, then following parameter can also be used:   

* ```duration:```   
Duration of input or output.

#### General functions:    

* ```get_timings(self, ref_points):```      
Input is dictionary of absolute times of all the 3 reference points mentioned above. Return a dictionary of out_start, out_end, trig_start, trig_end.   

* ```update(self, ref_points):```   
**Push the channel timings to pulse generator (DG645)**.   

___
## Sample python script:   

**WIHTOUT Instrument Wrapper [This is will be used in future]**
```python
from qcodes.instrument_drivers.sqdlab.TimingConfig import TimingConfig
timing = TimingConfig(
    'timing', pulser, 
    fixed_point_get_cmd=pc.fixed_point,
    awg_duration_get_cmd=pc.pattern_length,
    acq_duration_get_cmd=lambda: 10e-9*fpga.tv_samples.get()*fpga.tv_decimation.get() if fpga.app.get() == b'tvmodev02' else None
)

# instrument timing for time dependendent measurements
#timing.clock_interval.set(1/10e6) # frequency on the pulser trigger input
timing.clock_interval.set(1/5e6)
timing.repetition_time.set(36e-6)

pc.pattern_length(10.1e-6)
pc.fixed_point(10e-6)
pc.separation(5e-9)

#fpga.tv_samples.set(180) # (acquisition time defaults to 4 * 10ns * samples)
timing.acq.channel.set(pulser.AB)
#fpga.trigger_acq_delay.set(10) # trigger holdoff in samples
#timing.acq.trigger_delay.set(10e-9*fpga.trigger_acq_delay.get())
timing.acq.trigger_delay.set(100e-9)
timing.acq.offset.set(0e-6)
timing.acq.trigger_duration.set(100e-9)

timing.readout.channel.set(pulser.CD)
timing.readout.trigger_duration.set(5e-6)
timing.readout.trigger_delay.set(34e-9)
timing.readout.offset.set(0)

# AWGs on T0 and EF
timing.awg.channel.set(pulser.T0)
timing.awg.trigger_delay.set(410e-9)

timing.aux1.channel.set(pulser.EF)
timing.aux1.reference('awg output start')
timing.aux1.trigger_delay(123e-9)
timing.aux1.trigger_duration.set(100e-9)

timing.aux2.channel.set(pulser.GH)
#timing.aux2.channel.set(None)
timing.aux2.trigger_duration.set(100e-9)

timing.update()
timing.plot()
```
   
**This with Instrument wrapper enable**
```python
timing = Instrument(timing, 'timing')
timing.awg_fixed_point = pc.fixed_point
timing.awg_pattern_length = pc.pattern_length
timing.readout_duration = timing.duration_readout = timing.trigger_duration_readout = timing.readout_trigger_duration

rep_time = np.round(40e-6/100e-9//8)*100e-9*8 # used to be ceil

timing.repetition_time.set(float(rep_time)) # trigger prescale must be divisible by 8 for the AWGs

samples = 2**7
fpga.tv_samples.set(samples) # this sets the measurement time (decimation * 10 ns * # samples)
print('sample time = {0} us'.format(samples*10e-9*4*1e6))

pc.set('pattern_length', np.ceil(5.1e-6*(1.25e9//8))/(1.25e9//8))
#pc.pattern_length.set(5.1e-6)
pc.fixed_point.set(5e-6)
timing.readout_duration.set(5e-6) #sets length of MW cavity pulse

# calibration parameters -> setup dependent stuff!!
timing.acq_channel.set(pulser.AB)
fpga.trigger_acq_delay.set(5) # FPGA trigger to measurement delay. Measured in samples to avoid measuring the trigger through cross-talk
timing.acq_trigger_delay.set(10e-9*fpga.trigger_acq_delay.get()) # time btw fpga trigger and fpga measure
timing.acq_offset.set(-50e-9)

timing.readout_channel.set(pulser.CD)
timing.readout_trigger_delay.set(14e-9)
timing.readout_offset.set(-50e-9 + 220e-9)

#timing.awg_channel.set('')
#timing.awg_trigger_delay.set(260e-9)

timing.awg_channel.set(pulser.T0)
timing.awg_trigger_delay.set(310e-9) # data sheet says 540ns

# timing.aux1_channel.set(pulser.EF)
# timing.aux1_offset.set(200e-9)
pulser.EF.delay.set(220e-9)
#pulser.EF_delay.set(410e-9 - 260e-9)

timing.aux2_channel.set(None)

timing.update()
fig = timing.plot()
```
