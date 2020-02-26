## Table of Contents
* [Program Overview](#Program-Overview)
* [Instructions](#Instructions)
* [Documentation](#Documentation)
    * [Input Components](#Input-Components)
    * [Logic/Decision Components](#Logic/Decision-Components)
    * [Output Components](#Output-Components)
* [Troubleshooting/Debugging: Relevant Concepts](#troubleshootingdebugging-relevant-concepts)

# Program Overview
![Block diagram for general negative feedback system](https://www.tutorialspoint.com/control_systems/images/positive_feedback.jpg)

The program functions as a basic feedback loop. The process can be generalized as: take in a value, compare that value to predetermined thresholds, and then decide on an action based on that comparison. Thus, although the program is written for RPP control, it can be applied to any metric that can benefit from a feedback loop (e.x. heartrate, pharmacokinetics, etc)

The program therefore has 3 basic components:
* Read input
* Decide on action
* Write output

![general components](https://github.com/pl9ed/RPP-Control/blob/master/generalcomponents.png?raw=true)

### Specific case: RPP Control
![Block diagram for specific RPP implementation](https://github.com/pl9ed/RPP-Control/blob/master/feedback_simple.png)

In the specific case, RPP measurement is used to determine whether or not to inflate an aortic occluder using a syringe pump.
Our 3 components:
* Get RPP value from telemetric transmitter (in this case, via Excel spreadsheet)
* Infuse if RPP is too high, withdraw if too low, no action otherwise
* Write the appropriate commands to control the syringe pump in order to bring about the desired effect

![specific components](https://github.com/pl9ed/RPP-Control/blob/master/rppcomponents.png?raw=true)

If we wanted to use the program to control heartrate, for example, we would only need to change the output component to match whatever we're using to increase or decrease heart rate. The numerical values for the other components (input and thresholds, for example) might change, but the program itself would not.

# Instructions

Placeholder

# Documentation
This section describes the function, inputs, and outputs for each subVI in the program.

### front_panel
overall main program responsible for connecting components together and interfacing with the user

### spoof_data
periodically generates random numeric values, for testing

### spoofExcel
periodically writes random numeric values to an Excel file, for testing

## Input Components
### importExcel
**INPUTS**: filepath (path), data range (string)

**OUTPUTS**: numerical data from the specified data range (double array)

Reads in Excel data using ActiveX methods. The data from Excel is imported as a string and then converted into double using "Fract/Exp String to Number". As a result, any non-numeric data within this range will also be converted into its double value.

### importExcel_DDE
**INPUTS**: filename (string), row (int), column (int)

**OUTPUTS**: data at specific cell (double)

Reads in data from Excel spreadsheet named *filename* at cell (*row*,*column*) using OpenDDE. The file must be open in Excel prior to running the program. The data from Excel is imported as a string and then converted into double using "Fract/Exp String to Number". As a result, any non-numeric data within this range will also be converted into its double value.

### importValue
**INPUTS**: filepath (path)

**OUTPUTS**: data (double array)

Simple VI that reads in delimited data from *filepath* and outputs data in a double array. Uses LabVIEW "Read Delimited Spreadsheet." Delimiter can be changed. Type of file does not matter, but will typically be txt, csv, or tsv.

## Logic/Decision Components
group_data: bundles data corresponding to each sample (i.e. COM port, RPP value, pump output, etc associated with each specific rat is grouped together) 

system_logic: decides infuse/withdraw/no action

## Output Components
placeholder

# Troubleshooting/Debugging: Relevant Concepts
These concepts will hopefully point you in the right direction when something goes wrong, or if changes need to be made. 

### Data Types
**integer** (int)

Numerical integer values. Pretty straightforward. In LabVIEW, these are blue.

Examples: 1, 0, 32

**float and double** (flt/dbl)

[Floating point precision numerical values.](https://en.wikipedia.org/wiki/Single-precision_floating-point_format) For our purposes we can think of these as numbers with decimals, or real numbers. In LabVIEW, these are orange.

Examples: 23.50, 3.14159, 1.00

**string** (str)

Essentially text, or a string of characters. [LabVIEW uses ASCII encoding for strings.](https://en.wikipedia.org/wiki/ASCII) You can also have numbers and special characters, but note that the string '123' is not the same as the number 123 due to the way the different types are represented in the computer. Thus, if you try to do arithmetic directly on strings, you'll often get nonsensical results (e.x. 1+1=b). In LabVIEW, these are pink.

Examples: abc, John Smith, 123!@#

### Links to helpful resources
[Basic Data Types](https://www.cs.uic.edu/~jbell/CourseNotes/ProgrammingConcepts/DataTypes.html)
