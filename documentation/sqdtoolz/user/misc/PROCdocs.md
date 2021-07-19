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

## 1. Creating Processor object:
    
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
Example:
```python
import 
Stage
General Syntax:
```python
<proc_obj>.add_stage( <pkg_obj>.
___
**List of Processing stages in CPU:**
   
* DDC
* ESD (or PSD Power Spectral Density) )
* FFT 
* FIR
* Integrate
* Max
* Mean
   
___
**List of Processing stages in GPU:**
   
* DDC
* FIR
* Integrate
* Max
* Mean
___
