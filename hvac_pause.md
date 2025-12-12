# HVAC Pause Automation

**Version:** 1.8.0
**Released:** December 7, 2025

A Home Assistant blueprint that intelligently pauses your HVAC system when windows or doors are opened, and automatically resumes operation when they're closed. Now includes temperature override protection to ensure comfort is maintained even when openings are detected.

## Overview

This blueprint monitors door and window sensors and temporarily pauses your climate control system when openings are detected, helping save energy while maintaining the ability to restore your previous HVAC settings automatically.

## Features

### Core Automation
- **Smart Pause & Resume** — Automatically pauses HVAC when doors/windows open and resumes previous state when closed
- **State Preservation** — Captures and restores exact HVAC settings (mode, temperature, fan settings)
- **Configurable Delays** — Set custom delays before pausing and resuming to prevent rapid cycling
- **Timeout Protection** — Optional timeout to keep HVAC off if openings remain open too long

### Temperature Override (New in v1.8.0)
- **Intelligent Temperature Monitoring** — Override door/window automation when temperature goes outside safe range
- **Dual Threshold Protection** — Set minimum and maximum temperature limits
- **Flexible Configuration** — Enable/disable either or both temperature thresholds as needed
- **Auto-Resume on Temperature Events** — HVAC automatically resumes and automation suspends when temperature protection activates
- **Configurable Duration** — Set how long temperature must be outside range before override triggers

### Customization
- **Optional Actions** — Execute custom actions when pausing, resuming, or on timeout
- **Action Timing Control** — Choose whether pause actions occur before or after HVAC pause
- **Ecobee Integration** — Special handling for Ecobee thermostats to clear comfort holds

## Requirements

- Home Assistant with a climate entity
- Door/window sensors (individual binary_sensor or group entity)
- For temperature override: Temperature sensor entity

## Configuration Options

### Basic Settings
- **Climate Device** — Your HVAC/thermostat entity
- **Door and Window Sensors** — Group or binary sensor entities (assumes 'on' means 'open')
- **Pause Delay** — Time to wait before pausing HVAC (default: 5 minutes)
- **Resume Delay** — Time to wait before resuming once closed (default: 30 seconds)
- **Timeout** — Optional maximum time to wait before giving up (set to 0 to disable)

### Temperature Override
- **Enable Temperature Override** — Toggle to enable/disable temperature protection
- **Temperature Sensor** — Sensor to monitor for temperature override conditions
- **Minimum Temperature** — Resume HVAC if temperature falls below this value (set to -999 to disable)
- **Maximum Temperature** — Resume HVAC if temperature exceeds this value (set to 999 to disable)
- **Temperature Override Duration** — How long temperature must be outside range before resuming (default: 5 minutes)

### Advanced Options
- **Action First** — Execute pause actions before pausing HVAC
- **Pause Action** — Optional actions to perform when HVAC pauses
- **Resume Action** — Optional actions to perform when HVAC resumes
- **Timeout Action** — Optional actions to perform if timeout occurs

## How It Works

### Normal Operation
1. Automation monitors your door/window sensors
2. When an opening is detected for the pause delay duration, HVAC is paused
3. Current HVAC state (mode, temperature, settings) is saved
4. Optional pause actions are executed
5. When all openings close for the resume delay duration, HVAC resumes to saved state
6. Optional resume actions are executed

### Temperature Override Mode
1. When enabled, temperature sensor is continuously monitored
2. If temperature goes below minimum threshold (when not disabled) for the configured duration:
   - HVAC immediately resumes to saved state
   - Automation stops and door/window sensors are ignored
3. If temperature goes above maximum threshold (when not disabled) for the configured duration:
   - HVAC immediately resumes to saved state
   - Automation stops and door/window sensors are ignored
4. This ensures comfort is maintained even if doors/windows are open

### Disabling Temperature Thresholds
- Set **Minimum Temperature** to `-999` to disable low temperature protection only
- Set **Maximum Temperature** to `999` to disable high temperature protection only
- Set both to their extreme values to effectively disable temperature override (or use the toggle)
- This allows monitoring only heating concerns, only cooling concerns, or both

## Use Cases

- **Energy Savings** — Automatically pause heating/cooling when doors or windows are opened
- **Comfort Protection** — Ensure HVAC resumes if temperature becomes uncomfortable, even with open doors/windows
- **Seasonal Monitoring** — Use only minimum threshold in winter, only maximum in summer
- **Smart Notifications** — Add actions to send alerts when HVAC is paused or resumed
- **Multi-Zone Control** — Trigger actions in other automations or scenes

## Credits

This blueprint builds upon the work of:

- [@raffy-ops](https://github.com/raffy-ops) — [Initial blueprint](https://gist.github.com/raffy-ops/2bdf967036d8d274fb1f62572ed5e545)
