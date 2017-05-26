Adding PWM functiality to esp8266 based board
PWM is only available on esp8266 currently.
To (in seperate folders):
1. Slampher---mOS-Node-Red
2. Sonoff TH16---mOS-Node-Red

From:  
https://github.com/cesanta/mongoose-os/blob/master/fw/platforms/esp8266/src/esp_pwm.c

* Semi-hardware PWM - uses hardware timer 1 to generate base clock.
 *
 * PWM.set(pin, period, duty)
 *   pin:    GPIO number.
 *   period: Period, in microseconds, 100 is the minimum supported and any
 *           number will be rounded down to the nearest multiple of 50.
 *           period = 0 disables PWM on the pin (duty = 0 is similar but
 *           does not perform internal cleanup).
 *   duty:   How many microseconds to spend in "1" state. Must be between 0 and
 *           period (inclusive). 0 is "always off", period is "always on",
 *           period / 2 is a square wave. Number will be rounded down to the
 *           nearest multiple of 50.
 *
 *
 * Is it better than the PWM implementation that comes with the SDK? Yes it is.
 *
 * + Arbitrary number of pins can be configured for PWM simultaneously, pins
 *   can be added and removed at any time.
 *   Note: GPIO 16 is not currently supported.
 * + Period can be different for different pins.
 * + Up to 5 KHz frequency can be generated.
 * + No interrupts in idle mode (no pins configured or duty = 0 for all).
 */

let PWM = { set: ffi('int mgos_pwmset(int, int, int)') };

This yields a 1ms pulse train with a frequency of 20ms:
PWM.set(14, 20000, 1000)

This yields a 2ms pulse train with a frequency of 20ms:
PWM.set(14, 20000, 2000)




From: https://github.com/cesanta/mongoose-os/issues/153

PWM is only available on esp8266 currently. If you want to use it, do:

1. checkout mongoose-os repo 
2. cd to fw/examples/mjs_base
3. add line "mgos_pwm_set" to the list of exported symbols
	mongoose-os/fw/examples/mjs_base/mos.yml
	https://github.com/cesanta/mongoose-os/blob/master/fw/examples/mjs_base/mos.yml
4. type "mos build --arch esp8266"
5. type "mos flash"

Now you can use PWM from JS:

let PWM = { set: ffi('int mgos_pwm_set(int, int, int)') };
PWM.set(14, 20000, 1000)

