# Handling the delicious output files from OpenGate

## Step 1: installing imageJ/Fiji (Fiji is just imagej)

Fiji is a packaging of imageJ with the extensions we need to handle the .dat (data) files already installed, refer to https://imagej.net/software/fiji/ for setup instructions

## Step 2: Macros

You likely also want to have some automation of image handling for cases where you are converting many data files to images. Fiji supports macros for doing this and documentation can be seen at https://imagej.net/ij/developer/macro/functions.html and https://imagej.net/scripting/macro.

Be warned that running Fiji with --headless seems to cause macros to be very unstable and break on many actions such as opening files. I recomend not running Fiji headless and simply using `run("Quit");` at the end of a macro to close Fiji.

## Step 3: Example

Below is an example macro that will open the data file passed in as an arguement to the macro i;e `fiji -macro ./myMacro.ijm '/path/to/file.dat'` where `./myMacro.ijm` is the path to a macro file 
(located in the current folder the terminal is in in this example) and /path/to/file.dat is the path to the .dat file for processing.

```
//this doesn't work with headless mode and tends to crash upon calling open()
path = getArgument();
print(path); //print path for debugging purposes

//undocumented thing 1: how to skip the format mode dialog when opening stacks even though it's handled automatically if passing the file in as an argv when not using macros
//open(path);

//via https://stackoverflow.com/questions/59904853/imagej-macro-for-automatically-opening-a-stack-then-processing-and-saving-as-tif
s = "open=["+path+"] autoscale color_mode=Grayscale rois_import=[ROI manager] view=Hyperstack stack_order=XYCZT";
run("Bio-Formats Importer", s);

//any macro functions for processing the image, such as improving contrast, noise removal, etc, go here.
//you may want to use other tools, like imagemagick or GIMP, to cleanup images after saving in a standard format as an alternative.

saveAs("/formats/jpeg", "./image.jpeg"); //supports tiff, png, and many more.
close("*"); //close all image windows

//undocumented thing 2: how to make fiji exit after macro end
run("Quit");
```
