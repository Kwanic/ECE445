2/10/2026
First Meeting

We met together and discussed the project. Zhikuan proposed this bilateral PPG measurement device idea since he works in a research lab that could use this device. The TA suggested that we use individual LED+photodiode and ADC components. The complexity might be too low if we selected a chip that packaged those two together. 

2/12/2026
Proposal Doc

We met together and wrote the proposal doc. The problem: measure the time difference in pulse arrival time between each earlobe. This could be an important biomarker for things like stroke or any vascular problems. Goals for our design: high precision, high sample rate, skin interfaced, calculate the time differernce accurately, transmit the data to a laptop/phone where it can be plotted and analyzed.

 Our design idea is 2 ppg boards to measure heart rate in each earlobe and one ecg board used as reference. Each board will have 3 subsystems: battery/power distribution, microcontroller, and sensing. The parts we plan to use are shown in BlockDiagram1.png

We plan to use BLE to transmit the data from the microcontroller to the laptop/phone device. We will use MAX86140 driver for the SFH7050 LED and PPG data acquisition and we will use MAX30003 for the ECG data acquisition. They will interface with the nrf52850 microcontroller using SPI. Finally the microcontroller will use external crystals for precise timing and communicate using an external antenna.

2/15/2026
PCB schematic design

We mainly looked up all the compenents we wanted to use and how to interface with them and set up them. The nrf52840 datasheet has all the extra connections needed to make it work: decoupling capacitors, pin assignments, where to wire crystals, how to conennect antenna, etc. We plan to have 2 power levels: 3.7V from battery to power LEDs and buck converter, 1.8V from buck converter to power nrf52840 and MAX chips.


nrf52040 specification:
https://docs.nordicsemi.com/bundle/ps_nrf52840/page/keyfeatures_html5.html 
MAX86140 datasheet:
https://www.analog.com/media/en/technical-documentation/data-sheets/max86140-max86141.pdf
MAX30003 datasheet:
https://www.analog.com/media/en/technical-documentation/data-sheets/max30003.pdf

2/16/2026
Breakout boards for the breadboard demo

Since our chips don't have development kits, we had to make our own breakout boards. Zhikuan and I designed the pcb for several of our breakour boards. Image of the max86141 breakout: MAX86141_breakout.png.

2/22/2026 
nrf52840 breakout board

I did this breakout board. Was a lot more complicated with all the resistors, inductors, and capacitors needed.

2/24/2026
PCB ordering

Sent all the breakout boards to be manufactured

2/25/2026
Design Doc

Just basically rewrote the proposal and put some high level and subsystem requirements on it.
PPG signals need to be sampled at at least 50Hz for heart rate data. Since we want sub millisecond accuracy, we need around 1024 Hz. ECG signal sampled at around 512Hz

2/28/2026 
PPG and ECG boards

Finished both of our PCB and sent them in for fabrication

3/9/26
ECG board arrived and soldered

We worked a lot on programming. We started with a MATLAB program for data collection since it would be easiest to connect over BLE and read and plot the data. The nrf52840 program is a simple one that establishes a BLE service, reads data from the MAX30003 chip using SPI from the FIFO buffer, and transmits the data over BLE. It also sets certain parameters such as sample rate over the SPI.

3/15/2026
Design change: PPG from 2 separate boards to one singular board

We realized that the BLE cannot send a signal at exactly the same time to all the boards. This makes syncing them very difficult. To combat this, we collect both PPG data on one microcontroller so that the time difference is established. Could add small error due to SPI transaction 4MHz*128bits = 32 microseconds which is low enough.

3/16/2026
New ECG board

Added a usbc connection for battery charging and improved the layout for better signal acquisition

PCB issues - PPG boards never arrived, sent out an order for the new 1 board PPG and the new ECG board. Not much we can do while waiting for the boards to arrive.

3/29/2026 
Boards arrived

PCBs arrived and soldered.
PPG1board.png
Now we just have to work on coding the board.

3/31/2026
Programming board

We have issues downloading our code on to the board. Thought it was a soldering issue so we had to debug connections. 

4/2/2026
Fixed soldering

We were able to get BLE and SPI working. We can turn the LED's on over BLE but have issues transmitting the data back over BLE. Either data isn't being collected or we aren't reading the data properly.

4/13/29
Still debugging the PPG board

We can read data back from the board but it is very noisy. We believe it is an issue with the PCB design and the snaked wires. Since the wire length is so long, it collects a lot of noise from surrounding EMF interference. This is made worse since we are transmitting an analog signal along the long wire instead of a digital signal. 

We see that it can properly respond to a flashlight and the ADC maxes out on it's range when a flashlight is shined on the photodiode. When we try on human skin, there is an increase in the voltage readings but we cannot see a proper ppg waveform.

Further testing will be done to see if we can change any settings on the MAX86140 chips to try and filter the noise better. Will also try to write a python script to filter the data and hopefully recover a proper ppg waveform

4/16/2026
New PPG board

We tried many different settings and filters and have concluded that it was a PCB design issue that we could not fix. Therefore we designed a new PPG board to remove the snaked wires and move the ADC as close to the photodiode as possible as to reduce noise as much as possible.

4/18/26
ECG board

We got the ECG board code + website for data acquisition plotting working. This worked pretty much on the first try which was great. ECG works when touching electrodes with fingers. Waveform shows well. We haven't tried with actual electrodes on the chest yet. Now we are mainly waiting for the PPG board to arrive

4/23/2026
PPG board arrives

Final iteration of the PPG board arrived. We soldered it and downloaded the previous code we had. There was a lot of debugging since it wasn't working properly. The MAX86140 chip wafer level package made it very hard to solder properly and verify that it was soldered properly. 

We got the program downloaded, BLE connection works, SPI works.
We can turn the LED on over BLE and start data acquisition, the problem is that the data being transmit back over BLE is all 0s.

For debugging we tried using the multimeter to continuity check the PCB but everything seemed fine. We weren't able to check the pins underneath the MAX86140 chip since it was a wafer level package 4x5 pin array. 
The data did not show any reaction when the photodiode was shined with a flashlight.
We tried replacing the SFH7050 LED+Phododiode with a basic photodiode to see if this would change anything. When the new photodiode was shined with a flashlight, the data still read all zeros. Due to this we believe it is an issue with the MAX86140 chip.
We tried debugging the software by reading and printing out all the register values but this did not reveal any issues.
We tried replacing the MAX86140 chip but this did not change anything.
No matter what we tried, the data being transmit was all zeros.

4/26/2026
Preparing for final demo and final presentation

Soldered electrodes onto the board to measure heart rate waveform from the chest. 3 locations: upper left, upper right, lower left.

Works well on chest, requires person to not move or breath to get a perfect waveform. Otherwise noise from breathing is very large in magnitude and obscures the proper signal.

Implemented a program for peak detection to measure heart rate from the ECG waveform.


The state of our boards/program:
    ECG board works and transmits proper data. Waveform is displayed and looks very clean
    PPG board without snaked wires can be turned on, but data cannot be read
    PPG board with snaked wires works properly and data can be plotted. Issue is that noise obscures the waveform and there is no filtering that can reveal the ppg waveform.

No more design changes after this, just compiling data for the final presentaiton and report