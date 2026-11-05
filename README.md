# V2C Trydan — Home Assistant Integration

[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/hacs/integration)
[![GitHub release](https://img.shields.io/github/release/quovadiskt/V2C_trydan.svg)](https://github.com/quovadiskt/V2C_trydan/releases/)
[![Maintained](https://img.shields.io/badge/Maintained-yes-green.svg)](https://github.com/quovadiskt/V2C_trydan)
[![en](https://img.shields.io/badge/lang-en-red.svg)](https://github.com/quovadiskt/V2C_trydan/blob/main/README.md)
[![es](https://img.shields.io/badge/lang-es-yellow.svg)](https://github.com/quovadiskt/V2C_trydan/blob/main/README.es.md)

> **This is an actively maintained fork** of the original [Rain1971/V2C_trydant](https://github.com/Rain1971/V2C_trydant) integration, which was archived by its author due to hardware loss. The project is alive again! 🚀
>
> **Fork maintained by [@quovadiskt](https://github.com/quovadiskt)**

This integration exposes the information and functions made available by [V2C trydan](https://v2charge.com/trydan/) directly via HTTP interface in Home Assistant.

## What's new in v4.1.0

- 🐛 Fixed infinite recursion in `set_dynamic_power_mode` service handler
- 🐛 Fixed `NoneType` crash in `ChargeKmSensor` when state is `None`
- 🐛 Fixed broken `extra_state_attributes` writes in `PrecioLuzEntity`
- 🧹 Removed unused imports and duplicate property definitions from `sensor.py`
- 📝 Fixed 5 typos in service descriptions
- 🔧 Updated codeowners and documentation URLs to this fork

# Prerequisites

This integration supports network connection to V2C trydan directly, so take note of the V2C trydan static IP address previously.

For a later configuration, you need to know the electrical consumption data of your car expressed in kWh per 100 km.

If you want to use the price-based load control feature, you must install the following integrations for Lovelace, from HACS:
- [PVPC Hourly Pricing Card](https://github.com/danimart1991/pvpc-hourly-pricing-card) 
- [multiple-entity-row](https://github.com/benct/lovelace-multiple-entity-row)

# Setup

* Add this repository in HACS as a custom repository:
  `https://github.com/quovadiskt/V2C_trydan`

<img src="./images/install1.png" width="300">

<img src="./images/install2.png" width="350">

* Restart Home Assistant
* Go to Configuration -> Integrations -> Add Integration

<img src="./images/install3.png" width="220">
<img src="./images/install4.png" width="350">

* Add integration and put there your device IP
* Go to the V2C integration. Now, there are 29 entities. Press on settings and set:
   - kWh x 100km of your car (default: 20.8 kWh/100km)
   - Sensor.pvpc → (add this only if you want to control your car charge based on the price of electricity. See PVPC Hourly Pricing Card)

   <img src="./images/install5.png" width="350">

* Press 'Send' and it creates a new entity: sensor.v2c_precio_luz. Now there are 30 entities.
* Restart Home Assistant

# Entities

The following entities are created:

| Name                               | Type    | R/W  | Units        | Description                                    |
| :--------------------------------- | :------ | :--- | :----------- | :--------------------------------------------- |
| v2c_trydan_sensor_chargeenergy     | Sensor | R   | N kWh      | Current charging session energy in kWh.        
| v2c_trydan_sensor_chargekm / v2c_km_to_charge | Sensor / Number | R / W | N km | Current charging session energy in km.
| v2c_trydan_sensor_chargepower      | Sensor | R   | N W        | Current charging power in Watts.
| v2c_trydan_sensor_chargestate      | Sensor | R   | S `values`    | Charge Point. Spanish string states: `Manguera no conectada`, `Manguera conectada (NO CARGA)`, `Manguera conectada (CARGANDO)`
| v2c_trydan_numericalstatus         | Sensor | R   | N `values`    | Charge Point. Numerical status: `0`-Hose not connected, `1`-Hose connected (not charging), `2`-Hose connected (charging)
| v2c_trydan_sensor_chargetime       | Sensor | R   | N s        | Current charging session time. 
| v2c_trydan_sensor_contractedpower  | Sensor | R   | N W        | House contracted power in Watts. Default `-1`
| vc2_trydan_sensor_dynamic          | Sensor | R   | N `values`    | Dynamic intensity modulation state: `0`-Disabled, `1`-Enabled
| vc2_trydan_sensor_dynamicpowermode | Sensor | R   | N `values`    | Dynamic mode: `0`-Timed power enabled; `1`-Timed power disabled, `2`-Exclusive mode, `3`-Min power mode, `4`-Grid+FV mode, `5`-Stop mode
| v2c_dynamic_power_mode             | Select | R/W | `options`     | Dynamic power mode selector
| vc2_trydan_sensor_fvpower          | Sensor | R   | N W        | Photovoltaic power generation in Watts.
| vc2_trydan_sensor_housepower       | Sensor | R   | N W        | House power consumption in Watts.
| v2c_trydan_sensor_intensity / v2c_intensity | Sensor / Number | R / W | N A | Intensity offered by charge point in Amps (if dynamic charge disabled)
| v2c_trydan_sensor_locked           | Sensor | R   | N `values`    | Disabling state: `0`-Enabled, `1`-Disabled 
| v2c_trydan_sensor_maxintensity / v2c_max_intensity | Sensor / Number | R / W | N A | Max intensity limit in Amps (if dynamic charge enabled, default 32A)
| v2c_trydan_sensor_minintensity / v2c_min_intensity | Sensor / Number | R / W | N A | Min intensity limit in Amps (if dynamic charge enabled, default 6A)
| v2c_trydan_sensor_paused           | Sensor | R   | N `values`    | Pause state: `0`-Enabled, `1`-Disabled                
| v2c_trydan_sensor_pausedynamic     | Sensor | R   | N `values`    | Dynamic control pause state: `0`-Modulating, `1`-Not modulating
| v2c_trydan_sensor_slaveerror       | Sensor | R   | N `values`    | Slave communication state: `0`-No error, `1`-Error message, `2`-Communication error
| v2c_trydan_sensor_timer            | Sensor | R   | N `values`    | Timer state: `1`-Timer ON, `0`-Timer OFF
| v2c_precio_luz                     | Sensor | R   | state `attributes` | Data from api.esios.ree.es (REE). Contains current price and many attributes including `ValidHours`, `ValidHoursNextDay`, `TotalHours`.
| vc2_trydan_switch_dynamic          | Switch | R/W | `on` `off`    | Toggle dynamic charge. Default `off`                       
| v2c_trydan_switch_paused           | Switch | R/W | `on` `off`    | Toggle pause charge. Default `off`                        
| v2c_trydan_switch_locked           | Switch | R/W | `on` `off`    | Toggle block the charger. Default `off`
| v2c_trydan_switch_v2c_carga_pvpc   | Switch | R/W | `on` `off`    | Toggle PVPC price-limited charging. Default `off`
| v2c_trydan_switch_v2c_smart_charge | Switch | R/W | `on` `off`    | Toggle smart charge algorithm. Default `off`

# Events

| Event                              | Description                                   |
| :--------------------------------- |:--------------------------------------------- |
| v2c_trydan.charging_complete       | Triggered when the energy for the selected kilometers has been charged.

# Examples

* Automation triggered when charging is complete:

```yaml
alias: CARGA COCHE COMPLETA
description: CARGA COMPLETA
trigger:
  - platform: event
    event_type: v2c_trydan.charging_complete
action:
  - service: notify.notify
    data:
      message: La carga del vehículo ha alcanzado el límite de kilómetros.
  - service: notify.pushover
    data:
      message: KM del coche CARGADOS
      title: CARGADOR!
      data:
        priority: 1
    enabled: true
mode: single
```

# Screen Examples

* Use [example.yaml](https://raw.githubusercontent.com/quovadiskt/V2C_trydan/main/lovelance/example.yaml)
* For PVPC control: [example_pvpc.yaml](https://raw.githubusercontent.com/quovadiskt/V2C_trydan/main/lovelance/example_pvpc.yaml)
* For PVPC control, compact version: [example_pvpc_compact.yaml](https://raw.githubusercontent.com/quovadiskt/V2C_trydan/main/lovelance/example_pvpc_compact.yaml)

![Charts](./images/example_pvpc_compact.png)

---

# Related Projects & Ecosystem

| Project | Type | Description |
|:--------|:-----|:------------|
| ⚡ [V2C Trydan Card](https://github.com/mactron254/v2c-trydan-card) | Lovelace card | Beautiful dashboard card for V2C Trydan with visual editor, 11 languages, energy monitoring. Install via HACS. |
| 🔧 [Carbanog/V2C_trydan](https://github.com/Carbanog/V2C_trydan) | HACS integration | Alternative community integration with enhanced polling, session statistics, blueprints and diagnostics. Uses same `v2c_trydan` domain. |
| ☁️ [HomeAssistant-V2C-Cloud](https://github.com/samuelebistoletti/HomeAssistant-V2C-Cloud) | HACS integration | Cloud-based integration (hybrid cloud + LAN). Best option if you have OCPP enabled (which disables local HTTP). |
| ☁️ [v2c_cloud (lockevod)](https://github.com/lockevod/v2c_cloud) | HACS integration | Another cloud-only integration solving the OCPP / local HTTP conflict. |
| 🏠 [Official V2C integration](https://www.home-assistant.io/integrations/v2c/) | HA Core | Built-in HA integration (since 2023.12). Fewer entities than this custom integration but no HACS required. |

> **Which one should I use?**
> - **This fork** (`quovadiskt/V2C_trydan`) — if you want the original Rain1971 feature set (PVPC price control, smart charge algorithm, 46 entities) with active bug fixes
> - **Carbanog fork** — if you want a fully rewritten integration with better reliability on weak networks
> - **Cloud integrations** — if you have OCPP enabled on your Trydan
> - **Official HA integration** — if you prefer zero custom components and don't need PVPC features

---

*Originally created by [@Rain1971](https://github.com/Rain1971). Forked and maintained by [@quovadiskt](https://github.com/quovadiskt).*
