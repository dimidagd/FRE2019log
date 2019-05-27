+++
title = "Task-1 script"
tags = ["task-1"]
date = "2019-05-07"
#draft = "true"
+++

## Task 1 2018 script

* [ ] lobst and rosebot params l47:53, what does the laserserver command `rosebot` do
* [ ] inspect how $joyoverride works l:77
* [ ] setoutput for leds  l:79
* [ ] line 95 is laser initialization?
* [ ] what is the output at $l9 , l:97
* [ ] How does rowdrive work l144:168
* [ ] How turning works l171:190


you are fucked 

<details>
<summary>
Task1_code
</summary>
<p>

{{< highlight go "linenos=table, hl_lines=47-53 77 79 97 144-168 171-190" >}}
%%%%%%%%%%%%%%%%%%%%%%% task1 %%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%% DTUni-Corn %%%%%%%%%%%%%%%%%%%%%


%%%%%%%%%%%% constants %%%%%%%%%%%%%
%Startturn: -1 = right, 1 = left
sign = -1

%turn number of rows-1
turnlim=15

fwdVel = 0.7
bckVel = -0.5
slowVel= 0.6
turnVel = 0.7

fwdLaser = 1
bckLaser = 0

fwdLasX = 0.46
bckLasX = -0.17

slowdist= 11

pi=3.141592


%%%%%%%%%%%% variables %%%%%%%%%%%%%
id= 0
turncnt= 1
rowStartX = $odox
rowStartY = $odoy
turnfinishedX = $odox

Vel = fwdVel
Las = fwdLaser
lasX = fwdLasX

x=0
y=0
th=0


%%%%%%%%%%%% plugin variables %%%%%%%%%%%%%
set "$odoth" 0

laser "var lobst.minPointsInLine=8"
laser "var lobst.enableSplit=true"
laser "var lobst.minsplitcnt=2"
laser "var lobst.splitdist=0.5"
laser "var rosebot.lineAngleInterval='-105,-75'"
laser "var rosebot.lineRmax=1.0"
laser "var rosebot.holeAngleAllowed=180"

wait 1


%%%%%%%%%%%% holetypes %%%%%%%%%%%%%
% 0 = dead end
% 1 = free space
% 2 = right wall
% 3 = left wall
% 4 = hole
% 5 = ignore hole


%%%%%%%%%%%% start logging %%%%%%%%%%%%%
log "$time" "$odox" "$odoy" "$odoth" "x" "y" "th" %"$l4" "$l5" "$l6" "$l7"
laser "scanset logopen"
laser "scanset log=1"
laser "odopose log=true"


%%%%%%%%%%%%%%%%%%ini
label "ini"
wait 1
eval $joyoverride
backbut = $joyoverride
setoutput "ledMode" 32
if (backbut == 1) "ini"


fwd 0.4 @v 0.6 :($cmdtime> 25)

%%%%%%%%%%%% start %%%%%%%%%%%%%
label "start"
setoutput "ledMode" 0

eval $joyoverride
backbut = $joyoverride
if (backbut == 1) "ini"

laser "rosebot"
id = id + 1
stringcat "rosebot device="Las" laserx="lasX" id="id" backward=0"
laser "$string"
wait 1 :($l9==id)

%printing returned values
eval $l1
eval $l2
eval $l3
eval $l4
eval $l5
eval $l6
eval $l7
eval $l8
eval $l9
eval id

%if ($l4<0) "start"

x=$l4
y=$l5
th=$l6
holetype = $l7
eval holetype
eval x
eval y
eval th

if(holetype == 0) "start" %if meeting dead end

%decide which mode to enter
switch(holetype)
case 1 %free space
	if(turncnt>turnlim) "end"
	eval $odox
	%if($odox - turnfinishedX < 2) "rowdrive"
	goto "turn"
case 2 %right_wall
	goto "rowdrive"
case 3 %left_wall
	goto "rowdrive"
case 4 %hole
	goto "rowdrive"
case 5 %ignore hole
	goto "rowdrive"
endswitch

goto "start"


%%%%%%%%%%%% rowdrive %%%%%%%%%%%%%
label "rowdrive"
laser "rosebot"
xo = $odox-rowStartX
yo = $odoy-rowStartY
disto = sqrt(xo*xo + yo*yo)
eval xo
eval yo
eval disto
if(disto>slowdist) "speedcontrol"

eval $joyoverride
backbut = $joyoverride
if (backbut == 1) "ini"

label "driveon"
setoutput "ledMode" 0
laser "rosebot"
driveon x y th "rad" @v Vel :($drivendist>0.03)
goto "start"

label "speedcontrol"
laser "rosebot"
Vel = slowVel
goto "driveon"


%%%%%%%%%%%% turn %%%%%%%%%%%%%
label "turn"
laser "rosebot"
Vel = turnVel
fwd 0.4 @v 0.8 :($cmdtime > 10)
stop

eval $joyoverride
backbut = $joyoverride
if (backbut == 1) "ini"

%turn90 = sign*150
%turnV = sign*(168-150)

odoDist = $ododist
%turnr 0.53 turn90 @v Vel
%turnr 0.53 turnV @v Vel : (1)

if(sign == 1) "labell"
if(sign == -1) "labelr"



%%%%% turnr led
label "labelr"
setoutput "ledMode" 4
goto "postLed"

%%%%% turnl led
label "labell"
setoutput "ledMode" 2


label "postLed"

turnang = sign*180
turnr 0.35 turnang @v Vel
turnfinishedX = $odox
%Turn variables
turncnt=turncnt+1
sign = -1*sign
%Turn loop
label "turnR"
laser "rosebot"
id = id + 1
stringcat "rosebot device="Las" laserx="lasX" id="id" backward=0"
laser "$string"
wait 1 :($l9==id)

%Check for hole or if the robot has turned 180
if($l7 == 4) "stopR"
if($ododist < odoDist+1)  "turnR"
stop
wait 2
setoutput "ledMode" 0
fwd -0.1 @v0.3 :($cmdtime > 10)

eval $joyoverride
backbut = $joyoverride
if (backbut == 1) "ini"

goto "turnR"

%Hole found
label "stopR"
laser "rosebot"
eval $l7
eval $odoth
stop



%Set values to default
rowStartX = $odox
rowStartY = $odoy
Vel = fwdVel
goto "start"


%%%%%%%%%%%% end %%%%%%%%%%%%%
label "end"
setoutput "ledMode" 16
play "bender.wav"
fwd 0
wait 25
setoutput "ledMode" 0
stop
%stop logging
eval rowStartX
eval rowStartY
eval xo
eval yo
eval disto

laser "scanset logclose"
laser "odopose log=false"

{{< / highlight >}}
</p>
<!--
[^1]: I am the footnote
-->
