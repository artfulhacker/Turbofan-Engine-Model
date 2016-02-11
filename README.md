#Turbofan Engine Model (.NET)

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1200.jpg?raw=true)

About the Project

In order to accurately output the correct thrust, thrust specific fuel consumption (TSFC), engine noise, and CO2 emmisions throughout the flight in our senior aircraft design project: design and build a working aircraft with 100% code, an acurate engine model had to be designed. This was accomplished by analyzing a non-afterburning turbofan without aid of ideal assumptions. Calculations were performed at each stage of the cycle: freestream, ram compression, deceleration, fan, fan exhaust, compressor, combustor, turbine, core nozzle, and core exhaust. Once these baselines were established, the model was refined to meet the specifications of high bypass current generation turbofan engines. With these modifications, the code produced thrust and TSFC values that were comparable to many current engine designs. Finally, the bypass ratio and weight equations were fitted to match that of the selected engine, the Pratt and Whitney PW1000G geared turbofan. Using this method also allowed the engine to be scaled to create a fan diameter based on any thrust requirement at any condition, including the maximum needed to complete the mission.

#####Special Note: My terrible professor at ASU (Dr. Wells) said that it was more accurate to use engine data she stole from a 1960's low bypass ratio engine then to use math to calculate the SFC value for a high bypass ratio engine. So all I can suggest is use this at your own risk.

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1203.jpg?raw=true)

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1202.jpg?raw=true)

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1204.gif?raw=true)

Object-Oriented Turbofan Engine Model (.NET)

```csharp
   1:  using System;
   2:  using System.Text;
   3:  using Engine_Real_TurboFan;
   4:  using Fuel_Jet_A;
   5:   
   6:  namespace ConsoleTest
   7:  {
  8:      class Program
  9:      {
  10:          static void Main(string[] args)
  11:          {
  12:              ////// SETUP FUEL
  13:              using (Jet_A fuel = new Jet_A())
  14:              {
  15:                  ///// LOAD ENGINE
  16:                  using (Real_TurboFan a = new Real_TurboFan(30000, 0.8, fuel.hpr, ... 0.9))
  17:                  {
  18:                      ///// INSERT CODE HERE
  19:                  }
  20:              }
  21:          }
  22:      }
  23:  }
```

There are many inputs required to run the engine model. First, the fan pressure ratio and compression ratio must be found. For many engines these values are available online. The ratio of specific heats for both the cold air and hot air must also be known, along with their constant pressure specific heat. These values are used to find the temperature and pressure ratios across each stage. Polytropic factors, which are the ratio of ideal to adiabatic work, for the compressor, turbine, and fan are also required. With the real assumption, comes losses in efficiency due to friction and incomplete combustion. These are shown in the mechanical efficiency, nozzle efficiency, and combustor efficiency. The properties of the fuel must also be added. Hydrocarbon fuels vary in their carbon content and chemical composition. The fuel inputs of flame temperature and heating value are dependent on this composition. Finally, the mach number and freestream temperature are needed. These values vary throughout the mission and are fed into the engine model at each iteration.

```csharp
   1:          //// Input Values
   2:          private double M_0;            // Free Stream Mach Number
   3:          private double alpha;          // Bypass Ratio
   4:          private double pi_c;           // Compression Ratio
   5:          private double pi_f;           // Fan Pressure Ratio
   6:          private double h;              // Current Engine Altitude [ft]
   7:          private double pi_dmax;        // Max Pressure Ratio During Deceleration
   8:          private double ec;             // Polytropic Factor, Compressor
   9:          private double ef;             // Polytropic Factor, Fan
  10:          private double et;             // Polytropic Factor, Turbine
  11:          private double eta_b;          // Combustion Efficiency
  12:          private double eta_m;          // Mechanical Efficiency
  13:          private double pi_n;           // Nozzle Efficiency
  14:          private double P0P7;           // Pressure Ratio Free Stream and Core
  15:          private double P0P9;           // Pressure Ratio Free Stream and Fan
  16:          private double hpr;            // Fuel Heating Value [BTU/lbm]
  17:          private double T0_4;           // Flame Temperature [R]
```

