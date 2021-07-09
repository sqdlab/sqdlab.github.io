# ACQ Acqusition device:

This is HAL type for data acqusition devices. For SQDLab, currently there are 3 devices:

1. M4i Spectrum Digtizer card (Digitizer).
2. ETH FPGA (fpga).
3. Tabor Digitizer (Digitizer).
4. VNA

For using any of 2 key compoenet are required:

1. Qcodes driver through YAML.   
2. Processor.   

This hal provides the following parameters:
   
___
## List of Parameters :

### Number of Samples:
Number of samples to acquire in a single run of acquistion. This can also tell for how long are you measuring: ```num.of.samples/sampling rate = time of acq```.   
```python
lab.HAL("DigiC").NumSamples = 1024
```
   
### Number of Segments:
Number of different times you want to measure in one run of the experiment.
```python
lab.HAL("DigiC").NumSegments = 1
```
   
### Number of Repetitinos:
Number of times you want to average or repeat the entire experiment.
```python
lab.HAL("DigiC").NumRepetitions = 2**9
```
   
### Sampling rate(!):
Set or get the Sampling rate of the device. **NOT recommended to change this setting unless you know about the device in detail.**.  
```python
lab.HAL("DigiC").SampleRate = 5e6
```

### Trigger Edge(!):
Set if the device should be trigger at the rising edge or falling edge. **NOT recommended to change this setting unless you know about the device in detail.**.  
```python
lab.HAL("DigiC").InputTriggerEdge = 1 #positive
```

### 
