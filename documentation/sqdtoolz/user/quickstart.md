# QuickStart Guide
   
**Please read [Overview](./overview.md) and [Glossary](./glossary.md) before starting this guide.**.  

This guide is divided into 2 parts, both are required to be read inorder to perform an experiment.

* **PART A:** Provides general syntax and examples of object mentioned in [Overview](./overview).
* **PART B:** Provides general synatex and examples of [Processor](./hals.md) and [Waveform customization]().
   
___
# PART A
  
## 1.Imports and creating Lab object
   
#### A.Import and create package object:
General syntax:   
```python
import sqdtoolz as <pkg_obj>
import numpy as np
```
   
Example:
```python
import sqdtoolz as stz
import numpy as np
```
   
#### B. Create Lab object:
General syntax:
```python
<lab_obj> = <pkg_obj>.Laboratory(instr_config_file = "<yaml filename>", save_dir = "<path to dir in which data is saved>")
```
   
Example:
```python
lab = stz.Laboratory(instr_config_file = "eth_test2.yaml", save_dir = "Z:\\Data\\sqdtoolz_test\\")
```

## 2.Creating \& initialising HAL objects

**NOTE: To know more about different type of HALs & their customisations read [HAL section](./hals.md)**.
   
#### A. Load instruments from YAML using lab object:
General Syntax:
```python
<lab_obj>.load_instruments('<yaml name of insturment>')
```
   
Example:
```python
lab.load_instrument('pulser')
```

#### B. Use package Object to create HAL object:
General Syntax:
```python
<pkg_obj>.<HAL type>("<hal_obj_name">, <lab_obj>, "<yaml_name>")
```
   
Example:
```python
stz.DDG("DDG", lab, 'pulser')
```

#### C. Set HAL object parameters (OPTIONAL):
General Syntax:
```python
<lab_obj>.HAL('<hal name>').<sub_obj_function>().parameter = <<value>>     
# or
<lab_obj>.HAL('<hal name>').parameter = <<value>>
```
   
Example:
```python
lab.HAL('DDG').get_trigger_output('AB').TrigPulseLength = 10e-9
lab.HAL('sw_radial_rt').Position = 'P2'
```
   
## 3.Creating and using VARs:
   
#### A. Create VARs using package object and register to Lab:
General syntax:
```python
<pkg_obj>.<variable_type>('var_name', <lab_obj>, <additional arugments>)
```
   
Example:
```python
stz.VariableProperty('MWFreq', lab, lab.HAL("MW_cav"), 'Frequency')
stz.VariableProperty('MWDownConv', lab, lab.HAL("MW_dnc"), 'Frequency')
stz.VariableSpaced('CavityFreq', lab, 'MWFreq', 'MWDownConv', 25e6)
```

#### B. Using VARs to set HAL object parameters:
General syntax:
```python
<lab_obj>.VAR('<var_name>').Value = <<value>>
```
   
Example:
```python
lab.VAR('CavityFreq').Value = 7636827765.17
```

## 4.Creating and Using SPECs:

#### A1. Creating SPEC using JSON:
General Syntax:
```python
<pkg_obj>.ExperimentSpecification('<spec_name>', <lab_obj>, '<JSON_name>')
```

Example:
```python
stz.ExperimentSpecification('CavitySpec', lab, 'Cavity')
```

#### A2. Creating SPEC _WITHOUT_ JSON:
General Syntax:
```python
<pkg_obj>.ExperimentSpecification('<spec_name>', <lab_obj>)
```

Example:
```python
stz.ExperimentSpecification('cav2d',lab)
```

#### B1. Using SPEC to bind to destination:
   
The destination can be anything; a var, hal, waveform or any other **object property** registered with lab object.   
   
General Syntax:
```python
<lab_obj>.SPEC('<spec_name>').set_destination('<SPEC's_parameter_name>', <lab_obj>.<VAR/HAL/WAVeform_name>, <VAR/HAL/WAVeform's_parameter_name>)
```
   
