# LabsOf1.github.io

### PROJECT 1 Multi-Power Distribution Sensor-Based MUX DC Motor Controller

**Summary:**  
An electromechanical multiplexer system that controls multiple DC/AC motors using a single control circuit, reducing hardware complexity and increasing scalability in multi-axis platforms.

**Problem Solved:**  
Controlling many actuators typically requires separate channels or motor drivers. This system enables full motion control across X, Y, Z, and rotational axes using a shared PID-regulated distribution logic and a PLC-based switching mechanism.

**Key Features / Innovations:**
- Memory-mapped control of 5+ actuators using shared sensor inputs
- Integrated PID voltage regulation with safety cutoff during signal drift
- Sensor-triggered motion along all degrees of freedom with minimal user inputs
- Power-efficient: multiple actuators run off a single AC/DC conversion loop

**Technical Overview:**
- Allen-Bradley CompactLogix PLC
- Custom DC motor controller logic (SW1, SW2, xa–xe terminals)
- Feedback from 8 proximity sensors
- Full AC/DC converter: 220V AC to regulated 12V DC with ripple filtering
- Relay-based protection and PID voltage switch (Vs_diff_SW)

**Outcomes / Results:**
- Successfully demonstrated back-and-forth motion with logical switching
- Reduced control input count by 75% while expanding actuator control
- Built a scalable architecture for industrial or research robotic platforms


**Next Steps / Potential Applications:**
- Modular extension for robotic arms, industrial conveyor platforms
- Real-time integration with distributed control systems (via Ethernet/IP)
- Applicable to low-cost automation systems in labs or factories

