# HAL - Hardware Abstraction Layer

Its main purpose is to make juypter notebooks device agnostic and hence device driver's command agnostic. This is done by passing the Qcodes drivers into generalized wrapper present in the [HAL](https://github.com/sqdlab/sqdtoolz/tree/main/sqdtoolz/HAL) folder of the package. The function in these HAL modules are used to control devices instead of Qcodes drivers. A direct advantage of this is that change of vendor of insturments such from Aglient to Rohde & Schwarz, should not require any change in the jupyter notebook.   

Overall , there are 4 different type of HALs:    
1. **[ACQ](./hals/ACQdocs.md)** (Acqusition device).
2. **[DDG](./hals/DDGdocs.md)** (Digital Delay/Signal Generator).
3. **[WaveformAWG](./hals/WaveAWGdocs.md)** (Arbitrary Wave Generator + Pulse library).
4. **[GEN](./hals/GENdocs.md)** (other Generic devices).
   
The ```GEN``` type is more of a tempelate, and has been inherited and modified for other devices which do not fit the remaining category, such as switch, voltage sources etc.   

Each HAL object is directly made from package object and Qcodes driver as mentioned in [QuickStart Guide](./quickstart.md) , precisely [here](https://github.com/sqdlab/sqdlab.github.io/blob/master/documentation/sqdtoolz/user/quickstart.md#2creating--initialising-hal-objects). There can additional requirements or arguments needed by the HAL which are described in detail at their respective pages along with the functions and parameters they provide.
   
___

This completes the basic description about HALs. Inorder to customize and use their functions it is recommended to read each of pages.
