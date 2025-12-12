# Multiple Device & Light Synchronization

**Version:** 2.0.0

**Released:** December 7, 2025

A Home Assistant blueprint for automatically synchronizing states, brightness, colors, and transitions across multiple devices and lights. Keep your devices perfectly in sync with intelligent state management and graceful handling of offline devices.

## Overview

This blueprint works with any entity supporting `homeassistant.turn_on` and `homeassistant.turn_off` service calls, with enhanced features for dimmable and color lights.

## Features

### State Management
- **Smart State Synchronization** — Automatically sync on/off states across all linked devices
- **Post-Restart Recovery** — Automatically detects and corrects state mismatches after Home Assistant restarts
- **Loop Prevention** — Enhanced context tracking prevents infinite automation loops

### Light Control
- **Brightness Matching** — Dimmable lights sync brightness levels in real-time with configurable threshold
- **Color Synchronization** — RGB color lights sync colors across all color-capable lights
- **Color Temperature Sync** — White lights sync color temperature (warm/cool)
- **Smooth Transitions** — Configurable transition times for gradual, elegant state changes

### Reliability
- **Resilient Operation** — Gracefully handles offline/unavailable devices
- **Intelligent Filtering** — Only targets available entities, skipping unavailable ones
- **Sync Delay** — Optional delay before syncing to prevent rapid changes

## Requirements

- Home Assistant 2024.1.0 or newer
- All selected entities must support `homeassistant.turn_on` and `homeassistant.turn_off` service calls

> **Note:** 
- Entities that don't support these service calls will result in errors and the blueprint will not function properly. 
- You may see "Error in describing trigger" in traces - this is a cosmetic Home Assistant UI bug and does not affect functionality. The automation works correctly and traces are created successfully.

## Credits

This blueprint builds upon the work of several contributors:

- [@adchevrier](https://community.home-assistant.io/u/adchevrier) — [Initial blueprint](https://community.home-assistant.io/t/synchronize-the-on-off-state-of-2-entities/259010)
- [@hebus](https://community.home-assistant.io/u/hebus) — [Template improvements](https://community.home-assistant.io/t/synchronize-the-on-off-state-of-2-entities/259010/38)
- [@dbrand666](https://github.com/dbrand666) — [Various fixes](https://gist.github.com/dbrand666/b731672b2a282b9afd51a97e6e5c2b2b)
- [@jmaroeder](https://github.com/jmaroeder) — [Additional inspiration](https://gist.github.com/jmaroeder/c0952533a3f2c746eef83db268ddbbba)
