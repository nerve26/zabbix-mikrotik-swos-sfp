# Zabbix Template: MikroTik SwOS SFP Monitoring (HTTP)

Template for monitoring SFP/SFP+ optics on MikroTik SwOS devices using hidden HTTP endpoints (`/sfp.b` and `/link.b`).

## Why this exists

MikroTik SwOS does not expose full SFP diagnostics like RX/TX optical power over SNMP on many devices.

This template solves that by:
- querying hidden HTTP endpoints
- parsing data directly inside Zabbix
- using low-level discovery (LLD)
- creating per-port monitoring items and triggers

## Features

- RX power (dBm)
- TX power (dBm)
- Temperature (°C)
- Voltage (V)
- TX Bias (mA)
- Auto-discovery (LLD)
- Proper port names from `link.b`
- Triggers for:
  - low RX power
  - average RX degradation
  - no light / disconnected fiber
  - high TX bias
  - high temperature

## Requirements

- MikroTik SwOS switch
- Zabbix 7.x
- HTTP access to the switch web interface
- valid SwOS credentials

## Template macros

Set these after import:

| Macro | Example | Description |
|------|---------|-------------|
| `{$MIKROTIK.SFP.URL}` | `http://<SWOS-IP>/sfp.b` | SFP diagnostics endpoint |
| `{$MIKROTIK.LINK.URL}` | `http://<SWOS-IP>/link.b` | Link/port names endpoint |
| `{$MIKROTIK.SFP.USER}` | `admin` | SwOS username |
| `{$MIKROTIK.SFP.PASS}` | `change_me` | SwOS password |

## Default trigger thresholds

| Macro | Default |
|------|---------|
| `{$MIKROTIK.SFP.RX.WARN}` | `-15` |
| `{$MIKROTIK.SFP.RX.AVG.WARN}` | `-14` |
| `{$MIKROTIK.SFP.RX.DISCONNECT}` | `-35` |
| `{$MIKROTIK.SFP.BIAS.WARN}` | `50` |
| `{$MIKROTIK.SFP.TEMP.WARN}` | `70` |

## RX power interpretation

| RX power | Meaning |
|---------|---------|
| `-3 to -10 dBm` | Excellent |
| `-10 to -15 dBm` | OK / weaker |
| `-15 to -25 dBm` | Weak signal |
| `<= -35 dBm` | No light / disconnected fiber |

## No light / disconnected fiber

The trigger for `No light / disconnected fiber` usually means one of these:
- fiber unplugged
- broken fiber
- remote side down
- unused inserted SFP module
- dark port with no incoming light

It does **not** distinguish between unplugged and physically broken fiber. It only means the receiver sees no light.

## Notes

- This uses undocumented SwOS endpoints, so MikroTik may change behavior in future versions.
- Port naming comes from `link.b`
- Optical diagnostics come from `sfp.b`

## Import

1. Import the YAML template into Zabbix
2. Set the macros
3. Link the template to your host
4. Execute the master HTTP items if needed
5. Wait for LLD to create discovered items

## File

Template file included in this repository:
- `MikroTik SwOS SFP Monitoring (HTTP, LLD).yaml`

## License

Licensed under MIT.
