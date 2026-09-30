# Helicopter spool-up and bailout

> Part of the unofficial ESCape32 heli fork, version 17.50 (based on upstream 17.2). Not part of upstream ESCape32.

ESCape32 can spool a helicopter rotor up slowly and smoothly, and recover quickly from a throttle cut in flight, in the style of Hobbywing Platinum series heli ESCs.

Many heli flight controllers send a fixed throttle target the moment the model is armed (for example, 70%). Without a spool-up the ESC follows that almost instantly and the head slams up to speed, which is hard on the gears, belt, blade grips and motor, and unsafe on the ground. With a spool-up the head comes up to speed over several seconds.

## Quick setup

| Setting | Recommended | Why |
|---|---|---|
| `heli_spoolup_sec` | 20–25 | Time for the head to reach speed after arming |
| `heli_bail_window_sec` | 5–10 | How long after a throttle cut a quick recovery is allowed |
| `heli_bail_spool_ms` | 1000–2000 | How fast power returns during that recovery |
| `sine_range` | 5–8 | Lets the motor turn slowly and smoothly from a dead stop |
| `sine_power` | 8–12 | Raise until the head turns without stalling at the start |
| `throt_mode` | 0 (fwd) | Reverse throttle bypasses the spool-up |
| `throt_ztc` | 1 (on) | Head coasts freely in throttle hold; required for a clean bailout |
| `throt_cal` | 0 (off) | Set `throt_min` / `throt_max` to your flight controller's endpoints instead, so its throttle target is exact |

Always test on the bench with the blades removed first.

## Settings

| Setting | Range | Default | Description |
|---|---|---|---|
| `heli_spoolup_sec` | 0–60 s | 15 | Spool-up time from a stop to the commanded throttle. 0 turns the feature off (stock, immediate response). |
| `heli_bail_window_sec` | 0–60 s | 10 | After a throttle cut in flight, how long a quick re-spool is allowed. 0 turns bailout off. |
| `heli_bail_spool_ms` | 500–10000 ms | 1500 | Re-spool time during a bailout. Never longer than `heli_spoolup_sec`; the ESC limits it automatically. |

The setting names include their units because configurators such as the Wi-Fi Link show names but may not have descriptions for new settings.

These settings are not available with brushed motors.

## How it behaves

### Spool-up

When throttle is applied from a stop, the ESC ramps the throttle in a straight line from zero to whatever the flight controller commands, over `heli_spoolup_sec`. The time is the same regardless of the target: with a 15 s setting, 70% throttle is reached in 15 s, and so is 50%. If the flight controller changes its target during the spool-up, the ramp follows it smoothly.

Once the spool-up completes, throttle passes straight through with no delay.

### Bailout

If throttle is cut in flight (throttle hold) and restored quickly, a full slow spool-up could cost the aircraft. A bailout re-spools over `heli_bail_spool_ms` instead. It happens only if all three are true:

1. The cut happened after a completed spool-up (you were flying).
2. Throttle came back within `heli_bail_window_sec`.
3. The rotor is still turning fast enough for the ESC to track it (roughly 800 eRPM or more).

Otherwise the normal `heli_spoolup_sec` spool-up is used.

Whenever the rotor is still turning at a restart, the ramp starts from the throttle that matches the rotor's current speed rather than from zero. This keeps the ESC from braking a coasting rotor.

### Examples (defaults: 15 s spool-up, 10 s window, 1.5 s bailout)

| Situation | Result |
|---|---|
| Arm on the ground, flight controller sends 70% | Head reaches speed in 15 s |
| In flight, throttle hold, released 4 s later | Back to speed in about 1.5 s, starting from the current rotor speed |
| Autorotation to the ground, hold released 20 s later | Window expired: full 15 s spool-up |
| Throttle cut halfway through the first spool-up | Next start is a full 15 s spool-up (was not yet flying) |

## Implementation notes

The logic lives in `helispoolup()` in `main.c`. It filters the throttle input at the top of the main loop, before sine startup, the 6-step handover, `duty_spup`/`duty_ramp`, the `duty_rate` slew limiter and the protections. All of those keep working unchanged.

Shaping the input rather than the duty cycle is deliberate. Limiting the duty cycle after the slew limiter makes the two fight each other, which produces a spool-up that stalls near `duty_spup` and then surges at the end.

The new configuration fields are appended to the end of `Cfg` in `common.h`, so the layout of all stock fields is unchanged. Range checks are in `checkcfg()` in `util.c`, and the defaults are in `defs.h`.

## Tested hardware

- ESC: Sequre 28120
- Flight controller: Flywing H1 Pro (sends a fixed 70% throttle target on arm)
- Bench-tested and flight-tested.

