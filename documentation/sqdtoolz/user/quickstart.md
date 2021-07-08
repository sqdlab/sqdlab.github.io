# QuickStart Guide
   
**Please read [Overview](./overview) and [Glossary](./glossary.md) before starting this guide.**.  


Following are the steps along with general syntax and exmaple code on how to work with this stack:   

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
