# RPP-Control

This page describes the broad concept behind the program. [See the wiki](https://github.com/pl9ed/RPP-Control/wiki) for detailed documentation for each component and subVI.

## Table of Contents
* [Program Overview](#Program-Overview)
* [Documentation](#Documentation)
    * [Input Components](#Input-Components)
    * [Logic/Decision Components](#Logic/Decision-Components)
    * [Output Components](#Output-Components)

## Program Overview
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

## Documentation

#### Input Components
importExcel: read data from Excel file

importExcel_DDE: read data from Excel file

importValue: read data from delimited data (txt,csv,tsv,etc)

#### Logic/Decision Components
group_data: bundles data corresponding to each sample (i.e. COM port, RPP value, pump output, etc associated with each specific rat is grouped together) 

system_logic: decides infuse/withdraw/no action

#### Output Components
placeholder

#### Other Components
front_panel: overall main program responsible for connecting components together and interfacing with the user

spoof_data: periodically generates random numeric values, for testing

spoofExcel: periodically writes random numeric values to an Excel file, for testing

### Links to relevant sections in the wiki
[Wiki Home and Index](https://github.com/pl9ed/RPP-Control/wiki)

[Input Components](https://github.com/pl9ed/RPP-Control/wiki/1.-Input-Components)

[Logic/Decision Components](https://github.com/pl9ed/RPP-Control/wiki/2.-Logic-and-Decision-Components)

[Output Components](https://github.com/pl9ed/RPP-Control/wiki/3.-Output-Components)
