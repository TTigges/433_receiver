### Introduction

We are receiving signals from the TPMS sensors of our (Fiat) Abarth 124 Spider TPMS sensors. It’s a bit of a unique case because the 124 Spider are actually built by Mazda on the base of the MX-5. However, only the Abarth models seem to have active TPMS sensors. Unfortunately, the car does not display the sensor infos. I am testing with a set of tires sitting inside (controled environment, temperature and pressure are known) as well as a set of tires mounted on the car with copied sensors. I also got codes from friends, also recorded with rtl_433.


### Auto suggested profile/protocol

With _-G_, rtl_433 suggests the sensors are Jansite sensors but the values are off. So from this, I assume the signals are similar/having a similar preamble? Or how is the fitting device protocol selected? _(More below at **First try at a device protocol** and **What I don't understand.**)_

Where Jansite’s code is
II II II IS PP TT CC

my code seems to be
?? ?? II II SS PP TT


### Received codes and first notes:

Right now, we have checked 3 cars with 4 sets of tires total = 16 sensors.

**Set A 1**

- 0f54771bb79045
- 0f5476e8b78f45
- 0f5476eab79140
- 0f547711b79043


**Set A 2**

- 0f54771bb49835
- 0f5476e8b6ac35
- 0f5476eab5ab35
- 0f547711b48e35

Notice the same IDs (byte 3 + 4). Byte 5 (expected status) behaves a bit weird, though.


**Set B**

- 0f365c01078d40
- 0f365beb079141
- 0f38cb2f078e42
- 0f365c1a079043

Here, the 5th byte is uniform (07) again. I was surprised that the 2nd byte is different for one of the sensors.


**Set C**

- 0c15590367903b
- 0c2990b667913d
- 0cbf53cb679e3c
- 0f492f8367923c

The 5th byte is uniform here, too. But the first 2 bytes are a mess here. I’m surprised by the „0c“.


### BitBench:
http://triq.net/bitbench?c=0f54771bb79045&c=0f5476e8b78f45&c=0f5476eab79140&c=0f547711b79043&c=&c=0f54771bb49835&c=0f5476e8b6ac35&c=0f5476eab5ab35&c=0f547711b48e35&c=&c=0f365c01078d40&c=0f365beb079141&c=0f38cb2f078e42&c=0f365c1a079043&c=&c=0c15590367903b&c=0c2990b667913d&c=0cbf53cb679e3c&c=0f492f8367923c&f=%3F%3F%3A%208h%20%3F%3F%3A%208h%20ID%3A%208d%208d%20STATUS%3A%204h%204d%20PRESS%3A%208d%20TEMP%3A%208d


### Excel list of sensors and decoded values:
https://docs.google.com/spreadsheets/d/1ZGbVTRssBKqKV_I3kSD5BuykL3l_xucRNVISEH8U45A/edit?usp=sharing


#### Known values:

I made a lot of tests with set A1 with known temperatures and known pressures and found that the last (7th) byte in decimal – 50 is exactly the temperature.
I also noticed that the 2nd to last (6th) byte changed with the pressure. From several runs from 0.5 to 2.5 bar I found that the value as decimal * 1.4 was the pressure in kPa (+/- 1 – but that could also be my compressor or pressure tool that’s 1 kPa off).


#### Unknown values:
Since that’s all that I really need, I’d be happy with that and just call the 3rd and 4th bytes the IDs, but here’s what I’m wondering:
Sets B and C were received during driving. Similar to A 1, the 2nd nibble of the 5th byte is 7. Only A 2 is off there. Set A was received during rapid pressure change.
In one case while receiving values during rapid pressure changes I received a signal from one of the sensors mounted on the car outside and the 5th bit was 74 where it was b4 before during filling.


#### Thoughts about IDs:
There is also a dealer that is shipping these sensors „pre coded“, implying all cars have the same code (which I’ve never seen, actually, and can’t really believe). That would also imply that it doesn’t matter which wheel/corner the sensors are put at. That would make sense if the car just received any signals without caring about the position – as it doesn’t show any info besides „Flat Tire Warning“ anway.


#### Recoded signals:
https://github.com/merbanan/rtl_433_tests/pull/329
https://docs.google.com/spreadsheets/d/1ZGbVTRssBKqKV_I3kSD5BuykL3l_xucRNVISEH8U45A/edit#gid=529440585


#### First try at a device protocol:
Since the sensors where first detected as Jansite TPMS, I used a copy of it to get quick results:
https://github.com/TTigges/rtl_433/blob/master/src/devices/tpms_abarth.c
Comments are still for  the Jansite TPMS, though. (Needs to be fixed!)


#### What I don’t understand, yet:
In audacity, the signal looks totally different to that of the Jansite TPMS. And I don’t recognize any preamble. I looked at this issue on here about the Jansite TPMS (https://github.com/merbanan/rtl_433/issues/1007) and if I understood some oft he comments correct, other signals with missing CRC were suggested as Jansite TPMS, as well.
So, ist he signal suggested to be a Janiste TPMS sensor based on the missing/unknown CRC and not because of the preamble?


#### Final goal: Bringing this to CC1101 on Arduino:
In the end, the goal ist o read the signals via CC1101 on an Arduino. Similar to https://www.hackster.io/jsmsolns/arduino-toyota-uk-tpms-tyre-pressure-display-b6e544 and https://www.hackster.io/jsmsolns/arduino-renault-tpms-tyre-pressure-display-dfb548. @JSMSolns
I’m diffing the files and comparing them to my codes.