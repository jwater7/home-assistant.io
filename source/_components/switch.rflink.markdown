---
layout: page
title: "RFLink Switch"
description: "Instructions on how to integrate RFLink switches into Home Assistant."
date: 2016-01-04
sidebar: true
comments: false
sharing: true
footer: true
logo: rflink.png
ha_category: Switch
ha_release: 0.38
---

The `rflink` component support devices that use [RFLink gateway firmware](http://www.nemcon.nl/blog2/), for example, the [Nodo RFLink Gateway](https://www.nodo-shop.nl/nl/21-rflink-gateway). RFLink gateway is an Arduino firmware that allows two-way communication with a multitude of RF wireless devices using cheap hardware (Arduino + transceiver).

## {% linkable_title Configuration %}

First, you have to set up your [rflink hub](/components/rflink/).

The RFLink component does not know the difference between a `switch` and a `light`. Therefore all switchable devices are automatically added as `light` by default.

RFLink switch/light ID's are composed of: protocol, id, switch. For example: `newkaku_0000c6c2_1`.

Once the ID of a switch is known it can be used to configure it as a switch type in HA, for example, to add it to a different group, hide it or configure a nice name.

Configuring a device as switch with a nice name:

```yaml
# Example configuration.yaml entry
switch:
  platform: rflink
  device_defaults:
    fire_event: true
    signal_repetitions: 2
  devices:
    newkaku_0000c6c2_1:
      name: Ceiling fan
    conrad_00785c_0a:
      name: Motion sensor kitchen
```

{% configuration %}
device_defaults:
  description: The defaults for all listed devices.
  required: false
  type: list
  keys:
    fire_event:
      description: Set default `fire_event` for Rflink switch devices (see below).
      required: false
      type: boolean
    signal_repetitions:
      description: Set default `signal_repetitions` for Rflink switch devices (see below).
      required: false
      type: integer
devices:
  description: A list of devices with their name to use in the frontend.
  required: false
  type: list
  keys:
    name:
      description: Name for the device.
      required: false
      default: Rflink ID
      type: string
    aliases:
      description: (deprecated) Alternative Rflink ID's this device is known by.
      required: false
      type: [list, string]
    group_aliases:
      description: "(deprecated) `aliases` which only respond to group commands."
      required: false
      type: [list, string]
    no_group_aliases:
      description: "(deprecated) `aliases` which do not respond to group commands."
      required: false
      type: [list, string]
    fire_event:
      description: Fire a `button_pressed` event if this device is turned on or off.
      required: false
      default: false
      type: boolean
    signal_repetitions:
      description: Set default `signal_repetitions` for RFLink switch devices (see below).
      required: false
      default: 1
      type: integer
    group:
      description: Allow light to respond to group commands (ALLON/ALLOFF).
      required: false
      default: true
      type: boolean
{% endconfiguration %}

## {% linkable_title Switch state %}

Initially, the state of a switch is unknown. When the switch is turned on or off (via frontend or wireless remote) the state is known and will be shown in the frontend.

Sometimes a switch is controlled by multiple wireless remotes, each remote has its own code programmed in the switch. To allow tracking of the state when switched via other remotes add the corresponding remote codes as aliases:

```yaml
# Example configuration.yaml entry
switch:
  platform: rflink
  devices:
    newkaku_0000c6c2_1:
      name: Ceiling fan
      icon: mdi:fan
      aliases:
        - newkaku_000000001_2
        - kaku_000001_a
```

Any on/off command from any alias ID updates the current state of the switch. However, when sending a command through the frontend only the primary ID is used.

## {% linkable_title Device support %}

See [device support](/components/rflink/#device-support)
