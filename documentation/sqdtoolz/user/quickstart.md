# QuickStart Guide
   
**Please read [Overview](./overview.md) and [Glossary](./glossary.md) before starting this guide.**.  

This guide is divided into 2 parts, both are required to be read inorder to run an experiment with total control.

* **PART A:** Provides general syntax and examples of object mentioned in [Overview](./overview).
* **PART B:** Provides general synatex and examples of [Processor](./hals.md) and [Waveform customization]().
   
___
# PART A
  
## Imports and creating Lab object
   
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

## Creating \& initialising HAL objects

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
<lab_obj>.HAL('<hal name>').<property_function>().parameter = <<value>>     
# or
<lab_obj>.HAL('<hal name>').parameter = <<value>>
```
   
Example:
```python
lab.HAL('DDG').get_trigger_output('AB').TrigPulseLength = 10e-9
lab.HAL('DDG').get_trigger_output('AB').TrigPolarity = 1
lab.HAL('DDG').get_trigger_output('AB').TrigPulseDelay = 0.0e-9
```
   
## Creating and using VARs:
   
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

## Creating and Using SPECs:

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
     
## Creating and adding waveforms in (PART B)
   
## Creating and binding Processor in (PART B)
   
## Creating an Experiment Config
General Syntax:
```python
<pkg_obj>.ExperimentConfiguration('<Config name>', <lab_obj>, <repetition_time>, [ <list of HAL names> ], <ACQ HAL>, 
