The code in this repository is used in the project deriving from a Master's Thesis. 
It is used to program a Raspberry Pi Pico W to take readings from a Scale and interpret them into takt signals. 

The Circuit Diagram of the Raspberry Pi is depicted in the attached image. 
The wires going to the top of the diagram connect to the TX pin, VCC pin, and GND pin of the scale RS232 connection.

The Rapsberry Pi Pico W was not able to process the code with the buttons (start/stop and continue) implemented. The code 'Raspberry Pi no buttons' was therefore adapted to this change. This code was tested in an industrial environment with success. The two versions of the code are not reflective of the same process. The code with no button integration has been optimized to work as is required.

The following is a description of the process which the code attempts to implement.

LED States: 

  •	Red – solid: stopped state
  
  •	Red – blinking: error state
  
  •	Yellow: ready and paused state
  
  •	Green: operating state
  
  •	Continue Button LED: system awaiting action from continue button
  
  •	Start/Pause Button LED: system awaiting action from start/pause button
  
Program states: 
1.	System Initialization:

  •	The system starts with an empty scale.

  •	The red LED remains ON, signaling the stopped state.
  
  •	If the system detects an error (blinking red LED), the operator resets the system.
  
4.	Recording Carrier Weight:

  •	The operator places an empty carrier on the scale.
  
  •	Once the system detects a stable weight, the continue button LED turns ON.
  
  •	The operator presses the continue button to confirm the carrier weight.
  
  •	The system records the carrier weight and awaits part placement.

6.	Recording Part Weight:

  •	The operator places one part in the carrier.

  •	When the system detects the part weight, the continue button led turns ON.
  
  •	The operator presses the continue button to confirm the part weight.
  
  •	The system records the part weight and transitions to a ready for operation state:
  
    •	The red LED turns OFF.
    
    •	The yellow LED turns ON (ready and paused state).
    
    •	The start button LED ON.
    
8.	Starting the Process:

  •	The operator presses the start button.
  
  •	The yellow LED turns OFF.
  
  •	The green LED turns ON, signaling that the system is active.
  
  •	The system sends an initial takt signal for the part already in the carrier.
  
10.	During Operation:

  •	Adding Parts to the carrier:
  
    •	When the operator adds parts, the system detects the weight change and sends takt signals for each added part. The weight change detected by the scale must be within a certain margin of the initially recorded part weight.
    
    •	A variable for an absolute counter is implemented to count the total number of takts. Every time a takt is sent, the absolute counter is raised by one count. This counter is only reset when the Pi is reset. It acts as the counter of the MES system. 
    
  •	Removing Parts to the carrier:
  
    •	If one or several parts are removed, the system pauses:
    
      	The green LED turns OFF.
      
      	The yellow LED turns ON.
      
    •	The operator can place the parts back to resume automatically or press the start button to continue. If the weight that was removed from the carrier is replaced into the carrier (within a certain margin). If the start button is pressed, the parts are not to be placed into the carrier again as to not send repetitive takts. Any parts placed into the carrier subsequently trigger further takts. 
    
  •	Carrier Replacement:
  
    •	When a full carrier is replaced, the system detects the new carrier. The scale in AUTOPRINT mode does not send a weight signal if it is equal to zero. The next signal captured after removing the full carrier is the weight of the new, empty carrier. This reduction in weight (high multiple of the part weight) pauses the system.
    
    •	The green LED turns OFF.
    
    •	The yellow LED and start button LED turn ON.
    
    •	The operator confirms the new carrier weight by pressing the start button, and the system resumes operation (start button LED and yellow LED turn off, green LED turns on)
    
12.	Error Handling:
    
  •	If unexpected weights or inconsistencies are detected, the system enters an error state:
  
    •	The red LED blinks, and the system halts.
    
    •	The operator resets the system by removing all items from the scale.
    
    •	The operator has to start the carrier and part weighing process again. 
