# Processor

This is data processing framework (python files/modules), built as semi-independent part of the SQDToolz stack. Internally it uses ```Numpy``` and ```Scipy``` for processing data on CPU and ```Cupy``` based on NVIDIA-CUDA for gpu processing. The input data follows a particular format as defined in [Data format]() guide. 
  
Data processing follows pipeline processing fashion and overall processor is to used as follows:
   
1. Create Processor Object using package object.
2. Reset/Clear processor pipeline (Processing that needs to be done).
3. Add **processing stages** in the order of how data should be processed.
4. Explicity add the last stage.
5. Pass the processor object to ACQ hal.
      
Remainder of this Document has the following outline:
   
* Detailed guide on creating, customising and binding processor objects.
* List of Processing stages for CPU and GPU.
* Customisation for each stage

## 1. Create Processor object:
    
[Quick Start Guide](../quickstart.md#5creating-and-binding-processor) also contains steps to creating processor object and binding it to lab.   
   
General syntax:
```python
<proc_obj> = <pkg_obj>.ProcessorCPU('<proc_name>', <lab_obj>)
```
   
Example:
```python
myProc = stz.ProcessorCPU('ddcInteg', lab)
```

___
## 2. Reset the pipline:

It is a good idea to reset the processor object by using this function so it does not retain any previous processing stage from previous runs.
   
General Syntax
```python
<proc_obj>.reset_pipeline()
```
   
Example:
```python
myProc.reset_pipeline()
```
   
___
## 3. Add Processing Stages:

These stage are python modules located at : ```sqdtoolz/sqdtoolz/HAL/Processors/Processor-X```, where X is CPU or GPU depending on the device you would like to use. **WARNING(!): These stage CANNOT be inter-mixed.** CPU Processor, requires adding **ONLY** CPU processing stage, using it with a GPU processor might _cause the notebook or system CRASH_. 

    
#### A. Import the required stages:
General Syntax
```python
from sqdtoolz.HAL.Processors.<proc_type>.<proc_type>_<stage_name> import*
```
   
Example:
```python
from sqdtoolz.HAL.Processors.CPU.CPU_DDC import*
from sqdtoolz.HAL.Processors.CPU.CPU_FIR import*
from sqdtoolz.HAL.Processors.CPU.CPU_Mean import*
```
   
#### B. Adding the stages:
General Syntax:
```python
<proc_obj>.add_stage( <pkg_obj>.<proc_type>_<stage_name>( <stage_params> ) )
```
   
Example:
```python
myProc.add_stage(stz.CPU_DDC([25e6]))
myProc.add_stage(stz.CPU_FIR([{'Type' : 'low', 'Taps' : 128, 'fc' : 5e6, 'Win' : 'hamming'}]*2))
myProc.add_stage(stz.CPU_Mean('sample'))
myProc.add_stage(stz.CPU_Mean('segment'))
```
   
## 4. Add end stage:
This is nothing special ,but demarkates the end of processing pipeline, hence is added via a different function as follows:
   
General Syntax:
```python
<proc_obj>.add_stage_end( <pkg_obj>.<proc_type>_<stage_name>( <stage_params> ) )
```
   
Example:
```python
myProc.add_stage_end(stz.CPU_Mean('repetition'))
```
   
## 5. Bind processor to ACQ HAL:

The acqusition hal object, needs this processor object for processing the raw data coming from acqusition device. This object is passed as follows:   

General syntax
```python
<lab_obj>.HAL('<acq_hal_name>')..set_data_processor(<proc_obj>)
```
   
Example:
```python
lab.HAL("DigiC").set_data_processor(myProc)
```
   
___
# List of Processing stages
   
## CPU processing stages:

* DDC
* ESD (or PSD Power Spectral Density) )
* FFT 
* FIR
* Integrate
* Max
* Mean
   
## GPU processing stages:
   
* DDC
* FIR
* Integrate
* Max
* Mean
   
___
# Arguments for processing stages:
   
#### DDC
List of frequencies, corresponding to each channel of the acqusition device.
```python
stz.CPU_DDC([25e6])
```

#### ESD
Tuple of IQ indices.
```python
stz.CPU_ESD((0,1))
```

#### FFT
Tuple of IQ indices.
```python
stz.CPU_FFT((0,1))
```
   
 #### FIR
 Following are the optionals that are to passed as dictionary **per channel** (might have to create 2 list each belonging one sepeart channel:
 * Type: _low_ or _high_ pass filter.
 * Taps: number of taps to use.
 * fc: Frequency cutoff.
 * Win: Filter window.
    
```python
stz.CPU_FIR([{'Type' : 'low', 'Taps' : 128, 'fc' : 5e6, 'Win' : 'hamming'}]*2)
```
   
#### Integrate
Name of parameter accross which it sums:
```python
stz.CPU_Integrate("segments")
```
   
#### Max
Name of parameter accross which it find the max element:
```python
stz.CPU_Max("segments")
```
    
#### Mean
Name of parameter accross which to calculate the mean:
```python
CPU_Mean('segment')
```

___

This completed the Processor object documentation.