To validate these values, many engines were run through the engine model and their outputs were studied to find the correct inputs. Detailed reports of the TFE 731[4] and GE-90[5] were used for efficiency and weight studies. The efficiencies and polytropic factors were based off the GE90 data. General Electric provided a comparison of six engines, from GE, Pratt and Whitney, and Rolls Royce. Their charts showed overall pressure ratios, bypass ratios, sea level thrust, cruise thrust, fan diameter, engine length, TSFC, and weight. These values were instrumental in refining the model to accurately predict engine performance. The weights and lengths the document provided were used to generate a trend fit to be applied to any engine given the input of thrust at sea level. With the inputs refined, the engine was run using Jet-A fuel at the takeoff and cruise configurations for a comparison to show validity. The three engines that were best defined within the GE90 document were selected for the comparison, and shown in the table below. The bypass ratios, pressure ratios, and fan diameters were matched to their respective engines for the test. 


| Engine  | Takeoff Thrust  | Takeoff TSFC  | Cruise Thrust  | Cruise TSFC  | Dry Weight |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| GE - GE90-85B | 84,700 | 0.324 | 17,500 | 0.52 | 15,596 |
| TurboFan Model | 86,835.1805 | 0.3194 | 16,636.3441 | 0.5213 | 15,992.0173 |


| Engine  | Takeoff Thrust  | Takeoff TSFC  | Cruise Thrust  | Cruise TSFC  | Dry Weight |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| PW - PW4098 | 98,000 | 0.391 | 14,000 | 0.581 | 16,500 |
| TurboFan Model | 93,788.3999 | 0.3842 | 15,295.1496 | 0.59407 | 14,080.8210 |


| Engine  | Takeoff Thrust  | Takeoff TSFC  | Cruise Thrust  | Cruise TSFC  | Dry Weight |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| RR- Trent 772-60 | 71,100 | Unknown | 11,500 | 0.565 | 10,550 |
| TurboFan Model | 72,295.6767 | 0.3457 | 11,823.5721 | 0.6100 | 10,558.1127 |



This shows the engine model accurately predicts each of these engine configurations at multiple conditions, thus validating the engine code. Further, when run at a bypass ratio of 10, which corresponds to the engine we selected, the TSFC value matches that of the value found by Virginia Tech's aerospace department. 

The figure below shows the engine thrust accurately scales, as different fan diameters are input into the model and the resulting thrust is output. 

T=0.02449d^3+ 2.939d^2 

Engine Model Thrust [lb] with input of fan diameter [in] curve fit equation
Note: this is derived from output, it is not within the code to create the thrust.

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1206.jpg?raw=true)

To obtain the values, calculations were performed at each stage, following the air's path through the engine from start to finish. Before air reaches the engine, it goes through two processes: ram compression and deceleration. This occurs as the air compresses and slows down before it reaches either the fan or the compressor. The physics of the air during this phase is dictated by Mach and ideal gas relations. The engine model tracks the pressure and temperature at these points. It is at this junction where the air can take two different paths. A large mass of air can be slightly accelerated by going through the fan, or a smaller mass of air can be greatly accelerated by passing through the core. The ratio between these two openings is the bypass ratio.

