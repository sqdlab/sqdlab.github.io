# HAL - Hardware Abstraction Layer

It main purpose is to make juypter notebooks device agnostic and hence device driver command agnostic. This is done by passing Qcodes drivers into generalized wrapper present in the [HAL](https://github.com/sqdlab/sqdtoolz/tree/main/sqdtoolz/HAL) folder of the package. The function of these HAL modules are used to control devices instead of Qcodes drivers directly. A direct advantage of this is change vendor of insturment such from aglient to Rohde & Schwarz, should not require any change in the jupyter notebook.   

Generalizing the type of device, there are 4 different types of HALs:   
1. **[ACQ](./hals/ACQdocs.md)** (Acqusition device).
2. **[DDG](./hals/DDGdocs.md)** (Digital Delay/Signal Generator).
3. **[WaveformAWG](./hals/WaveAWGdocs.md)** (Arbitrary Wave Generator + Pulse library).
4. **[GEN](./hals/GENdocs.md)** (other Generic devices).
   
Each HAL is directly made from package object and Qcodes driver as mentioned in [QuickStart Guide](./quickstart.md) , precisely [here](https://github.com/sqdlab/sqdlab.github.io/blob/master/documentation/sqdtoolz/user/quickstart.md#2creating--initialising-hal-objects). There can additional requirements or arguments needed by the HAL; which are described below in detail:
