
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

# GATE Simulation Configuration Guide

## File Structure Overview

The project contains these key components:

- **Main directories**:
  - `/mac` - Contains all macro configuration files
  - `/model` - Stores phantom model definitions
  - `/output` - Holds all simulation output files

- **Key files**:
  - `selectAngle` - Rotation calculation utility
  - `main.mac` - Primary simulation control file
  - Other supporting source files

## Using the changeAngle Tool

Execute the rotation calculator in terminal:
​```bash
./selectAngle
```

The tool will:

1. Prompt you to enter multiple rotation axes and angles
2. Display and select six angles:
   - Ready-to-use GATE macro commands

```terminal
root@09556a7b8f38:/home/vgate/Project/chestCT# ./selectAngle 
Select one angle (enter 1–6, or 0 to exit):
1: LAO+Cranial
2: LAO+Caudal
3: RAO+Cranial
4: RAO+Caudal
5: AP Cranial
6: AP Caudal
0: Exit
Your selection: 1

Selected: [LAO+Cranial]
/gate/container/placement/setRotationAxis  0.386624 0.908227 -0.160145
/gate/container/placement/setRotationAngle 49.032471 deg
/gate/container/placement/setTranslation 0 25 -59 mm
/gate/output/imageCT/setFileName output/45LAO,20LCA

Do you want to update macro files with this angle? (y/n): n
Macro files not updated.

```

## Configuration Updates Required

### 1. Update Rotation Parameters (phantom.mac)

Locate and replace these two rotation commands in `mac/phantom.mac` with the outputs from changeAngle.

```gate
/gate/world/daughters/name container
/gate/world/daughters/insert box
/gate/container/geometry/setXLength 400 mm 
/gate/container/geometry/setYLength 400 mm
/gate/container/geometry/setZLength 156.2 mm
/gate/container/setMaterial Air

/gate/container/daughters/name Thoracic_cavity
/gate/container/daughters/insert ImageNestedParametrisedVolume
/gate/Thoracic_cavity/geometry/setImage ./model/Thoracic_cavity.mhd
/gate/Thoracic_cavity/geometry/setRangeToMaterialFile mac/attenuation_range.dat

# replace these
/gate/container/placement/setRotationAxis  0.386624 0.908227 -0.160145
/gate/container/placement/setRotationAngle 49.032471
/gate/container/placement/setTranslation 0 25 -59


/gate/Thoracic_cavity/vis/setVisible
/gate/Thoracic_cavity/attachPhantomSD
```



### 2. Configure Output Filename (output.mac)

Modify the output file naming convention:

```gate
/gate/output/imageCT/setFileName output/CustomName
```

### 3. Adjust Phantom Position (phantom.mac) -- if needed

Edit the translation parameters:

```gate
/gate/container/placement/setTranslation X Y Z mm
```

**Position Adjustment Reference**:

| Axis | Direction  | Effect                       | Notes                    |
| :--- | :--------- | :--------------------------- | :----------------------- |
| X    | - Decrease | Moves left                   | Positive X = right       |
|      | + Increase | Moves right                  |                          |
| Y    | - Decrease | Moves down                   | Positive Y = up          |
|      | + Increase | Moves up                     |                          |
| Z    | - Decrease | Appears smaller (moves down) | May exit scan range      |
|      | + Increase | Appears larger (moves up)    | May exceed field of view |

## Recommended Workflow Steps

1. **Calculate rotations**:
   - Run `./selectAngle`
   - Record the output values
2. **Update configuration**:
   - Apply new rotations in phantom.mac
   - Set a descriptive output filename
3. **Test and adjust**:
   - Run initial simulation
   - Fine-tune X/Y/Z position values
   - Repeat until satisfactory image is obtained
4. **Finalize**:
   - Save optimal configuration
   - Note final parameters for future reference