```csharp
   1:          //// Conditional Input Values
   2:          private double a_0 = 0;            // Ambient Speed of Sound [ft/s]
   3:          private double T_0 = 0;            // Free Stream Air Temperature [R]
   4:          private double P_0 = 0;            // Free Stream Air Pressure [lb/ft^2]
   5:          private double Rho_0 = 0;          // Free Stream Air Density [slugs/ft^3]
   6:          private double Rho_SL = 0;         // Free Stream Air Density at Sea Level [slugs/ft^3]
   7:   
   8:          //// Output Values
   9:          public double U_7 = 0;            // Core exhaust Speed
  10:          public double U_9 = 0;            // Fan exhaust Speed
  11:          public double F_s = 0;            // Specific Thrust
  12:          public double TSFC = 0;           // Thrust Specific Fuel Consumption
  13:          public double SLTFSC = 0;        // Thrust Specific Fuel Consumption at Sea Level
  14:          public double eta_p = 0;          // Propulsive Efficiency
  15:          public double eta_t = 0;          // Thermal Efficiency
  16:          public double eta_o = 0;          // Overall Efficiency
  17:          public double f = 0;              // Fuel to Air Mass Flow Ratio
  18:   
  19:          //// Private Scientific Constants (READ ONLY)
  20:          private double g = 32.2;                   // Gravity [ft/s^2]
  21:          private double gc = 1.4;                   // Ratio of Specific Heats [cold air](c_p/c_v)
  22:          private double gt = 1.33;                  // Ratio of Specific Heats [hot air](c_p/c_v)
  23:          private double cpc = 0.24;                        // Cold Air Specific Heat [BTU/(lmb-R)]
  24:          private double cpt = 0.276;                       // Hot Air Specific Heat [BTU/(lmb-R)]
  25:          private double Rc = (1.4 - 1) / 1.4 * 0.24 * 778;    // Gas Constant, Cold Air, (BTU=778) -> [(ft-lbf)/(lbm R)]
  26:          private double Rt = (1.33 - 1) / 1.33 * 0.276 * 778; // Gas Constant, Cold Air, (BTU=778) -> [(ft-lbf)/(lbm R)]
  27:   
  28:          //// Private Internal Engine Data (READ ONLY)
  29:          private double tau_r;           // Ram Temperature Ratio
  30:          private double pi_r;            // Ram Pressure Ratio
  31:          private double eta_r;           // Ram Efficiency
  32:          private double T0_2;            // Stagnation Temperature @2
  33:          private double P0_2;            // Stagnation Pressure @2
  34:          private double pi_d;            // Deceleration Pressure Ratio
  35:          private double T0_3;            // Stagnation Temperature @3
  36:          private double P0_3;            // Stagnation Pressure @3
  37:          private double tau_c;           // Compressor Temperature Ratio
  38:          private double eta_c;           // Compressor Efficiency
  39:          private double pi_b;            // Ideal Combustion
  40:          private double P0_4;            // Combustion Pressure @4
  41:          private double tau_b;           // Combustion Temperature Ratio
  42:          private double tau_lambda;      // Overall Temperature Ratio
  43:          private double tau_f;           // Fan Temperature Ratio
  44:          private double M_9;             // Fan Exit Mach Number
  45:          private double eta_f;           // Fan Efficiency
  46:          private double tau_t;           // Turbine Temperature Ratio
  47:          private double T0_5;            // Turbine Stagnation Temperature
  48:          private double P0_5;            // Turbine Stagnation Pressure
  49:          private double pi_t;            // Turbine Pressure Ratio
  50:          private double tau_n;           // Nozzle Temp Ratio
  51:          private double P_7;             // Exit Pressure = Free Stream Pressure
  52:          private double P0_7P7;          // Stagnation to Static Pressure Ratio @ 7
  53:          private double P0_7;            // Nozzle Exit Stagnation Pressure
  54:          private double T0_7;            // Nozzle Exit Stagnation Temperature
  55:          private double T7T0;            // Overall Temperature Ratio
  56:          private double T7;              // Exit Temperature
  57:          private double M_7;             // Core Exhaust Mach Number
  58:          public double U_0;              // Freestream Velocity
```

