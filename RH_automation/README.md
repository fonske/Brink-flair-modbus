Instead of the (expensive) Brink RH sensor, it's possible to use a custom humidity sensor in the inlet and use a HomeAssistant automation to (temporarily) increase the fan speed when a (sharp) humidity rise is detected.

You can use this automation blueprint: https://community.home-assistant.io/t/bathroom-humidity-exhaust-fan/509992

To set it up you need:
1. Derivative sensor
2. Switch helper
3. Set fan speed high script
4. Set fan speed low script

# Setup
## Setup of derivative sensor
Go to Settings / Devices & Services and click on the "Helpers" tab at the top. Click create helper and select "Derivative"
![image](https://community-assets.home-assistant.io/optimized/4X/0/a/2/0a2c348dbb860509de8728ede7af9ffe26143050_2_126x250.jpeg)

Fill out the name you would like your derivative sensor to be, e.g. brink_humidity_from_inside_derivative. Select the humidity sensor (brink_humidity_from_inside) as input sensor, precision as 2, time window as 3 and in time unit, select "minutes". Then click submit to create the helper.

## Setup of switch helper
In the same tab as the derivative sensor. Add another helper and select "Toggle". Give it a name you want, e.g. brink_fan_high_low_helper, and click submit to create it.

## Setup of fan speed high and low scripts
Go to Settings / Automation and click on the "Scripts" tab. Click create script. Choose a name, e.g. Brink fan high/low. Then add action "call service". In service input field enter "number.set-value".
Choose entity: number.brink_modbus_flow_rate / number.brink_ventilatie_stand, and add value 3 for high or 1 for low. Do this for both high and low!

# Blueprint configuration
After adding the blueprint, configure it as follows:
![image](https://github.com/werty9021/Brink-flair-modbus/assets/24314690/b5c6ac19-1fcd-4363-acc0-33bb87bca820)
- Humidity Derivative Sensor - Trigger: select the just created derivative sensor, e.g. brink_humidity_from_inside_derivative.
- Fan Switch: select the switch helper (brink_fan_high_low_helper) and the fan high script (script.brink_fan_high).
- Input - Fan Speed - Switch Off (Optional): select the fan low script (script.brink_fan_low).
- Rising/Falling Humidity can be fine-tuned according to your needs. Since the sensor is in the inlet, and not in the bathroom or kitchen, the humidity rise/fall might not be very large. For me, 1%/min rise is OK. 1%/min fall is mostly too much.

Then in "Use The Fan Speed Options (Optional)", select "Enable the fan speed - turn off option":
![image](https://community-assets.home-assistant.io/original/4X/0/2/d/02df7e1aa41dd7f6d3a444816cbd4072542d7d8e.jpeg)


