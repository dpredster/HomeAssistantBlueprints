# HVAC Pause Automation

**Version:** 1.8.1

**Released:** December 12, 2025

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

### Notifications (New in v1.9.0)
- **Optional Notification Messages** — Pre-formatted messages for pause, resume, and temperature override events
- **Specific Door/Window Identification** — Notifications include which specific door or window triggered the pause
- **Independent Notification Controls** — Enable/disable notifications for each event type separately
- **Message Variables** — Access pre-formatted messages with timestamps, temperature data, and entity names
- **Flexible Notification Services** — Use any notification service (mobile app, Alexa, etc.)

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

### Notification Options
- **Enable Pause Notifications** — Toggle to enable/disable pause notifications (default: off)
- **Enable Resume Notifications** — Toggle to enable/disable resume notifications (default: off)
- **Enable Temperature Override Notifications** — Toggle to enable/disable temperature override notifications (default: off)

### Advanced Options
- **Action First** — Execute pause actions before pausing HVAC
- **Pause Action** — Optional actions to perform when HVAC pauses (e.g., send notification)
- **Resume Action** — Optional actions to perform when HVAC resumes (e.g., send notification)
- **Temperature Override Action** — Optional actions to perform when temperature override activates (e.g., send notification)
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

## Configuration Instructions

### Setting Up Pause Notifications
1. Enable **Enable Pause Notifications** toggle
2. In the **Pause Action**, select your notification service
3. In the Message field, type exactly:
   ```yaml
   {{ message }}
   ```
4. (Optional) In the Data section, add:
   ```yaml
   importance: high
   ttl: 0
   priority: high
   ```

### Setting Up Resume Notifications
1. Enable **Enable Resume Notifications** toggle
2. In the **Resume Action**, select your notification service
3. In the Message field, type exactly:
   ```yaml
   {{ message }}
   ```
4. (Optional) In the Data section, add:
   ```yaml
   importance: high
   ttl: 0
   priority: high
   ```

### Setting Up Temperature Override Notifications
1. Enable **Enable Temperature Override Notifications** toggle
2. In the **Temperature Override Action**, select your notification service
3. In the Message field, type exactly:
   ```yaml
   {{ message }}
   ```
4. (Optional) In the Data section, add:
   ```yaml
   importance: high
   ttl: 0
   priority: high
   ```

## Available Variables

### Pause Action
- `{{ message }}` — Pre-formatted message with timestamp and specific door/window names
  - **Example (single):** "HVAC paused at 3:45 PM on Dec 12 because Kitchen Window opened"
  - **Example (two):** "HVAC paused at 3:45 PM on Dec 12 because Kitchen Window and Front Door opened"
  - **Example (multiple):** "HVAC paused at 3:45 PM on Dec 12 because Kitchen Window, Bedroom Window, and Front Door opened"
- `{{ time }}` — Formatted timestamp (e.g., "3:45 PM on Dec 12")
- `{{ opened_items }}` — List of specific doors/windows that are open

### Resume Action
- `{{ message }}` — Pre-formatted message with timestamp and last closed sensor
  - **Example:** "HVAC resumed at 3:50 PM on Dec 12 after Kitchen Window closed"
- `{{ time }}` — Formatted timestamp (e.g., "3:50 PM on Dec 12")
- `{{ closed_sensor }}` — Name of the last sensor that closed before resume

### Temperature Override Action
- `{{ message }}` — Pre-formatted message with temperature, threshold, and reason
  - **Example (too cold):** "Temperature override activated: 18.5°C is below minimum threshold of 20°C"
  - **Example (too hot):** "Temperature override activated: 26.8°C is above maximum threshold of 25°C"
- `{{ time }}` — Formatted timestamp
- `{{ current_temp }}` — Current temperature reading
- `{{ threshold }}` — The threshold value that was exceeded (min or max)
- `{{ reason }}` — "below minimum threshold" or "above maximum threshold"

## Use Cases

- **Energy Savings** — Automatically pause heating/cooling when doors or windows are opened
- **Comfort Protection** — Ensure HVAC resumes if temperature becomes uncomfortable, even with open doors/windows
- **Seasonal Monitoring** — Use only minimum threshold in winter, only maximum in summer
- **Smart Notifications** — Get instant alerts when HVAC is paused, resumed, or temperature override activates
- **Custom Messages** — Replace `{{ message }}` with your own text for personalized notifications
- **Multi-Zone Control** — Trigger actions in other automations or scenes
- **Multiple Notification Services** — Send notifications to mobile app, Alexa, TTS, or multiple services

## Version History

### Version 1.9.0
- Added optional notification messages for pause, resume, and temperature override events
- Added specific door/window identification in pause notifications
- Added last closed sensor identification in resume notifications
- Added independent enable/disable toggles for each notification type
- Added pre-formatted message variables with timestamps and temperature data
- Enhanced action descriptions with notification configuration examples

### Version 1.8.0
- Added temperature override protection
- Added configurable minimum and maximum temperature thresholds
- Added temperature override duration setting
- Added special Ecobee thermostat support

## Tips & Best Practice

- **Pause Delay**: Set to 3-5 minutes to avoid pausing when briefly opening doors
- **Resume Delay**: Set to 30-60 seconds to ensure doors/windows are truly closed
- **Temperature Override**: Enable for comfort protection, especially if you frequently have windows open
- **Notification Toggles**: Enable only the notifications you need to avoid alert fatigue
- **Testing**: Use developer tools to test temperature override triggers
- **Custom Messages**: You can use `{{ message }}` or write your own message text

## Advanced Usage

### Custom Message Examples
Instead of using `{{ message }}`, you can write custom messages:
- Pause: `"Alert: HVAC paused at {{ time }} because {{ opened_items }} opened"`
- Resume: `"HVAC back online at {{ time }} after {{ closed_sensor }} closed"`
- Temperature Override: `"Warning: Temperature is {{ current_temp }}°C ({{ reason }} of {{ threshold }}°C). HVAC resumed."`

### Multiple Notification Services
Create pause/resume/override actions that call multiple services:
1. Add a sequence with multiple notification calls
2. Send to mobile app for instant alerts
3. Send to Alexa/Google for voice announcements
4. Log to persistent notification for history

## Credits

This blueprint builds upon the work of:

- [@raffy-ops](https://github.com/raffy-ops) — [Initial blueprint](https://gist.github.com/raffy-ops/2bdf967036d8d274fb1f62572ed5e545)