Air passing through the fan is accelerated, but at a much lower magnitude than that of the core. However, due to the increased mass of the air, most of the thrust is generated through the fan section. Once passing through the fan, the air is slightly accelerated through the nacelle. Air passing through the core first undergoes compression. At this stage the air is compacted and heated up to conditions that are ideal for combustion. This is accomplished through a series of compressor stages. In the Pratt and Whitney geared turbofan, there are 3 low pressure stages coupled with 8 high pressure stages to accomplish a compression ratio of 36. At the next stage, the air is mixed with fuel and combusted. This increases the temperature of the air to that of the flame temperature. This heat is then used by the turbine to create mechanical work to drive the compressor and fan. The remaining heat is then used by the nozzle to accelerate the air, thus gaining additional thrust. 

Additions to the real turbofan code, as defined by Dr. Lee in MAE468, were required. The real turbofan code is not set up to handle a low freestream velocity nor does it support bleed air losses. The inlet fan velocity was added to the code to find the static thrust and thrust required at takeoff. This became a function of density, throttle setting, mach number, and fan diameter. An equation, was derived based on Honeywell documents pertaining to the AGT1500. The results were matched against known engines and refined to match the static sea level thrust. Results of this are shown above in table 2. Using RPM data for throttle settings, the bleed air losses and throttle setting efficiency based on the AGT1500. In order to accommodate early laminar flow control mechanics, bleed air was going to be required. Data was collected for efficiency at different throttle settings and fit to create a curve that would determine the losses due to bleed air. Since the aircraft sets the throttle to match the required thrust at each iteration, the efficiency of this throttle setting must be taken into account for an accurate TSFC calculation.

| Throttle Setting  | Max Shaft Horse Power [HP] | Actual Shaft Horse Power [HP] |
| ------------- | ------------- | ------------- |
| 1  | 1500 | 1500 |
| .8  | 1220  | 1200 |
| .6 | 930 | 900 |
| .4 |  640 | 600 |

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1207.jpg?raw=true)


The increased efficiency for the engine, and the reason it was selected, is due to the use of a gear box. Many turbofan engines these days are triple spooled, where the low pressure compressor is driven by the intermediate pressure temperature turbine, the high pressure compressor is driven by the high pressure turbine, and the fan is driven by the low pressure turbine. This is accomplished through three concentric shafts. The fan diameter is limited by the speed at which it needs to operate. This is not so with a geared design. Geared turbofans generally hide a planetary gear system to decrease the fan speed as the turbine will rotate at a much higher velocity. [1] This also allows for a double spool, where the low pressure turbine and compressor are connected to the fan. The gearbox allows the fan and the turbine to operate at their respective ideal rotational velocity, where the fan can operate at 34% of the speed and the compressor and turbine are capable of twice their usual speeds. [3] The net effect is less complexity, fewer stages, and a weight reduction of 182 kg per engine. The PurePower series increases propulsive efficiency, and utilizes a variable core nozzle to decrease the amount of fuel burned. Finally, the engine is nearing its initial entry into service for large commercial airliners in the next few years, which gives it a very high technology readiness level. Pratt and Whitney are also currently working on the next two revisions to the engine, which will eventually make it 15% more efficient than the first version. 


The testbench software is a WPF application, written in 100% C#. It consists of 2 dll files, 1 for the Real Turbofan model and the second for the PurePower PW1000G. The entire solution is only 600 lines of code, including the XAML for the interface.

![alt text](https://github.com/artfulhacker/Turbofan-Engine-Model/blob/master/91_1209.jpg?raw=true)


1.	Hill, Phillip and Peterson, Carl. Mechanics and Thermodynamics of Propulsion 2nd Edition. Addison-Wesley Publishing. Reading, Massachusetts. 1992.

2.	Lecture: MAE 463. Dr. Tae-Woo Lee. Spring 2010

3.	Croft, John. US Engine Makers Gear Up for 2025 and Beyond. Flight Global. http://www.flightglobal.com/news/articles/us-engine-makers-gear-up-for-2025-and-beyond-354207/. Accessed September 26, 2011. 

4.	Joint Aviation Authorities. Data Sheet JAA/E/96 – 012 TFE731. Issue 8. October 22, 1999. 
5.	GE90 – An Introduction 

6.	AGT1500 Honeywell Technical Data Sheet 