**Diagrams / Schematics and Technical Document:**
![image](https://github.com/user-attachments/assets/a6168f3a-7b55-4641-9f1d-b0a68528f0a9)

Fig.0 Sensor-Based MUX

![image](https://github.com/user-attachments/assets/e0987473-b0cc-4844-ba3c-8f08ceeff58b)

Fig.1 Platform with Multiple Degrees of Freedom

The working principle consists of a control input that activates a set of proximity sensors. The activated sensor triggers any electrically connected components or motors. The goal is to implement a method to control a platform with multiple degrees of freedom (Fig 1) using only one multiplexer or DC Motor Control Circuit that can implement any desired control algorithm (Fig 0).

![image](https://github.com/user-attachments/assets/ebc75f24-88c1-47fe-bcab-34a776e4a99e)

Fig.1a Sensor-Based MUX

The goal behind this implementation is to design an electromechanical like multiplexer that controls several motors simultaneously while simplifying the number of user input commands. To demonstrate, we will consider the example project in (Fig 1). It consists of a conveyor belt; one AC motor that controls the speed and frequency of rotation of the gears; one DC linear actuator that moves the AC motor through a system of gears; one DC linear actuator responsible for moving the vertical shaft holding the control platform along the z axis; one DC rotational linear actuator responsible for CW and CCW platform rotations; one DC rotational linear actuator responsible for 45-degree angular platform rotations. There are a total of eight proximity sensors: ForwSensor, BackwSensor, UpSensor, DownSensor, FortyFiveDegPosSensor, FortyFiveDegNegSensor, FrontSensor, BackSensor. The ForwSensor and BackwSensor control the movement of the platform in the x dimension acting as limits. Similarly, (UpSensor, DownSensor), (PosRotSensor, NegRotSensor) limit motion in the z dimension and CCWx/CWx direction respectively. The MotorAC and MotorLinAct complex move in the y dimension and are controlled by sensors: FrontSensor and BackSensor acting as limits. At each MotorACStartSensor proximity sensor, power is transferred to actuators: MotorLinAct, MotorRotLinActPistonMotionStart (controls platform motion in CCWz/CWz), MotorUpDownLinActMotionStart (controls platform motion along the z axis), MotorFortyFiveDegTiltLinAct (controls platform along the CCWz/CWz direction) and finally to the AC motor itself named as MotorACHorzMotionStart.

Note: When we mention AC Motor we mean a Brushless DC Motor.

![image](https://github.com/user-attachments/assets/9f06989d-243f-4b74-af10-171598af0618)

Fig.2 Sensor Based MUX Overview

An AC voltage signal VAC is converted to a DC voltage signal Vs_ref. A PID controller is used to detect noticeable changes in the DC voltage from the AC DC supply to a DC actuator. If Vs differs noticeably from Vs_ref, the switch Vs_diff_SW is activated and breaks the connection. Vs_diff_SW is returned to its normal state when there is close to zero difference between Vs_ref and Vs. Upon successful transmission the DC Linear Actuator is activated with Vs. The AC Motor and DC Actuators are both then ready to receive instructions from the PLC and perform their operations in any desired order. The speed of the AC Motor can be controlled by a Variable Frequency Driver VFD.

![image](https://github.com/user-attachments/assets/dfe5b563-fda4-4a05-b3fb-24c1137c66fe)

Fig.3 AC/DC Converter

A 220 V AC signal is converted to 15 V AC with a 220 VAC to 15 VAC transformer. The 15 V AC signal then passes through a full wave rectifier. The capacitors in parallel to the IN4001 diodes are responsible for minimizing signal oscillations and vibrations ensuring a smooth rectified signal with minimal disturbances. The rectified signal passes through a polarized 220 mF capacitor which filters out high frequency components and a non-polarized 1 mF capacitor which filters out any remaining ripples in the 15 V AC signal. The linear voltage regulator IC 7812 reduces the signal amplitude to 12 V DC. The two remaining parallel capacitors: non-polarized 1mF and polarized 1mF are used for assisting the regulator supply gross load changes by removing some of the load from the regulator.
A and B are six-way and nine-way wire connectors respectively. Connector A captures the initial voltage or the reference voltage Vs_ref, while B captures the voltage Vs near the DC Actuators. Vs_ref is compared with Vs and if the difference Vs_diff >> 0, the four-pole relay is energized. At energized state the connection between AC/DC converter and DC Motor is broken.

![image](https://github.com/user-attachments/assets/4fa25918-ead6-465d-a95e-19a8912c1082)

Fig.4 Proportionality Integrator Derivative (PID) Controller

The PID controller corrects and fixes voltage drops between the AC/DC converter (Vs_ref) and the DC Actuators (Vs). When Vs_ref and Vs differ by a large amount and are not approximately equal the Vs_diff_SW is energized and immediately disconnects DC actuators from power supply. The parameters within the PID controller are designed to be small such that Vs is approximately equal to Vs_new. And as such the PID controller is only useful for very small corrections in voltage.

![image](https://github.com/user-attachments/assets/5fad0271-6dd0-44e5-8944-afdc535b20e9)

Fig.5 DC Motor Control Circuit (Controller)

All DC Motor actuators will be controlled by a single DC Motor Control Circuit. Controls SW_1 and SW_2 control the forward and reverse motions of the linear or rotational actuators. Inputs xa, xb, xc, xd determine which DC actuator terminals v+ and v- will be distributed to by feeding them back into the PLC. This will result in multiple 2 terminal pairs: (xav+, xav-) ... (xdv+, xdv-) each supplying individual actuators with power of varying levels. The voltages supplied: Vsa, Vsb, Vsc, Vsd can either be equal or different depending on the characteristics of the inductor, NPN transistor, variable resistor and diodes. 

![image](https://github.com/user-attachments/assets/9a9a6298-414a-4806-8d18-ae3c20a92f9f)

Fig.5a DC Motor Controller 

For this project switches SW_1, SW_2 each represent the multiple switch sensor states of each DC motor, effectively reducing eight different switches into two by sharing the same memory address. Switches xa, xb, xc, xd, xe represent the states in when each motor will operate upon activation.

![image](https://github.com/user-attachments/assets/9a0e436b-a0d9-4e16-adfd-c50df672adae)

Fig.5b DC Motor Controller

Overview of how this controller will operate on this given project. Additional switches Forw_SW and Rev_SW are added for additional protection. A program will also be added to the PLC to assist it in locating the correct motors for proper power distribution.

![image](https://github.com/user-attachments/assets/b2954de3-8b8a-4863-8701-2e3a2dcdb8cc)

Fig.5c DC Motor Controller

The controller can also be used on multiple PLC’s that have similar voltage requirements. For safety this application can be used only for low voltage applications. For this controller to function safely and efficiently for any system voltage low or high, we would need a system of virtual infinite inductors. The advantage here is that multiple processes can be synchronized and controlled concurrently with only one motor controller, PID and PLC motor distribution program (which can be connected to the PLC’s using either USB or ethernet cables or can be written using structured text if the PLC has no programming support for C programs). 

![image](https://github.com/user-attachments/assets/526d549f-802e-44df-85a3-72ed867117b3)

Fig.5d Multi-Power Distribution Sensor-Based MUX DC Motor Controller

This shows a compact form of the MUX. It can be placed inside a control box for more complex motor control systems.

![image](https://github.com/user-attachments/assets/6a878797-9091-472e-a6a8-66d7c7e6b718)

Fig.5e Multi-Power Distribution Sensor-Based MUX DC Motor Controller Action on a PLC

This describes the working principle behind the MUX. A PLC_A cable from the cable splitter is connected to a PLC. Assuming the PLC has an Ethernet/IP module the cable downloads a program from the PC to instruct the PLC which motors can be activated at a given time. The PLC_A_Activation contains the address of the PLC port at the PLC_A Cable Splitter input. In this way the program can identify which PLC it needs to program. The System_SW are a set of inputs containing all the switches in the system the PLC controls that are all memory mapped one at a time to the motor controller’s switches SW depending on the state of the system. Ports V+ and V- are the two end terminals of the motor controller. Depending on which sensors are activated at a given time the program copies the inputs V+, V- through memory and transfers them to the appropriate terminals to activate the appropriate motor. This method can easily be scaled to more advanced systems with multiple controls.

![image](https://github.com/user-attachments/assets/9781b5e2-5b15-4836-9d0e-41b1f67d3a47)

Fig.6 Overview of Example Project

![image](https://github.com/user-attachments/assets/3200c57a-e2a6-4d43-b45f-2e140d5b9673)

Fig.6a Control Block 

For multiple such systems we can use a control block to activate a particular system of interest.

![image](https://github.com/user-attachments/assets/6bb890f8-65bc-4cce-a24e-ec5aee887e0b)

Fig.6b POU_1

Controls the MotorLinAct relative to the FrontSensor and BackSensor. The linear actuator will start at the back sensor upon activation and proceed in the forward phase activating Front_Control_Feedback_Signal. When the front sensor is reached the linear actuator will then retract until it reaches the back sensor again. This will be the back phase where Back_Control_Feedback_Signal is activated. The process will repeat back and forth until the emergency switch, stop switch, or manual Forw_SW and Rev_SW are activated.

![image](https://github.com/user-attachments/assets/adf9f5cf-8a9d-400b-b75d-7d77a821d945)
![image](https://github.com/user-attachments/assets/c000ff3f-4a1f-428f-b6d6-3bbadb180be7)

Fig.6c POU_2

Activates the AC motor (MotorACHorzMotionStart), linear actuator responsible for driving the motor (MotorLinAct), and the rotational motor responsible for rotating the platform (MotorRotLinActPistonMotionStart) with respect to proximity sensors (MotorACStartSensor1, MotorACStartSensor2). When the AC motor reaches the first AC sensor the linear actuator will stop and the AC motor will activate a set of gears connected to the conveyor belt. After a period of time the motor will stop, MotorLinAct will activate. Upon reaching the second AC sensor the rotational actuator will activate in a similar way. Upon completion Initial_Control_Feedback_Signal is activated to inform the POU_1 module that the linear actuator is still in the forward phase.

![image](https://github.com/user-attachments/assets/ebb291ec-66ed-45d3-8afa-9c5d6d9cef56)
![image](https://github.com/user-attachments/assets/367d4147-93e2-48f1-8586-135856bad65a)

Fig.6d POU_3

Upon activation of the FrontSensor the linear actuator MotorLinAct is now in reverse mode. Upon the detection of sensor MotorACStartSensor1 power is distributed first to MotorUpDownLinActMotionStart actuator and then to MotorFortyFiveDegTiltLinAct. While if it detects sensor MotorACStartSensor2 power is distributed the opposite way. Upon completion Initial_Control_Feedback_Signal is activate to inform the POU_1 module that the linear actuator is still in the reverse phase.

![image](https://github.com/user-attachments/assets/3af22cb2-0085-430a-ae85-b80a038f8b36)

Fig.6e HorzMotionConvBelt_0

![image](https://github.com/user-attachments/assets/cd438e97-69a5-4c3a-8aa4-509442fbfb44)

Fig.6f UpDownPiston_0

![image](https://github.com/user-attachments/assets/35a51406-bf23-42e2-83e7-3b083b7ed022)

Fig.6e FortyFiveDegreeRotPiston_0

![image](https://github.com/user-attachments/assets/b4d0660e-d3b4-42ad-a1c6-c6bce0448811)

Fig.7 System Selection

Either manually or through some algorithm a system is selected. The names operation_system A, operation_system B, operation_system C describe the address of the system we would like to program.

![image](https://github.com/user-attachments/assets/8455ccf3-6178-445a-90c8-facddd7cb5ac)

Fig.7a System State Selection

After a system is selected we now have to define the states or phases within the system. For this example project they are identified through the signals POU_1_Operation, POU_2_Operation and POU_3_Operation.

![image](https://github.com/user-attachments/assets/8f18aa8f-bb83-4690-a884-431197969c67)

Fig.7b POU_1 Module

Within each instance, state or module of the system the motor controller terminals V+, V- or V_positive, V_negative are transferred to the proper terminals of the correct motor with respect to the sensor signals in the POU function and the switches from the PLC.

![image](https://github.com/user-attachments/assets/2e97a078-7f7a-404f-a9bd-5d20daa0f025)
![image](https://github.com/user-attachments/assets/c877bf6c-d4aa-4996-9f3e-4eb924ef0f89)
![image](https://github.com/user-attachments/assets/cbf61593-9f1b-4317-8040-3745754a66c4)

Fig.7c POU_2 Module

![image](https://github.com/user-attachments/assets/5f641c97-ea2a-4f8f-916f-1ed10c1abede)
![image](https://github.com/user-attachments/assets/4c3a133b-9b5e-417a-a1eb-7f10b009b1de)
![image](https://github.com/user-attachments/assets/29ddbe65-c1e5-46dc-a08e-cc792aa73d7b)

Fig.7d POU_3 Module

![image](https://github.com/user-attachments/assets/a23958f2-3f99-4702-baa3-c9469d3a8d26)

Fig.8 Overview of PLC Controlled by MUX Motor Controller for this Example Project

![image](https://github.com/user-attachments/assets/b60fbde0-11b8-4531-a81b-244ecfdb87c3)

Fig.9 Allen-Bradley CompactLogix can be used for this Implementation 



































  
