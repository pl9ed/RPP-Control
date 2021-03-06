**CURRENTLY A WORK IN PROGRESS**

## Table of Contents
* [Program Overview](#Program-Overview)
* [General Components](#general-components)
    * [front_panel](#front_panel)
    * [spoofExcel](#spoofExcel)
* [Input Components](#Input-Components)
    * [importExcel](#importExcel)
    * [importExcel_DDE](#importExcel_DDE)
    * [importValue](#importValue)
* [Logic/Decision Components](#LogicDecision-Components)
    * [group_data](#group_data)
    * [system_logic](#system_logic)
* [Output Components](#Output-Components)
    * [pump_control](#pump_control)
    * [create_log](#create_log)
* [Glossary](#Glossary)

Contact pl9ed@virginia.edu for information

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

# General Components

## front_panel

![front panel](images/front_panel.png)

Overall main program responsible for connecting components together and interfacing with the user. Block diagram connects sub-components.

## spoofExcel

![Excel spoofing program](images/spoofExcel.png)

Standalone program that periodically writes values into an Excel spreadsheet. Can either generate random values or output based on a CSV file. Can use either the Report Generation Toolkit or write to a delimited spreadsheet and forces an Excel file extension. Used primarily for testing.

# Input Components
## importExcel
**INPUTS**: Path (path), RANGE (str)

**OUTPUTS**: Excel Data (dbl array)

Reads in Excel data from *Path* in the cell range *RANGE*. Uses ActiveX methods. The data from Excel is imported as a string and then converted into double using "Fract/Exp String to Number". As a result, any non-numeric data within this range will also be converted into its double value. **Not used in current iteration**

## importExcel_DDE

![Import Excel](images/importExcel_DDE.png)

**INPUTS**: Filename (str), ROW (int), COLUMN (int)

**OUTPUTS**: Value (dbl)

Reads in data from Excel spreadsheet named *filename* at a specific cell at (*row*,*column*), and outputs the value at that cell. Uses OpenDDE. The file must be open in Excel prior to running the program. The data from Excel is imported as a string and then converted into double using "Fract/Exp String to Number". As a result, any non-numeric data within this range will also be converted into its double value.

## importValue
**INPUTS**: Filepath (path)

**OUTPUTS**: Data (dbl array)

Simple VI that reads in delimited data from *filepath* and outputs data in a double array. Uses LabVIEW "Read Delimited Spreadsheet." Delimiter can be changed. Type of file does not matter, but will typically be txt, csv, or tsv. **Not used in current iteration**

# Logic/Decision Components
## group_data ###

![group data](images/group_data.png)

**INPUTS**: VISA Resource Names (VISA name array), Input Values (dbl array)

**OUTPUTS**: Grouped Data (cluster of arrays)

Groups the input values with their corresponding VISA array. The grouping is done based on the index at *VISA Resource Names* and *Input Values*, so we need to make sure that the VISA Resources and Excel spreadsheet input range are set in the correct order. Otherwise, the pump won't be infusing/withdrawing for the correct mouse.

## system_logic ##

![system logic](images/system_logic.png)

**INPUTS**: Value (dbl), Upper Threshold (dbl), Lower Threshold (dbl)

**OUTPUTS**: Action (int)

Decides on a course of action based on numerical comparisons of *Value*, *Upper Threshold*, and *Lower Threshold*. Outputs an integer *Action* corresponding to the decision. 0: Infuse 1: Withdraw 2: No action 3: Error. *Action* values for infuse and withdraw (0/1) correspond to the ones used by NX drivers.

# Output Components
## pump_control ##

![pump control](images/pump_control.png)

**INPUTS**: VISA Resource Name (VISA name), Syringe Inside Diameter (dbl), Dispense Volume (dbl), Flow Rate (dbl), Direction (int), Units (int)

**OUTPUTS**: Pump Firmware Version (str), Volume Dispensed (str), Pump Status (bundle)

Interfaces with the pump at *VISA Resource Name* to withdraw/infuse *Dispense Volume* at *Flow Rate* based on *Direction*. *Volume Dispensed* returns a string which contains both the withdrawn and infused volume. *Pump Status* is a boolean bundle that functions as an indicator for when the pump is running/stopped/etc. pump_control is functionally similar to single run example from NE library. This subVI starts with an initialization phase where the pump memory is cleared and parameters are set. This means *Volume Dispensed* is for each individual infuse/withdraw. If we want the total volume dispensed, we simply have to sum *Volume Dispensed* from the log file.

## create_log

![create log](images/create_log.png)

**INPUTS**: VISA Resource Name (VISA name), Read Value (dbl), Lower Threshold (dbl), Upper Threshold (dbl), Action (int), Volume Dispensed (str)

**OUTPUTS**: Output Array (str array)

Groups the relevant data together and creates a log file for diagnostic and troubleshooting purposes. Takes in data from other portions of the program and groups them into an array, along with system date and time. Data are converted into strings. Doubles are truncated from the 3rd digit after the decimal. This array is appended to a csv file in the current directory with the filename *VISA Resource Name_log.csv* (e.x. COM1_log.csv). *Output Array* is technically unnecessary, but is currently used as a display on the front panel. The order of the output is as follows:

Data, Time, Read Value, Lower Threshold, Upper Threshold, Action, Vol Dispensed

# Glossary

### Data Types
**boolean** (bool)

True/false. LabVIEW stores booleans as an 8-bit value, where 0=false, and any non-zero value=true. In LabVIEW, these are green.

**integer** (int)

Numerical integer values. Pretty straightforward. In LabVIEW, these are blue.

Examples: 1, 0, 32

**float and double** (flt/dbl)

[Floating point precision numerical values.](https://en.wikipedia.org/wiki/Single-precision_floating-point_format) For our purposes we can think of these as numbers with decimals, or real numbers. In LabVIEW, these are orange.

Examples: 23.50, 3.14159, 1.00

**string** (str)

Essentially text, or a string of characters. [LabVIEW uses ASCII encoding for strings.](https://en.wikipedia.org/wiki/ASCII) You can also have numbers and special characters, but note that the string '123' is not the same as the number 123 due to the way the different types are represented in the computer. Thus, if you try to do arithmetic directly on strings, you'll often get nonsensical results (e.x. 1+1=b). In LabVIEW, these are pink.

Examples: abc, John Smith, 123!@#

**Array** (array)

Group of similar data types.

Examples: [1 0 32], [23.50 3.14159 1.00], [abc John Smith 123!@#]

**Cluster** (cluster)

Group of dissimilar data types. Unique to LabVIEW. Items can be grouped/ungrouped using various bundle/unbundle functions.

Examples: [123 "123" "John Smith" 32.50], [2020/02/28 17:10:59 COM1 151.50]

### Links to helpful resources
[LabVIEW Data Types](https://www.ni.com/getting-started/labview-basics/data-structures)
