# Boxzy-Fusion360-Laser
Modified smoothie post processor for Fusion360 laser operations

-Readme concerning file "smoothie Boxzy laser Rev01.cps", created 06/30/2019 B.Glaser

All changes to the original smoothie code are flagged with "Boxzy" as well as the Revision number (e.g. "Rev01")

The gist of it:
  - Boxzy's laser power is controlled by gcode with prefix "L" (instead of Smoothie's prefix "S")
  - Boxzy's laser power values vary from L0 to L100 (percentage of power)
  - Boxzy needs the laser power command on each G1 line of code.  For example, consider this 40mm square:
       - G92 X0 Y0
       - G0 X10 Y10 F1000
       - G1 X50 L100 F800   ; Feed = 800mm/min, Laser power = 100%
       - G1 Y50 L100        ; Feed F800 is implied, but L100 is not.  It must be included for each line.
       - G1 X10 L100        
       - G1 Y10 L100        
       - G0 X0 Y0 F1000
       
The trick of it:
  - The trick was to manually enter an "L" into the writeBlock command in function onLinear(_x, _y, _z, feed)
  - Search for "function onLinear" in the .cps file, and you will see both the new and old code
  - Adding the character "L" manually into the writeBlock command forces it to show up in every G1 block

Saving the modified prost processor to your computer:
  - Download "smoothie Boxzy laser Rev01.cps" from github
  - Open Fusion > Manufacturing
  - Create a 2D Profile setup
  - Right click on the operation and choose Post Process
  - The folder location of .cps files is at the top of the post-processing window
  - Copy "smoothie Boxzy laser Rev01.cps" to that folder.
  - It may or may not be necessary to re-open the model
  - In the post process window, choose "smoothie Boxzy laser Rev01.cps" as your processor
  
Setting up a "2D Profile" in Fusion360
  - Tool:  laser
  - Cutting mode:  Through or etch as appropriate
  - Compensation Type:  In computer

User Defined Post Processor Settings (can be changed during each post)
  - Laser etch power:  10 (etching power)
  - Laser power: 100 (through cutting power)
  - Laser tool number:  1

The unintended consequence of Boxzy using "L" for laser power
  - M98 subroutines do not seem to work with Boxzy, because M98 uses "L" commands to set the number of repeat cycles
  - For example, this does not work in my Boxzy:  
       
       - G92 X0 Y0
       - M98 P1001 L10  ; call subroutine O1001 ten times
       - G0 X0 Y0 F1000
       
       - ;Subroutine 1001, 40mm square
       - O1001
       - G0 X10 Y10 F1000
       - G1 X50 L100 F800   
       - G1 Y50 L100        
       - G1 X10 L100        
       - G1 Y10 L100 
       - M99
       - ;End subroutine 1001
       
  
  - Nor does this work (actually crashes my Boxzy):
       
       - G92 X0 Y0
       - M98 P101001  ; call subroutine O1001 ten times
       - G0 X0 Y0 F1000
       
       - ;Subroutine 1001, 40mm square
       - O1001
       - G0 X10 Y10 F1000
       - G1 X50 L100 F800   
       - G1 Y50 L100        
       - G1 X10 L100        
       - G1 Y10 L100 
       - M99
       - ;End subroutine
       
I hope it helps someone.
Good luck!
