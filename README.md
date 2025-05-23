
# ICA Simulation

## Step 1: Install OpenGate

Please refer to **Gate_installation** for detailed installation instructions.

> **Important**: Please install **Gate 9.2** and **Geant4 11.2.2**.

---

## Step 2: Adjust the parameters

There are 3 files you need to adjust: `main.submit`, `sources.mac`, and `phantom.mac`.

### `main.submit`

This is a script used to control multiprocessing.  
You need to modify the paths to `Gate` and `main.mac`.  
Please adjust the number of running processes based on the number of CPU cores on your computer.

---

### `sources.mac`

This is a section of code for setting up the light source.

```bash
/gate/source/xraygun/setActivity 1000000 becquerel
```

- This line sets the number of photons.
- The more photons, the clearer the image and the more distinct the contrast.
- Running with **1 million photons** takes about **one hour** and produces results that are visibly distinguishable to the naked eye.
- Running with **10 million photons** takes approximately **eight hours**.

```bash
/gate/source/xraygun/gps/centre 0 0 650.6195 mm
```

- This sets the position of the light source.
- The three parameters represent the **x**, **y**, and **z** coordinates of the source in the world coordinate system.

---

### `phantom.mac`

```bash
/gate/brain_mri/geometry/setImage mac/first.mhd
```

- This line sets the information of the phantom.

The following two lines of code indicate a 180-degree rotation around the y-axis:

```bash
/gate/brain_mri/placement/setRotationAxis 0 1 0  
/gate/brain_mri/placement/setRotationAngle 180 deg
```

Similarly, to rotate -90 degrees around the x-axis, use:

```bash
/gate/brain_mri/placement/setRotationAxis 1 0 0  
/gate/brain_mri/placement/setRotationAngle -90 deg
```
