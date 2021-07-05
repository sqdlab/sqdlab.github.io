# SQDToolz Documentation:

SQDToolz is an instrument control and data processing software stack written in python and JSON, used and developed at Superconducting Quantum Devices Lab, The University of Queensland. It is inspired and designed from the lessons learned through fixing bugs of the now defunct UQTools. The key points in the design of this stack are:

* **Less external library dependency** \- less things to fix and maintain.
* **Device abstraction** - Control scripts should not be directly affected by hardware changes or bug fixes.
* **Automation \& Chaining** - Multiple experiments should be seamlessly linked and executed.

---
## Documentations:

> For those who want to quickly start with the software stack without going into deep details about the control and data flow, please use:   
* **[User Documentation](./user/readme.md)**.  

> For those who want to understand the inner working, design choices and drive development and compatibility please use:   
* **[Developer documentation](https://github.com/sqdlab/sqdtoolz/wiki/Developer-Documentation)**.
