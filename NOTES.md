Project notes: FluidNC I2S stepping & config troubleshooting
=============================================================

Summary of recent changes (Nov 2025)
-----------------------------------
- Replaced on-device `/config.yaml` with a corrected configuration that enables I2S stepping
  on the ESP32 and avoids duplicate GPIO claims.

- Key fixes applied:
  - Removed UTF-8 BOM and stray control characters from `config.yaml` (these caused parse errors).
  - Ensured motor definitions live under `axes:` with `motor0:` and driver `standard_stepper:`.
  - Replaced `dir_pin` with the expected `direction_pin` key.
  - Configured an `i2so:` section with native-capable GPIOs (bck/ws/data pins).
  - Set `stepping.engine: I2S_STREAM` so `i2so.*` pins are valid for steppers.
  - Assigned `disable_pin: gpio.19:low` to X only to avoid "Pin is already used" errors.

Configuration example used (top-level keys):

```
stepping:
  engine: I2S_STREAM
  pulse_us: 2

i2so:
  bck_pin: gpio.5
  ws_pin: gpio.23
  data_pin: gpio.4
  min_pulse_us: 2

axes:
  x:
    motor0:
      standard_stepper:
        step_pin: i2so.0
        direction_pin: i2so.1
        disable_pin: gpio.19:low

  y:
    motor0:
      standard_stepper:
        step_pin: i2so.2
        direction_pin: i2so.3

  z:
    motor0:
      standard_stepper:
        step_pin: i2so.4
        direction_pin: i2so.5

  a:
    motor0:
      standard_stepper:
        step_pin: i2so.6
        direction_pin: i2so.7
```

Flash / deploy notes
--------------------
- LittleFS contains `/config.yaml`. When WebUI is unavailable, build the FS image and write it to the ESP32:

  ```powershell
  pio run -e wifi -t buildfs
  pio run -e wifi -t uploadfs --upload-port COM10
  # OR
  esptool.py --chip esp32 --port COM10 write_flash -z 0x3D0000 .pio/build/wifi/littlefs.bin
  ```

- Close PuTTY or any serial monitor before flashing (port must be free).

Runtime checks
--------------
- On boot, verify these lines in serial logs (115200):
  - "I2SO BCK:gpio.X WS:gpio.Y DATA:gpio.Z Min Pulse:2us"
  - "Stepping:I2S_STREAM Pulse:2us ..."
  - "standard_stepper Step:I2SO.N Dir:I2SO.M Disable:..." for each axis

Known warnings
--------------
- The parser logs benign warnings like "Ignored key machine" and "Ignored key enabled" for top-level settings — these are non-fatal.
- If you see repeated brownout or WDT resets, investigate the power supply (brownout detector triggers).

Next recommended tasks
----------------------
1. Test enabling motors and jogging from WebUI or telnet; capture serial output for any unexpected errors.
2. If you want a shared disable line, replace per-axis `disable_pin` with a single `user_outputs` control and reference it.
3. Add homing and limit definitions when ready.

Contact
-------
If you want me to push this commit to the remote, or to add the shared disable configuration, say so and I will proceed.
