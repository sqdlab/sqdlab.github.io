# Processor

This is data processing framework (python files/modules), is built as semi-independent part of the SQDToolz stack. Internally it uses ```Numpy``` and ```Scipy``` for processing data on CPU and ```Cupy``` for processing data via NVIDIA-CUDA based GPU. The input data follows a particular format as defined in [Data format]() guide. This data format is a template for most data acqusition devices such as FGPA, Digitizer, but can also be modified for other acqusition device such as VNA.
   
Way to create processor and pass them to ACQ Hal is already presented in [Quick Start Guide](../quickstart.md)
