# GEN Generic devices

This HAL type includes various device which are required to conducted the experiment and do not fall into any of other category. Generally, these instruments have simple configuration, and very limited number or parameters are changed during the experiment. Currently we have the following generic insturments:   
   
* Attenuator (Vaunix).
* Microwave sources (R&S).
* Switch (custom rpi based).
* Voltage source (IVVI, SIMRACK).  

Following is the list of functions or parameters:
___
# Attenuator

### Binding to lab:
Bind Qcodes driver to Lab object.
```python
stz.GENatten('rf_atten', lab, ['VaunixAtten', 'CH1'])
```
   
### Attenuation Value
Set or get Attenuation value in **dB**.   
```python
lab.HAL('rf_atten').Attenuation = 12
```
   
___
# Microwave Sources
   
### Binding to lab:
Bind Qcodes driver to Lab object.
```python
stz.GENmwSource("MW_cav", lab, 'src_sgs2', 'RFOUT')
```
   
### Output state:
Turn the output ON (True) or OFF (False).
```python
lab.HAL("MW_dnc").Output = True
```

### Power:
Set or get the power of source in **dB**.
```python
lab.HAL("MW_cav").Power = -35
```
   
### Frequency:
Set or get the frequency of the souce in **Hz**.
```python
lab.HAL("MW_cav").Frequency = 1e6
```
   
### Phase:
Set or get the starting phase of the souce in **radians**.
```python
lab.HAL("MW_cav").Phase = 3.14
```
   
### Mode:
Set the output mode of the source. It can be ```Continuous``` or ```PulseModulated```.
```
python
lab.HAL("MW_cav").Mode = "Continuous"
```
   
### Trigger Source:
Set or get trigger source, which is a _trigger Object_ type.
```python
lab.HAL("MW_cav").set_trigger_source(<trigger object type>)
```
   
### Manual Activation(!!):
Experiment Configuration can automatically turn insturments "ON" and "OFF" based on when they are needed. If you would like to **Disable** it, set this property to True.
```python
lab.HAL('MW_CLK').ManualActivation = True
```
   
___
# Switch
   
### Binding to lab:
Bind Qcodes driver to Lab object.
```python
stz.GENswitch('sw_radial_rt', lab, 'sw_rpi_radial_rt')
```
   
### Position:
Set of get the position.
```python
lab.HAL('sw_radial_rt').Position = 'P2'
```
   
___
# Voltage Source
   
### Binding to lab:
Bind Qcodes driver to Lab object.
```python
stz.GENvoltSource('vFlux1', lab, ['sim_rack928','CH3'])
```
   
### Output state
Turn the output ON (True) or OFF (False).
```python
lab.HAL("vFlux1").Output = True
```
   
### Voltage:
Set or get the output voltage.
```python
lab.HAL("vFlux1").Voltage = 1.0
```
   
### RampRate:
Set or get the rate of change of voltage in **Volts/second**.
```python
lab.HAL("vFlux1").RampRate = 0.01
```

### Manual Activation(!!):
Experiment Configuration can automatically turn insturments "ON" and "OFF" based on when they are needed. If you would like to **Disable** it, set this property to True.
```python
lab.HAL("vFlux1").ManualActivation = True
```
___

This completes all the available Generic HAl device. It is recommended to read AWGWavefrom section now.
