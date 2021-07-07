# Stack Overview

This document presents all the layers  and elements of SQDToolz, and explain the design philosophy behind them. **It is important** to read this document to get an idea of how to use these different layers/ elements of stack correctly. **NOT using them in this way can lead to unexpected bugs and behavior!!**, no support is available for this.
   
   
___
## Layers:
   
The is divided into 2 key layers:

1. **HAL (Hardware abstraction layer)**
   * Qcodes wrapper, and RL linking.
2. **RL (Runtime layer)**
   * VARs, SPECs.
3. **IL (Interface layer)**
   * Experiment, Experiment Config.

Following are the elements and use case for each layer:   

___
## HAL (Hardware abstraction layer)

This layer had 2 purpose:

* **Qcodes driver wrapper:**.  
Previously, bug fixing in qcodes driver has lead to command syntax changes, making the existing notebooks un-useable, **this wrapper provides it own common functions** which are internally implemented by qcodes driver. Hence any change in the internal naming scheme to qcodes driver will not affect the notebook, maintaining compatibility.
   
* **Device abstraction:**.   
Jupyter notebook should not be directly dependent on the device or rack being used. This layer makes it possible to "rack/device" independent.   
   
Steps to creating a HAL object:

1. _Load Qcodes driver using RL object_. This is done by running the ```load_instrument()``` function of the RL object.
2. _Use package object to create a HAL type object and link it to RL object and it's qcodes driver_. This is done by ```<pkg_obj>.<hal_type>(<hal_obj_name>,<rl_obj>,<qcodes_name>,<additional_params>)```. While linking, the HAL will register itself as an \"active\" instrument.
* 
**NOTE:** Currently, this is a **wrapper around qcodes driver**. This is **only** part of the stack which dependency on qcodes library, so if you see an qcodes related error, it must have originated here. 
    
____
## RL (Runtime Layer)  

This layer is the main component being used when performing different experiments. This layer has the following functionality:

* HAL objects storage.
* File I/o of data.
* Experiment parameters storage (SPEC and VAR).

File saving directory is passed as an argument when creating the RL object. The HAL object storage at the beginning **only once**.   

Experiment parameters are the most important part of this layer, as they are the container that hold various type of information and data _between_ different experiment. Following are the 2 type of RL containers:

1. VAR
2. SPEC
   
**NOTE:** Their functionality looks very similar on the surface, but their' way intended way of usage _is very different_. Using them in improper manner can lead to data loss, data inconsistency or other unexpected behavior.   
   
___
### VARs (Variable property):

Instead of changing HAL parameters direclty, they should be linked to VARs, and these **VARs should be ajusted** instead of direct changes to HAL parameters with tuning the them _before_ running the experiment.   

General Usage:

* Created ones.
* Set, get or swept.
* Use over differnet experiments and experiment configurations.

These are placeholder which _may or may NOT_ belong to HAL object parameter. Ideally, you can create and add custom variable parameters or use existing ones. For example:

* **Combine HAL object parameters**, to adjust in a particular way.    
Eg, "VariableSpaced" take cavity src and down conversion src, along with the offset "25MHz" to automatically keep them them 25MHz off from each other.
   
**NOTE** the following for HAL object related VARs:

* They only retain the last value set to them **during the runtime**. This means, in a sweep, whatever is the last value of sweep is kept. 
* In a **chained experiments**, if the previous experiment updates a values, that is maintained, **the value you set will be lost!!**.
    
____
### SPECs (Specifications):

These can be thought of as "tempelate" or "collection" of information and parameters relevant to ths given experiment, or obtained after running the experiment or after processing the data obtained from it. Additionally it provide a certain level of **automation, using the JSON templates.**

General usage:

* Created on the fly **or** using JSON scripts.
* Assign values, at execution time, or post processing.
* Use over different experiment and experiment config.
     
Additionally, they have the following features:   

* Saved in JSON file, readable via notepad.
* Same JSON file can be used to initialize different specification.
* JSON file based initializations CAN be edited on the fly and words with advance features like ```load_last_config()```.
* Linked to HAL object properties via "destination" argument.
   
Example: Creating base.json

Consider a case of Cavity and qubit spectroscopy. From each of them we obtain a frequency, power and decay time. You can have JSON with name "base" in the ```sqdtoolz/sqdtoolz/ExperimentSpecifications/``` folder as follows

```
{
   "Frequency" : 5e9,
   "Power" : -30,
   "decay time": 1
}
```

This JSON file can be used to create SPEC('Qubit') and SPEC('cavity'), in which later more parameters can be added specific to their own details. At the time of creation of SPECs, all the paramters mentioned in the JSON as automatically created and loaded with deafault values mentioned in the JSON file.
   
___
## IL (Interface layer).  

This layer where the runtime parameters are actually executed, that is, the programming of the instruments, timing checks, data collection and data processing (depending upon the experiment object), takes place in this layer. This comprises of the following 2 key elements (classes):

1. Experiment Configuration.
2. Experiment.

Following are the functions of each component in detail:
   

### Experiment Configuration:   

This takes the following inputs:

* Configuration name
* lab object
* repetition time
* HAL objects
* VARs
* SPECs
* Processor
   
Basically it acts as a giant container for all the details relevant to the experiment it will be run in. Essentially, it contains functions for controlling insturments, **HOWEVER** these are not called by the user, everything inside it is automated!   
   

### Experiment:

This can be thought of as an intelligent Experiment config mamnger. It uses the built-in functions in a particular way ( as defined by the user) to:

* prepare instrument.
* save data
* perform sweeps
* automatically turn insturments ON \& OFF acting like a smart context manager.
   
**Creation:** These are supposed to be created by the user, when some particular insturments are handle in a specific way to get data, and for this sequence **no** other experiemnt exists.
   
___

This completed the Design and Use Philosophy of the entire stack.
