# Jetronic to Motronic Conversion Guide

This guide is not exhaustive nor definitive, and is aimed for people who have experience with automotive electronics and fabrication.

Please note the conversion from Jetronic to Motronic in this guide does not use factory ignition or injection and you will need to make up your own harnesses for these components.

## Requirements
- High impedance injectors - Jetronic injectors are low impedance and are not directly compatible with most standalone ECU's.
- Crank pulley trigger conversion kit - the factory harness uses two crank position sensors in the bell housing which are not compatible
- Motronic 1.1/1.3 crank position sensor
- Motronic 1.3 female connector (side that connects to the ECU) with fly-leads
- Aftermarket air temperature sensor
- Motronic 1.3 coolant temperature sensor
- **Optional** 3bar fuel pressure regulator upgrade


## Factory wiring
To make this conversion work, we will de-pin or cut wires from the jetronic ECU connector and join them to the M1.3 ECU connector.

Refer to the [ECU Pinouts - E30 Zone Wiki](https://www.e30zone.net/e30wiki/index.php/ECU_Pinouts) for tables and connector diagrams of Jetronic and Motronic 1.3.

The following table will outline Jetronic connector pin numbers and their respective M1.3 connector pin destination

| Description                       | Jetronic Pin | Jetronic Colour | Motronic/Destination Pin |
| --------------------------------- | ------------ | --------------- | ------------------------ |
| Tachometer                        | C104 Pin 1   | BL/BU           | 6                        |
| Switched power from main relay    | 9            | RD/WT           | 37                       |
| AFM Air temp - pin 1*             | 8            | GR/VI           | 44                       |
| Coolant temp input                | 10           | BR/RD           | 45                       |

*We will also be disconnecting the AFM and plumbing an aftermarket intake temperature sensor. You may optionally delete the AFM entirely.

You will also need to join all of the ground wires accordingly.

## Custom wiring

### Main Relay + Fuel Pump Relay

Since the Jetronic system does not have a main relay, we will need to wire one in.
There's a few different ways to do this but we will simplify it.

Using the [DME - E30 Zone Wiki](https://www.e30zone.net/e30wiki/index.php?title=DME#Operation) article we can get the neccessary wiring information for the main relay.
In short:
- DME Relay pins 30 and 86 are to be connected to constant, unfused 12V
- DME Relay pin 85 goes to pin 36 of the M1.3 harness
- Ignition being turned on should provide 12V to pin 27 on M1.3 harness, which should ground pin 36 on M1.3 harness thus activating the DME relay
- DME Relay pins 30 and 87 bridge upon activation, supplying power to the fuel pump, injectors and to pin 37 of the M1.3 harness (which we have outlined in the previous table)

With all of this in mind, to simplify the setup we don't need:
- Switched 12V to pin 27 on M1.3 harness
- No grounding of pin 36 on M1.3 harness
- Main relay powering the fuel  (though you can do this if you wish)

We can also remove the old 7 pin fuel pump relay, and re-use some of the wires for our new relays.
Namely, we can cut the following wires from the 7-pin relay:
- pin 15 - Switched 12v
- pin 31 - Ground
- Pin 30 - Unfused 12v
- Pin 87 - 12v to fuel pump

Using the above wires, we can make up the main relay using pins 15, 31 and 30 to pins 85, 86 and 30 respectively. Then a new wire going from pin 87 on the relay to pin 37 on the M1.3 harness and a split that provides injectors with a common 12V. Optionally, you may also use another split from DME relay pin 87 to activate the fuel pump relay.
<img width="939" alt="maain-relay" src="https://github.com/user-attachments/assets/bb969703-e01d-435d-9874-10142dcfc6f3">

Likewise with the fuel pump relay, we can make up relay using pins 15, 31 and 30 to pins 85, 86 and 30 respectively.
Lastly, wire pin 87 from the 7 pin relay to pin 87 on the new relay to provide the fuel pump with 12v.
<img width="841" alt="fuel-relay (1)" src="https://github.com/user-attachments/assets/84edcfc5-3946-479c-82fe-97e204812cfc">


### Air Temperature Sensor

We will need to plumb an air temperature sensor into the intake system. The easiest rule of thumb to stick to, is to plumb the sensor approximately where the AFM is located.
Majority of aftermarket sensors work on the same principle of resistance comparison from a 5v reference. Pin 8 from the jetronic harness will go into the signal pin on the temperature sensor, the ground pin on the sensor can be supplied from a ground wire on the engine harness. Note that pin 8 on the jetronic harness goes to pin 1 on the AFM connector.

### Coolant Temperature Sensor
This is a simple replacement procedure which requires the blue coolant sensor for a M1.3 system.

### Crank Trigger wheel

The trigger wheel will require welding on to the crank pulley. You will then need to install the crank sensor mount to the engine block, and fasten the crank position sensor to it. Run the CPS wire along the front timing cover to underneath the intake manifold.

Below is a reference image of where to position the trigger wheel relative to the original timing marks where the gap in green signifies the missing teeth on the trigger wheel.
<img width="844" alt="image" src="https://github.com/user-attachments/assets/d5455021-abd7-4d0b-8260-57fc0c91b0c2">


### Crank position sensor

Using some 2 core shielded wire:
- Wire up to a 3 pin EV1 connector to interface with the crank position sensor
- From the EV1 connector, join the wire to the Motronic 1.3 harness as outlined in the table below

| CPS Pin | Description | Wire Colour | Motronic 1.3 Pin |
| ------- | ----------- | ----------- | ---------------- |
| 1       | VR +        | Black       | 47               |
| 2       | VR -        | Yellow      | 48               |
| 3       | Shield Gnd  | N/A         | Gnd              |

### Injectors

There's many to choose from and this greatly varies on the application and requirements of the vehicle.
For vehicles maintaining natural aspiration can use M20B25 blue injectors, or M50B25 green injectors. For those intending to squeeze out more power (such as high compression strokers) may look at M52B28 pink injectors as a viable option.

All of the aforementioned injectors maintain the same dimensions as the stock injectors and directly clip into the fuel rail requiring no further modification.

To maintain OEM-like wiring layout, you can feed injectors with 12V from the main relay. The trigger/signal wires can be run directly to the ECU's external injector connector.

### Ignition

Much like the injector wiring, the ECU has an external connector for ignition trigger/signal wires.

Depending on which ignition coils you choose to utilise, the wiring may vary slightly. But generally, you will need to provide a switched 12V feed to the coil pack/packs and a signal input from the ECU's external ignition connector. Some coil packs may require additional grounding. In most cases, grounding to the rocker cover is acceptable.
