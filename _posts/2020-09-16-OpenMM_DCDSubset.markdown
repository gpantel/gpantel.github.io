---
layout: single
References:
title:  "Writing a Subset of Atom Coordinates in OpenMM"
date:   2020-09-16 01:00:00 -0500
categories: computational-method
---

Often times in MD there are cases where we would like to write the coordinates of a system at a much higher frequency than is typical.  



Sometimes, we are only interested in a *smalll* subset of atoms. This might be true for enhanced sampling methods like Umbrella Sampling, where we might want to write *one* or a *few* coordinates every 1 ps or so, while we may only otherwise want to save the coordinates of the full system every 1 ns.

The DCD-format coordinate trajectory writer packaged with OpenMM provides no way to do this out of the box. However, it is pretty easy to hack it to perform this function. Here I'll briefly talk about how that can be done, and I will provide the two necessary modified scripts.

In OpenMM the DCDReporter is in wrappers/python/simtk/openmm/app/dcdreporter.py (simtk.openmm.app.DCDReporter). Normally, this is added to a list of "data reporters" in OpenMM belonging to a simulation, like this.

```python
simulation.reporters.append(app.DCDReporter('trajectory.dcd', writing_interval))
```

Where *writing_interval* is an integer that specifies how many time steps between which the full system coordinates are written to 'trajectory.dcd'. The DCDReporter uses the DCDFile class to actually format and write the DCD file. DCDFile is in wrappers/python/simtk/openmm/app/dcdfile.py.



To make a new DCDReporter that writes the coordinates for just a subset of atoms in the system, let's say the list is called *indices_list*, we will add this list as a new input to DCDReporter. We will also need to very slightly modify DCDFile. Let's call these new scripts (and corresponding classes) dcdsubsetreporter.py (DCDSubsetReporter) and dcdsubsetfile.py (DCDSubsetFile).

First, we'll make DCDSubsetFile, since DCDSubsetReporter will end up calling this. The *only* thing we need to do here is remove an exception that is raised if DCDFile is not writing a file with the same number of atoms as the system topology.

```python
#if len(list(self._topology.atoms())) != len(positions):
#    raise ValueError('The number of positions must match the number of atoms')
```

And we'll also rename the class just to differentiate it from the DCDFile class...

```python
class DCDSubsetFile(object):
```



That's it. Now we'll do the real work in making dcdsubsetreporter.py. First, we'll import DCDSubsetFile and numpy...

```python
from dcdsubsetfile import DCDSubsetFile
import numpy as np
```

Next we'll rename the class to DCDSubsetReporter and add *indices_list* as a new input to the class...

```python
class DCDSubsetReporter(object):
    ...
    def __init__(self, file, reportInterval, indices_list, append=False, enforcePeriodicBox=None):
    ...
    self._reportInterval = reportInterval
```

And, finally, we'll only pass the subset of atom indices defined in *indices_list* to DCDSubsetReporter...

```python
if self._dcd is None:
    self._dcd = DCDSubsetFile(
        self._out, simulation.topology, simulation.integrator.getStepSize(),
        simulation.currentStep, self._reportInterval, self._append
        )
self._dcd.writeModel(nanometer*np.array([np.array(state.getPositions(asNumpy=True)[x]) for x in self._indices_list]), periodicBoxVectors=state.getPeriodicBoxVectors())
```

That's it!



So, to use this in a typical OpenMM simulation, we would just import DCDSubsetReporter from dcdsubsetreporter.py, and have dcdsubsetfile.py present in the same directory, using it like so.

```python
from dcdsubsetreporter import DCDSubsetReporter
simulation.reporters.append(DCDSubsetReporter('trajectory_subset.dcd', subset_writing_interval, indices_list))
```



I've placed dcdsubsetfily.py and dcdsubsetreporter.py at [https://github.com/gpantel/MD_methods-and-analysis/tree/master/OpenMM_DCDSubset].