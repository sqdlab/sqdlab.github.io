# Stack Overview

This document presents all the layers  and elements of SQDToolz, and explain the design philosophy behind them. **It is important** to read this document to get an idea of how to use these different layers/ elements of stack correctly. **NOT using them in this way can lead to unexpected bugs and behavior!!**, not support is available for this.
   
   
___
## Layers:
   
The is divided into 2 key layers:

1. HAL (Hardware abstraction layer).
2. RL (Runtime layer).
3. IL (Interface layer).

Following are the elements and use case for each layer:   

___
### HAL (Hardware abstraction layer)

This layer had 2 purpose:

* **Qcodes driver wrapper:**.  
Previously, bug fixing in qcodes driver has lead to command syntax changes, making the existing notebooks un-useable, **this wrapper provides it own common functions** which are internally implemented by qcodes driver. Hence any change in the internal naming scheme to qcodes driver will not affect the notebook, maintaining compatibility.
   
* **Device abstraction:**.   
Jupyter notebook should not be directly dependent on the device or rack being used. This layer makes it possible to "rack/device" independent.   

**NOTE:** Currently, this is a **wrapper around qcodes driver**. This is **only** part of the stack which dependency on qcodes library, so if you see an qcodes related error, it must have originated here. 
    
____
### RL (Runtime Layer)  

This layer is the main component being used when performing different experiments. This layer has the following functionality:

* HAL objects storage.
* File I/o of data.
* experiment parameters storage.

* **
