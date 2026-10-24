# EasySwipe

Give your home screen swipe down more than one job.

EasySwipe splits the swipe down on the home screen into three zones (left, center and right) and lets you choose what each one does. Keep Spotlight in the middle, open Notification Center on the left and Control Center on the right, or set it up however you like.

## Features

- Three zones with their own action
- Adjustable width for the left and right zones (0–50 % of the screen each)
- Changes apply instantly, no respring needed
- Optional [Activator](https://github.com/rpetrich/libactivator) support
- No Cephei dependency

### Actions

| Action | What it does |
|---|---|
| Spotlight | Stock behavior |
| Notification Center | Opens Notification Center |
| Control Center | Opens Control Center |
| Lock Screen | Locks the device |
| Flashlight | Toggles the flashlight |
| Screenshot | Takes a screenshot |
| Open App | Opens the app picked in Settings |
| Respring | Restarts SpringBoard |
| Activator | Sends an EasySwipe event to Activator, falls back to Spotlight if nothing is assigned |
| Nothing | Ignores the swipe |

## Requirements

- iOS 15 or later, rootless jailbreak
- PreferenceLoader
- Activator (optional)

> Tested on the iOS 18.5 simulator. Feedback from real devices, especially on iOS 15–17, is welcome.

## Installation

Download the latest `.deb` from the [Releases](../../releases) page and install it with your package manager (Sileo, Zebra…). Then open **Settings → EasySwipe**.

## Building

You need [Theos](https://theos.dev) with a patched iOS SDK (the settings bundle links against the private `Preferences` framework from the 16.5 SDK).

```bash
make package FINALPACKAGE=1
```

The rootless package is written to `packages/`.

### Testing in the iOS Simulator

The project can run in the simulator through [simject](https://github.com/akemin-dayo/simject):

```bash
make sim
```

This builds the tweak and the settings bundle for the simulator, copies them to `/opt/simject` and resprings the simulator with `resim`. Set `SIM_UDID` to target another simulator:

```bash
make sim SIM_UDID=<simulator-udid>
```

On iOS 18 the Settings root list is SwiftUI, so `sim/ESSettingsLoader.m` (simulator only, not packaged) opens the EasySwipe panel directly when Settings launches.

## Activator

EasySwipe registers three events under the **EasySwipe** group:

| Event | Identifier |
|---|---|
| Swipe down on the left | `com.antonym.easyswipe.swipe-left` |
| Swipe down in the center | `com.antonym.easyswipe.swipe-center` |
| Swipe down on the right | `com.antonym.easyswipe.swipe-right` |

Set a zone to **Activator** in EasySwipe's settings, then assign any action to the matching event in Activator.

## Preferences

Settings are stored in the `com.antonym.easyswipe` domain and reloaded on the `com.antonym.easyswipe/reload` Darwin notification.

| Key | Type | Default |
|---|---|---|
| `enabled` | bool | `true` |
| `leftAction` / `centerAction` / `rightAction` | integer (see actions above, 0–9) | `1` / `0` / `2` |
| `leftApp` / `centerApp` / `rightApp` | bundle identifier | — |
| `leftWidth` / `rightWidth` | percentage, 0–50 | `33` |

## Project layout

```
Tweak.x              Gesture hook and actions (SpringBoard)
Tweak.h              Private SpringBoard declarations
easyswipeprefs/      Settings bundle (PreferenceLoader)
layout/              Activator event metadata
sim/                 Simulator-only Settings loader
```

## How it works

EasySwipe hooks `-[SBSearchGesture searchScrollViewShouldRecognize:]`, which SpringBoard asks when a pull down starts on the home screen. If the touch is in a zone that isn't set to Spotlight, the hook runs that zone's action and returns `NO` so Spotlight stays closed.

## Credits

Inspired by [EZSwipe](https://repo.packix.com/package/com.imkpatil.ezswipe) by P2KDev.
