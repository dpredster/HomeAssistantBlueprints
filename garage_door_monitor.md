# Garage Door Status and Left Open Monitor

**Version:** 1.0.0

A Home Assistant blueprint that provides comprehensive garage door monitoring with immediate status notifications and smart reminders when doors are left open. Includes actionable notifications to close the door directly from your phone.

## Overview

This blueprint combines two powerful monitoring features: instant notifications when your garage door opens or closes, and intelligent reminders when a door has been left open for too long. It helps prevent accidentally leaving your garage door open while keeping you informed of all door activity.

## Features

### Part 1: Status Change Notifications
- **Instant Alerts** — Get notified immediately when the garage door opens or closes
- **Timestamp Information** — Each notification includes the exact time and date of the event
- **Independent Control** — Enable or disable status notifications separately from left-open alerts
- **Customizable Actions** — Use any notification service or action you prefer

### Part 2: Left Open Monitoring
- **Smart Delay** — Configurable initial delay before first notification to avoid false alarms
- **Intelligent Reminders** — Periodic notifications showing how long the door has been open
- **Actionable Notifications** — Tap "YES" on the notification to close the door remotely
- **Automatic Door Closing** — Blueprint handles closing the door when you tap YES
- **Restart Resilient** — Continues monitoring even after Home Assistant restarts
- **Independent Control** — Enable or disable left-open notifications separately from status alerts

### Customization
- **Flexible Actions** — Configure any action (notifications, scripts, etc.) for status changes and left-open alerts
- **Message Variables** — Access pre-formatted messages with door status, time, and duration
- **Notification Priority** — Built-in support for high-priority, time-sensitive notifications
- **Car UI Support** — Optimized for Android Auto notifications

## Requirements

- Home Assistant with a cover entity (garage door)
- For notifications: Mobile app or notification service configured
- For actionable notifications: Home Assistant Mobile App

## Configuration Options

### Basic Settings
- **Cover Entity** — Your garage door cover entity
- **Status Change Action** — Action to perform when door opens or closes (e.g., send notification)
- **Left Open Action** — Action to perform when door is left open (e.g., send notification with YES button)
- **Enable Status Change Notifications** — Toggle to enable/disable immediate open/close alerts (default: on)
- **Enable Left Open Notifications** — Toggle to enable/disable left-open monitoring (default: on)

### Timing Settings
- **Initial Delay** — How long the door must be open before the first notification (default: 15 minutes)
- **Reminder Interval** — How often to send reminder notifications (default: 60 minutes, set to 0 to disable reminders)

## How It Works

### Status Change Notifications
1. Blueprint monitors your garage door cover entity for state changes
2. When the door opens or closes, a notification is sent immediately
3. Message includes the exact time and date (e.g., "Garage door just Opened at 3:45 PM on Dec 10")
4. Works independently of left-open monitoring

### Left Open Monitoring
1. When the door opens and stays open for the initial delay period, first notification is sent
2. Message includes when the door was opened and asks if you want to close it
3. If you tap "YES" on the notification:
   - Blueprint automatically closes the garage door
   - Monitoring stops
4. If you don't respond and reminder interval > 0:
   - Reminder notifications are sent at regular intervals
   - Each reminder shows total time door has been open (e.g., "1 hours 30 minutes")
   - Each reminder includes the YES button to close the door
5. Monitoring continues until the door is closed (manually or via YES button)

### Home Assistant Restart Handling
1. If Home Assistant restarts while the door is open past the initial delay:
   - Blueprint automatically resumes monitoring
   - Sends a notification with the YES button
   - Continues reminder loop if configured
2. No loss of protection even after system restarts

## Configuration Instructions

### Setting Up Status Change Notifications
1. In the Status Change Action, select your notification service
2. In the Message field, type exactly: `{{ message }}`
3. (Optional) In the Data section, add:
   ```yaml
   importance: high
   ttl: 0
   priority: high
   car_ui: true
   ```
### Setting Up Left Open Notifications with YES Button
1. In the Left Open Action, select your notification service
2. In the Message field, type exactly: `{{ message }}`
3. (Optional) To enable the YES button, expand the Data section and add:
   ```yaml
   actions:
     - action: "{{ action_yes }}"
       title: "YES"
   importance: high
   ttl: 0
   priority: high
   car_ui: true
   ```

## Available Variables

### Status Change Action
- `{{ message }}` — Pre-formatted message with status and timestamp
  - **Example (door opened):** "Garage door just Opened at 8:10 AM on Dec 11"
  - **Example (door closed):** "Garage door just Closed at 5:32 PM on Dec 11"
- `{{ status }}` — Door status (Opened or Closed)
- `{{ time }}` — Formatted timestamp

### Left Open Action
- `{{ message }}` — Pre-formatted message with time door was opened and duration
  - **Initial notification:** "Garage door was left open at 8:10 AM! Do you want to close it?"
  - **Reminder notification:** "Reminder: Garage door has been open for 1 hours 30 minutes. Do you want to close it?"
- `{{ action_yes }}` — Unique action ID for the YES button (required for actionable notifications)

## Use Cases

- **Security Monitoring** — Get instant alerts whenever your garage door opens or closes
- **Prevent Accidents** — Receive reminders if you forget to close the garage door
- **Remote Control** — Close the garage door from anywhere by tapping YES on the notification
- **Peace of Mind** — Know your garage door status even after Home Assistant restarts
- **Custom Messages** — Replace `{{ message }}` with your own text for personalized notifications
- **Multiple Doors** — Create separate automations for each garage door

## Tips & Best Practice

- **Initial Delay**: Set to 5-15 minutes to avoid alerts when actively using the garage
- **Reminder Interval**: Set to 30-60 minutes for regular reminders, or 0 to get only one notification
- **Testing**: Trigger the door state changes to test (manual testing via "Run Actions" won't work)
- **Custom Messages**: You can use `{{ message }}` or write your own message text
- **Disable Features**: Turn off status or left-open notifications independently based on your needs

## Advanced Usage

### Custom Message Examples
Instead of using `{{ message }}`, you can write custom messages:
- Status: `"Alert: Door {{ status }} at {{ time }}"`
- Left Open: `"URGENT: Garage door open for {{ ((as_timestamp(now()) - as_timestamp(states[cover_entity].last_changed)) | int) // 60 }} minutes"`

### Multiple Notification Services
Create multiple automations from the same blueprint to send notifications to different services (e.g., mobile app + Alexa announcements).

## Version History

### Version 1.0.0
- Combined status change and left open monitoring in one blueprint
- Added actionable notifications with YES button to close door
- Added Home Assistant restart resilience
- Added independent enable/disable toggles for each notification type
- Added tracing support for debugging

