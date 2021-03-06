---
layout: default
---

# Split Flap Counter 1-0
 ![](https://github.com/refrigerated/EP1000/blob/master/docs/images/Presentation.png?raw=true)

I plan to create a Youtube/Instagram Subcriber Counter. The counter will use a split flap mechanism and was inspired by counters like [Smiirl](https://www.smiirl.com/en/store?gclid=EAIaIQobChMIvvTDg8Ls6gIVlH0rCh0ipgDZEAAYASAAEgKEMfD_BwE) and [FlapIt](https://www.flapit.com/). However due to its complexity, i decided to make the counter with only a basic and simple prototype. When I have succeeded this prototype for my DFAB project, I would built on it and make it do awesome things, like the initial plan; A subscriber counter or some other ideas, like a clock.


## Concept

The old school split flap display is cool and mesmerising to look at. To help build my counter, I will be laser cutting a lot of the split flap mechanics and 3D print the house.


## Bill of Materials

1. Arduino Uno Kit
2. Breadboard 
3. 1 push buttons
4. Stepper motor 28BYJ-48 and ULN2003 driver motor.
5. 6 male to female wire
6. male to male wire.
7. 2 M4 hex nuts and screws
8. 3 M3 hex nuts and screws



## Laser Cuts:

**Flaps**

I begin this project with the flaps first beacuse it is the simplest thing to create. I drew my sketch on Fusion 360 and laser cut on a 3mm acrylic board. Since the flaps are quite thick to be placed on a rotating wheel, I could only place digit number 1 till 0. I followed the dimension of a standard business card as my guide. From here, I could estimate the dimension of other laser cutting parts.

The numbers on the flaps are placed using white sticker labels since this is just a prototype. I feel that engraving would be too time consuming and could not be undone if I want to change the characters on the flaps. The font im using is called Solari. I printed it out on paper and cut out using penknife so I could trace it on my sticker labels like a stencil. The flap layout with text was a bit tricky, but once you understand the concept it's pretty simple. Basically the "front" of each flap pair is a whole number, and the "back" of each flap pair has the bottom of the next number and the top of the previous number. So "1" has the bottom of "2" behind its top flap, and the top of "0" behind its bottom flap.

The text layout looks like this:<br/>

![](https://github.com/refrigerated/EP1000/blob/master/docs/images/Flaps%20number%20position%20n.png?raw=true)

**Wheels**

This part is very tricky for me to get it right. Every millimeter makes a difference. I made few mistakes of getting the holes for each tiny arms of the flaps too wide and too close to each other. The circular holes for the little arms of its flaps is used using the circular pattern tool on fusion. To make sure that the top and bottom flaps do not align to far apart, make sure between each hole is at most 10mm apart. 

The diameter of the wheel also plays a role on making sure it can turn well. I used 60mm for my diameter. One of the wheel has a shape precisedly cutted out following the shape of my stepper motor on the middle. And my other wheel has a M4 hex cutted right on its middle.

I also made 4 rectangluar cuts for my spool to fit in. With some calculations, I could easily figure where the cuts should be.

![](https://github.com/refrigerated/EP1000/blob/master/docs/images/wheels%20with%20hex%20nut%20n.png?raw=true)
![](https://github.com/refrigerated/EP1000/blob/master/docs/images/left%20side%20of%20wheel%20with%20hex%20n.png?raw=true)


**Spool**

I made a simple cuboid spool and used the pressed fit method to join them together. Then, to ease my assembly, I glued them together.


**Plates**

Initially I plan to make the housing out of laser cuts, however my design falls short for the whole unit to stand up. I created a new one with laser cutting too after carefully measuring the longest end when the whell is spinning.

![](https://github.com/refrigerated/EP1000/blob/master/docs/images/original%20design%20of%20side%20housing.jpeg?raw=true)

## 3D Print
The only part that was 3D printed was the spool extension i used to make the split-flap display hold and hang up. It is made of two parts. One is a hollow rod that fits the shape of the motor shaft. And another was an extension shaft to fit the hole of the wheel. The two parts were then forced fit together with a clamp.

![](https://github.com/refrigerated/EP1000/blob/master/docs/extension.png?raw=true)

This is the 3D housing that I design but decided to pass. It would take too much time to print and I wasnt sure if the house dimensions are measured perfectly. I am relieved that I laser cutted the housing instead. 
<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968f05c39e96cc72d81?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>



### Arduino wiring and Code

Stepper Motor 28BYJ-48:<br/>

The 28BYJ-48 is a 5-wire unipolar stepper motor that runs on 5 volts. According to the data sheet, this motor runs in full step mode, each step corresponds to a rotation of 11.25°. That means there are 32 steps per revolution (360°/11.25° = 32).

![](https://github.com/refrigerated/EP1000/blob/master/docs/images/Stepper%20motor.png?raw=true)


ULN2003 Driver board: <br/>

The board has a connector that mates the motor wires perfectly which makes it very easy to connect the motor to the board. There are also connections for four control inputs as well as power supply connections. The board has four LEDs that show activity on the four control input lines (to indicate stepping state). They provide a nice visual when stepping.

![](https://github.com/refrigerated/EP1000/blob/master/docs/images/ULN%20driver.png?raw=true)

The wiring is quite simple for this project. When I upload the code, I should be able to control the stepper motor forward by pressing the Red button. And stops when I released it. It is programmed this way so that the user may control to display what number he chooses to show. 



```js
int Pin1 = 10;//IN1 is connected to 10 
int Pin2 = 11;//IN2 is connected to 11  
int Pin3 = 12;//IN3 is connected to 12  
int Pin4 = 13;//IN4 is connected to 13 
int switchCW  =2;//define input pin for CW push button

 
int pole1[] ={0,0,0,0, 0,1,1,1, 0};//pole1, 8 step values
int pole2[] ={0,0,0,1, 1,1,0,0, 0};//pole2, 8 step values
int pole3[] ={0,1,1,1, 0,0,0,0, 0};//pole3, 8 step values
int pole4[] ={1,1,0,0, 0,0,0,1, 0};//pole4, 8 step values


int poleStep = 0; 
int  dirStatus = 3;

void setup() 
{ 
  
 pinMode(Pin1, OUTPUT);//define pin for ULN2003 in1 
 pinMode(Pin2, OUTPUT);//define pin for ULN2003 in2   
 pinMode(Pin3, OUTPUT);//define pin for ULN2003 in3   
 pinMode(Pin4, OUTPUT);//define pin for ULN2003 in4   

 pinMode(switchCW,INPUT_PULLUP); 
 
} 
 void loop() 
{ 
  if(digitalRead(switchCCW) == LOW) 
  {
    dirStatus =1;
  }else if(digitalRead(switchCW) == LOW)
  {
   dirStatus  = 2;  
  }else
  {
    dirStatus =3; 
  }
 if(dirStatus ==1){ 
   poleStep++; 
    driveStepper(poleStep);    
 }else if(dirStatus ==2){ 
   poleStep--; 
    driveStepper(poleStep);    
 }else{
  driveStepper(8);   
 }
 if(poleStep>7){ 
   poleStep=0; 
 } 
 if(poleStep<0){ 
   poleStep=7; 
 } 
 delay(1); 

}

``` 


## Results

The result works fine. The motor can be controlled smoothly. However, the top flap of number 9 does not drop smoothly. The reason must be because of the tight fitted wheels that were glued together. I also made the mistake of placing the sticker numbers upside down. Thus the counter can only be count downwards.

## Conclusion
 I learnt a lot through this project. It is not good looking as I expected and there were a decent amount of mistakes made in measurements. I now learn that I should lower down of my expectations as there were a lot of trial and errors in my design. 

 After this, I plan to make more improvements to my design. For example, I can buy a bearing instead of having a screw jutting out, make few adjustmnets on the housing and coreect the counting to count up. I would also change the flaps to be made from pvc blank cards instead of laser cutting.

## Design files & Source Code

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968533eee3872a38526?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Flaps.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20Flaps%20design%20.f3d?raw=true)

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968dc4b930492115511?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Wheels.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20Wheels.f3d?raw=true)

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968b3f8dc6697253c59?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Spool.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20Spool.f3d?raw=true)

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd9685e17363d9778c46e?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Housing.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20housing.f3d?raw=true)

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968117cb34e93499d01?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Extended Shaft Part 1.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20extended%20shaft%201.f3d?raw=true)

<iframe src="https://ichat754.autodesk360.com/shares/public/SH56a43QTfd62c1cd968e53e823e6894216f?mode=embed" width="640" height="480" allowfullscreen="true" webkitallowfullscreen="true" mozallowfullscreen="true"  frameborder="0"></iframe>
[Extended Shaft Part 2.f3d](https://github.com/refrigerated/EP1000/blob/master/NEW%20extended%20Shaft%202.f3d?raw=true)


## References

3-letter abbreviation Split flap display by[JON-A-TRON](https://www.instructables.com/id/Split-Flap-Display/) <br/>
Prototype four-character display by [Scottbez](https://github.com/scottbez1/splitflap) <br/>
Weather forecast Split Flap by [gabbapeople](https://www.instructables.com/id/IoT-Split-flap-Weather-Forecast-Powered-by-XOD/)
