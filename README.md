# macro_edingCNC_sorotec
Macro subroutine collection for Eding's Computerized Numerical Controllers, specialized for Sorotec portal milling machines 

## Preface / Resources
Please visit my [website](https://blog.schallbert.de/macros-for-cnc/) for an in-depth introduction into CNC macros.

> "A Macro or subroutine is a collection of instructions written for the CNC interpreter to perform actions. They are often used to automate recurring operations."

There are many good third party resources out there to learn about writing them, e.g. [cnccookbook](https://www.cnccookbook.com/cnc-macro-programming-fanuc-macro-b/), too.

The macros provided in this repository are somewhat narrowed to [EdingCNC](https://edingcnc.com/)'s interpreter and are starting off a 'fork' of the non-version-controlled `Standard_Macro_V2-1e` by [Sorotec](https://www.sorotec.de/).

## Disclaimer
⚠️ Warning: Usage and tryout of macros on a physical machine are kind of risky. There's no safety net. One variable entered incorrectly, a parameter with a wrongly placed decimal separator, or simply a line with an error may lead to severe damage of machine, material, and even injuries. Use the provided macros carefully and at your own risk. I do not take any liability.

💡 Idea: When you write a new routine, use of Code `M00` might simplify testing your routine. This code will pause the macro and wait for user input (Cycle Start) to continue.  

## Folder structure of this repo
This repository has two folders for assets:
- `dialogPictures` that keeps images presented in the dialog message boxes
- `op_f_key` that keeps icons of 'user' macro's function buttons

The images are pulled by EdingCNC based on their file names. For dialog pictures, e.g. the `dlgmsg`'s title parameter has to exactly match the corresponding file name in `dialogPictures`.
Within the `user` folder, icon names have to match the corresponding user macro number, e.g. `U3` for user `sub user_3`.

## User Macro routines in this repository
### Sub user_1: find Z-Zero
Uses a tool length sensor (alternatively, a 3D edge finder) to detect workpiece surface height.
Sets local `G92` working coordinate offset to `Z=0` at this place.
![Image: determining Z-zero with help of a tool length sensor](https://blog.schallbert.de/assets/images/posts/2022-06-11_tls.jpg)

### Sub user_2: measure tool length
Determines tool length in the sense of how long it protrudes from the spindle collet.
![Image: How the machines requires an estimate for how long the tool might protrude before getting the measurement](https://blog.schallbert.de/assets/images/posts/2022-06-11_toolLengthVariables.jpg)

### Sub user_3: check for cutter failure
Basically this routine is intended to re-check an endmill's length after a given job, compare it with the value before, and notify the user in case the tool has become "shorter".

### Sub user_4: spindle warmup
Some spindle manufacturers specify a spindle warmup profile. Dwell times and spindle speed can be entered using a `config` routine so the requested ramp can be executed.

### Sub user_5: tool change
Calls `change_tool`, continue reading [on my blog](https://blog.schallbert.de/macros-for-cnc/#werkzeugwechsel)

### Sub user_6: tool change per G43 length offset (tool table)
Just asks the operator to manipulate the tool, will apply the tool length offset for that tool (no measurement here!)

### Sub user_8: find XY center (edge finder)
Geometrical detection of the center of a bore.

### Sub user_10: go to machine Zero

### Sub config: Configures basic macro settings
In here, you can define where on the machine tool length measurements shall take place, How high the tool length sensor is, enter the position of the spindle collet, define spindle warmup ramp parameters, etc.

## Eding's Macro routines
### Sub home_z: reference machine's Z-axis zero

### Sub home_y: reference machine's Y-axis zero

### Sub home_x: reference machine's X-axis zero

### Sub home_a: reference machine's A-axis zero

### Sub home_b: reference machine's B-axis zero

### Sub home_c: reference machine's C-axis zero

### Sub home_all: home all active machine's axes
Calls all active axes' home routine, order is `ZYX` `ABC`

## Variables
As per [EdingCNC handbook](https://edingcnc.com/wp-content/uploads/2021/11/edingcnc_manual_v4.03.pdf?_gl=1*12ihvf2*_up*MQ..*_ga*NDA5OTcxMDMwLjE3MjAyNzAxODE.*_ga_QW7BG3X45E*MTcyMDI3MDE3OS4xLjAuMTcyMDI3MDE3OS4wLjAuMA..*_ga_G8V3KZYQ63*MTcyMDI3MDE3OS4xLjAuMTcyMDI3MDE3OS4wLjAuMA..) `R4.03` chapter `3.1` "Systemparameters/ -variables", [RS274 NGC](https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=823374) defines variables in the range of `#1...#5400`. Some of those parameters are read-only, some system-specific, some volatile and some others permanent. Let's shed some light here:

### Properties
- Parameters have the [double precision floating point](https://en.wikipedia.org/wiki/Double-precision_floating-point_format) number data type.
- EdingCNC's parameter range is higher than defined in the standard, it ranges until `#5999`.
- Parameters are nameless which can make writing macros a pretty cumbersome process.

### Set / Get
- A parameter can be assigned in a macro file as simply as this: `#100 = 47.11` which assigns the number `47.11` to the parameter with ID `#100`.
- A parameter can be evaluated with different statements, example: `IF [[#5011] == [#5008]] THEN ...` for a deeper syntax overview, please refer to the EdingCNC handbook.

### Volatile
Variables in range `#26...#3999` are volatile and can be used by the programmer to store data temporarily. The data assigned in this range is deleted on power off or in the event of an alarm/emergency stop.

### Permanent
The variable range `#4000...#4999` are stored permanently on the system. They are free to be used.
They keep their value also when powered down, after an emergency stop etc.
EdingCNC uses some of these variables per default, e.g. `#4995...#4999`. 

| ID    | name                    | description                                                       |
| ----- | ----------------------- | ----------------------------------------------------------------- |
| #4995 | toolLengthSensorHeight  | Saves height of tool length sensor for correctly setting Z-zero   |
| #4996 | toolLengthSensorFlyover | Safety height above tool length sensor (recommended: machine Z0)  |
| #4997 | toolLengthSensorPosX    | X-position of the tool length sensor                              |
| #4998 | toolLengthSensorPosY    | Y-position of the tool length sensor                              |
| #4999 | toolLengthSensorChuck   | Switching point of the tool length sensor when no tool in spindle |

### System-reserved
Generally speaking, the variable range from `#5000...#5999` should be avoided by the programmer - most of them are used system-internally or have intentionally been left blank.

### Write-protected 
1. #1...#26 ; Code for letters where `#1=A`, `#26=Z`. Can be used in subroutines and macros.
2. #5001...#5006 ; Current machine position where `#5001=X-axis` and so on.
3. #5008 ; Current tool number
4. #5009 ; Current tool's radius
5. #5010 ; Current tool's Z-offset
6. #5011 ; Incoming tool number on tool change
7. #5012...#5014 ; Current tool's G43 offset (tool table)
8. #5051...#5056 ; Sensor position in machine coordinates
9. #5061...#5066 ; Sensor position in work coordinates
10. #5067 ; Flag that is written to `true` if sensor has triggered during a Z-sensing run `G38.2`
11. #5068 ; sensor's trigger point on Z-axis (machine coordinates)
12. #5069 ; Handwheel step count
13. #5070 ; spindle speed `S` in `r/sec`
14. #5071...#5076 ; Current machine position (machine coordinates)
15. #5081...#5086 ; Sensor position in joint coordinate system
16. #5101...#5106 ; `MCA` Machine Change Area negative limit: Area the machine may not penetrate in normal operation, e.g. to protect vices
17. #5111...#5116 ; `MCA` Machine Change Area positive limit
18. #5121...#5126 ; Machine's home position (machine coordinates)
19. #5131...#5133 ; `TCA` Tool Change Area negative limit: Area the machine may only access for tool changes
20. #5242...#5143 ; `TCA` Tool Change Area positive limit
21. #5150 ; Machine's coordinate type. `1 = Trivial/Cartesian` `2 = 4th axys cylinder as Y-axis` `Virtual C-axis` `4-17 = reserved` `18-30 = user defined`
22. #5151 ; flag showing `ZHC` Z-height compensation status where `true = active`
23. #5152 ; flag showing Spindle status where `true = on`
24. #5161...5166 ; `G28` parking position one's axis values (machine coordinates)
25. #5181...5186 ; `G30` parking position two's axis values (machine coordinates)
26. #5190 ; `G68` flag showing rotation of coordinate system where `true = active`
27. #5191...#5193 ; Axis rotation point (machine coordinates)
28. #5194...#5196 ; Axis rotation angle around rotation point 
29. #5200 ; flag showing `G51` scaling of coordinate system where `true = active`
30. #5204 ; Non-uniform scaling: scale factor X
31. #5205 ; Non-uniform scaling: scale factor Y
32. #5206 ; Non-uniform scaling: scale factor Z `always 1.0`
33. #5390 ; flag showing active Spindle selection, `M90 = 0` `M91 = 0` `M92 = 2`, `M93 = 3`, default 0
34. #5391...#5393 ; active spindle offset X-Z
35. #5394...#5397 ; maximum spindle speed for spindles `0...3`


# Working
@TODO: Add asset folder for icons and show images of macro execution within EdingCNC.
