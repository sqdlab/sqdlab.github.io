# DDG Digital Delay Generator

This HAL type belong to the Digital Delay generator (Previously known as pulser). Currently we have the following DDGs:   

* DG645
   
Primary purpose of these devices is to send trigger pulse to different instruments. These are important for timing control. Since its only function is to generate trigger pulse at correct times, this makes it the simplest HAL type in the stack. It provides the following functions/Parameters:   

___

## List of parameters/functions:
   
### Repetition time:
This is the total time of all the entire experiment (all the segements). This can be get or set.
```python
# Set repetition time
lab.HAL("DDG").RepetitionTime = 1e-6

# Get repetition time
lab.HAL("DDG").RepetitionTime
```
   
### Channel & configuration:
These are channels of the DDG are obtained and/or configured as a seperate python object of type _trigger object_. This object is sent as an argument to some other HAL type as trigger source. 
   
### Accessing the channel and/or generate object:   
```python
lab.HAL("DDG").get_trigger_output('AB')
```
   
### Pulse length:   
Set trigger pulse length
```python
lab.HAL("DDG").get_trigger_output('AB').TrigPulseLength = 100e-9
```
   
### Pulse polarity:
Set pulse polarity (1=positive)
```python
lab.HAL("DDG").get_trigger_output('AB').TrigPolarity = 1
```
   
### Pulse Delay:
Set delay before the send the pulse
```python
lab.HAL("DDG").get_trigger_output('AB').TrigPulseDelay = 0.0e-9
```
   
___
   
This completes all the parameters and functions available for customizing triggering of different devices. It is recommended to checkout the trigger objects section to get a better understanding of usage, functionality and philosophy behind them.