Example:
```python
lab.SPEC('Cavity').set_destination('Frequency', lab.VAR('CavityFreq'))
lab.SPEC('Cavity').set_destination('Power', lab.HAL("MW_cav"), 'Power')
lab.SPEC('Cavity')['Power'].Value = -30
```
     
## 5.Creating and binding Processor in (PART B)
   
## 6.Creating and adding waveforms in (PART B)
   
## 7.Creating an Experiment Config
General Syntax:
```python
<pkg_obj>.ExperimentConfiguration('<Config name>', <lab_obj>, <repetition_time>, [ <list of HAL names> ], <ACQ HAL>, [ <list of SPECs names> ])
```
   
Example:
```python
stz.ExperimentConfiguration('contMeasQ', lab, 1.1e-3, ['DDG', 'WfmCon', 'MW_cav', 'MW_dnc', 'MW_qubit', 'sw_radial_rt'], 'DigiC', ['Cavity', 'Qubit1'])
```

## 8.Creating and Runnning PRE-BUILT Experiment

#### A.Importing pre-built experiment
General Synatax:
```python
from sqdtoolz.Experiments.<folder>.<experiment_module> import *
```
   
Example:
```python
from sqdtoolz.Experiments.Experimental.ExpPeakScouterIQ import*
from sqdtoolz.Experiments.Experimental.ExpRabi import*
```
   
#### B. Creating experiment _Object_:
General Syntax:
```python
exp_obj = <Exp_CLASS_name>("<exp_name>", <lab_obj>.CONFIG('<config_name>', <additional parameters>)
```
   
Example:
```python
new_exp = ExpRabi("RabiAuto", lab.CONFIG('RabiAuto'), lab.WFMT('QubitFreqGE'), np.linspace(0.0,0.5,100), param_rabi_frequency=lab.SPEC('Qubit1')['Rabi Amplitude Frequency'], param_rabi_decay_time=lab.VAR('Qubit Decay Rate'))
```
   
####  C. Running experiment using lab object:
General Syntax:
```python
data_variable = <lab_obj>.run_single(<exp_obj>)
```
   
Example:
```python
leData = lab.run_single(new_exp)
```
   
**This completes the PART A of quick start guide. Part B provides controls on programming awg and processing data, with a lot of customisation, which are mentioned in the their respective sub-files.**
   
___
# PART B

This is advanced section because it assumes you have some basic knowledge of stack and have read the HAL types and other details.    

## 5.Creating and binding Processor
     
**NOTE: For more customization please read [Processor Section](./misc/PROCdocs.md)**
    
#### A. Create Processor object:
General Syntax (NOTE: CPU can be replaced by GPU)
```python
<proc_obj> = <pkg_obj>.ProcessorCPU('<proc_name>', <lab_obj>)
```
   
Example:
```python
myProc = stz.ProcessorCPU('ddcInteg', lab)
```
   
#### B. Reset pipeline & Add processing stage:
This section require user to read [Processor subsection](./misc/PROCdocs.md) in miscellaeous section.
   
#### C. Binding Processor to ACQ HAL:
General Syntax:
```python
<lab_obj>.HAL('<ACQ_obj_name>').set_data_processor(<<proc_obj>)
```
   
Example:
```python
lab.HAL("DigiC").set_data_processor(myProc)
```
   
## 6.Creating and adding waveforms

**NOTE: To know more about different type of waveforms & their customisations read [WaveformAWG section]()**.

#### A. Creating AWGwaveform object:
General Syntax:
```python
<pkg_obj>.WaveformAWG("<AWG_obj_name>", <lab_obj>, [ ('<AWG_YAML_name>', 'ch<channel_number>'), (<similar tuple for more channels/AWG>) ], <sampling_rate>, <repetition_time>)
```
   
Example:
```python
stz.WaveformAWG("WfmCon", lab, [('Agi1', 'ch1'), ('Agi1', 'ch2')], 1.25e9, total_time = 1024e-9)
```


