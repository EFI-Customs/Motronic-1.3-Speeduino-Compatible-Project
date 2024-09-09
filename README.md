# Speeduino Compatible BMW ECU's

This is the main repository containing base maps for various BMW engines utilising the Speeduino Compatible PCB's from EFI Customs.

As well as this, you will find (eventually) documentation on building your own PCB's.


# Base Maps

It is important to note that these base maps are not intended to be a "ready to go" tune. 

These are purely used as a starting point to get your project going without having to go through the time-consuming process of tweaking every setting.

It is also important to verify your timing with a light before you crank your engine with injectors firing, otherwise you may cause some damage to your motor (not good!). 
These base maps have been preset to be 50deg ATDC (After Top Dead Centre, as opposed to the typical Before Top Dead Centre values used in other kinds of standalone ECU's), which seems to be a typical expected value on a stock M20 running factory single ignition coil. 

Lastly, these base maps have been built under the assumption that a MAX992x based VR Conditioner for the crank sensor. As such, the Trigger Edge is set to RISING. If you are running another type of VR Conditioner or you've converted to Hall Effect then please ensure you have checked this setting before cranking. Remember that if you have switched over to a Hall Effect crank sensor then you need to change jumper configuration on the PCB. There is a jumper configuration table located on the silkscreen underneath where the Arduino mounts.

You can also achieve your own base map by using the Speeduino base maps found here: [Official and user contributed base tunes for Speeduino installations](https://github.com/speeduino/Tunes)

## Motronic 1.3 General Information & Observations
These instructions are designed for the V1.7 Motronic 1.3 Board.
For the Motronic 1.3 compatible ECU, please ensure you have done;

### Air Flow Meter (AFM)
Assuming you are keeping your AFM, unplug the AFM connector and add wires to pins 1 & 4 on the plug and connect them to pins 1 & 4 on the AFM. 
Doing so allows us to utilise the factory intake air temperature sensor. 

The reason we need to unplug pins 2 & 3 is because they go to the External MAP and +5V line on the ECU pins 7 and 12 respectively. 
If you do not do this, you will find that your MAP reading will halve, and you will experience signal noise.

If you intend to delete the AFM, you can instead run any generic intake temperature sensor. A very popular one is an Open Element GM IAT sensor. Again, you can just wire this sensor into the factory AFM harness (pins 1 & 4).

### TPS
If you want to start the car without re-wiring the TPS, simply unplug it and manually enter values in the TPS calibration window to spoof readings. 
However, on a full implementation of this board it is highly recommended to convert to an M50 style TPS.

To convert your TPS to the M50 one you need to do the following:

 - Acquire M50 TPS (Part number 13 63 1 721 456)
 - Acquire M50 TPS Adapter 
 - Swap pins 1 (Brown/Blue) and 2 (Brown) on the factory TPS Connector

### Ignition

If you are running factory ignition and you have the cylinder 1 pulse sensor attached (image link here: https://www.r3vlimited.com/board/filedata/fetch?id=7225671) you need to unplug the sensor from the engine harness. 

The Motronic PCB's support individual coil-on-plug for up to 4 cyl engines (needs Cam Position Sensor retrofitted) and wasted-spark for 6-cyl engines. To retrofit a cam sensor you can re-use the cylinder 1 pulse sensor plug on the engine harness side. A MAX9926 VR Conditioner accepts two inputs (crank + cam).

The principle remains similar for both platforms, but we will cover the most common Wasted Spark conversion for an M20.

There are multiple approaches to converting to Wasted Spark, we'll cover the two easiest:

#### Method 1 - Retain the factory coil wiring (black wire and green wire)
The Motronic 1.3 PCB has an external ignition connector for additional channels, for this method you need to add two wires that go from the Ign 2.1 and 3.1 channels on the connector to the respective coils. 
In total, you will have three ignition channel wires and one +12V (green) wire.
The factory black wire will act as channel 1 ignition, 2.1 (PCB) is channel 2 and 3.1 (PCB) is channel 3.

#### Method 2 - Utilise the external connector exclusively
As the title suggests, we either remove or hide the factory black ignition wire and run the 3 channels exclusively from the external ignition connector on the ECU PCB.

Channel 1.1, 2.1 and 3.1 on the PCB are channels 1,2,3 respectively. 
With this method, we still retain the factory green coil wire to provide switched +12V.

#### Ignition wiring continued
We have used a GM V6 Coil Pack with a gutted and re-wired DFI module.

Reference Delphi Coil Pack Part Number: GN10123

Reference Delphi DFI Module Part Number: CM414

Reference DFI Module wiring guide: https://www.e30zone.net/e30wiki/images/9/9a/Gmgutted.jpg

Another wiring reference image: http://i.imgur.com/Q9WdHVS.png


Use a multimeter to ensure you have connected the respective wires to the appropriate pins on the DFI Module.
+12V should be a shared pin across all three pairs of pins (usually the left pin on each pair)
Then channels 1-3 will be individual pins (i.e. not shared).

Label your coil packs with the cylinders they will supply spark for.

- Channel 1 -> 1-6
- Channel 2 -> 2-5
- Channel 3 -> 3-4

Install the DFI module and run some new ignition leads to your spark plugs.
Connect the new wiring from the DFI module into your Motronic PCB External Ignition Connector.
#### TunerStudio settings
With wasted spark installed, you will need to change a few things in your tune file.

Firstly, you need to go into `"Settings" -> "Trigger Setup"` and change the `Trigger Angle` from `50` to `276`. The M20 has a trigger angle of 84 deg BTDC. 
360 - 84 = 276. **This trigger angle is a starting reference point and shouldn't be your final value until you have verified this with a timing light!!** 
Our 2.9 stroker M20 reads 88 deg BTDC which equates to 272 ATDC trigger angle.

Secondly, go to `"Spark" -> "Spark Settings"` and change `"Spark Output Mode"` to `"Wasted Spark"`.
That's it! Wasted Spark is now ready to go.

### Injection
The factory injector wiring is designed to run in a batched manner. Unfortunately, the Speeduino firmware does not support batched injection. To make this work our base maps use untimed or simultaneous injection. 
This isn't perfect and will likely run rich. Tuning this also becomes rather difficult. 
The best approach for this is to convert our injector wiring to a paired setup.

Thankfully, this can be done in such a manner than you can reverse it. 
I don't intend to advertise the below method as being the perfect option, but it was an option that suited our needs to be able to revert injector wiring as per requirements.
Using a Series 1 E30 M20 injector harness, we have split all wires apart.
Then we run each brown/yellow (cyls 2,4,6) and brown/white (1,3,5) wire to a 6 plug Deutch connector labelling them 1 through to 6 respectively. Use a multimeter to verify which wire goes to which injector.
We then also separate the temperature sensor wires and wire them into a 4 plug Deutch Connector.

The engine harness needs to have a 4 pin Deutch connector wired to the temperature sensor wires, and another 4 pin Deutch connector for Injector Channel 1, 2, 3 and  injector ground.

Time to make a little adapter! 
Grab a 6 plug Deutch Connector and wire it the same as the previous 6 plug Deutch connector that now houses the injector wires. 
Now on the other end we will merge cylinders 1 & 6 to channel 1, cylinders 2 & 5 to channel 2, and cylinders 3 & 4 to channel 3 and wire them into a 4 plug Deutch connector with the same layout as the engine harness plug. Finally, grab all the injector ground wires and merge them into one thick cable (We used 12AWG wire to achieve this) and wire it into the last open port on the 4 plug Deutch connector.

![image](https://user-images.githubusercontent.com/119147626/204684690-f8ce2eee-9a6a-4b58-8c86-ba1dd6574aa2.png)

Do the same for the temperature sensors.

Your third channel injector wire will need to be neatly tucked into the factory harness and brought back to the ECU where it will connect into the 3rd Channel output (bottom left) on the External Injector Connector on the PCB.

Now you have a dynamic injector harness that you can swap the setup as per your requirements.
In our case, we have two adapters for paired and batched. 

![image](https://user-images.githubusercontent.com/119147626/204685290-036e6773-328a-487f-b7bb-91d2e83cb272.png)


### MAP/Vacuum
Run a vacuum hose from the intake manifold to where your ECU is installed. You can get a T-Piece adapter and run the vacuum hose from between the intake manifold and the fuel pressure regulator.

### Wideband
Install a wideband controller and gauge. One we have used many times before is the Innovate LC-2. This comes with the controller, sensor, gauge and supports two programmable sensor outputs.

Refer to the instruction manual for installation and wiring or inquire with your preferred auto electrician.

For our installation, we have grounded our sensors to the ECU (using the expansion pads located underneath the Arduino) and re-programmed the LC-2's second sensor output to Wideband and wired it to the middle-bottom pin of the black IO connector on the Motronic PCB.
