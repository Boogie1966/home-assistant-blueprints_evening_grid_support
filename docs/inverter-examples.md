# Inverter-specific action examples

> ⚠️ **Entity IDs below are illustrative.** They differ between integrations, firmware versions and installations. Look yours up under **Developer tools → States** — do not copy blindly.
>
> Working configurations for other inverters are very welcome: open an issue or PR.

*A magyar magyarázatok megmaradtak a kódpéldák mellett — a YAML maga nyelvfüggetlen.*

> ⚠️ **Az alábbi entitásnevek illusztratívak.** Integrációnként, firmware-verziónként és telepítésenként eltérnek – a **Fejlesztői eszközök → Állapotok** alatt keresd meg a sajátjaidat, ne másold be vakon.

### Victron (GX / ESS – Modbus TCP vagy `victron` integráció)

A legtisztább eset: az **ESS grid setpoint** negatívra állítása export.

```yaml
# Indító parancsok
- action: number.set_value
  target:
    entity_id: number.victron_ess_grid_setpoint
  data:
    value: -2000        # W, negatív = hálózatra táplálás

# Teljesítmény-visszavétel
- action: number.set_value
  target:
    entity_id: number.victron_ess_grid_setpoint
  data:
    value: -1000

# Leállító parancsok
- action: number.set_value
  target:
    entity_id: number.victron_ess_grid_setpoint
  data:
    value: 0            # vissza az öntfogyasztás-optimalizált alapállapotba
```

Ha az ESS minimum SoC-t is állítod, azt a leállító ágban szintén állítsd vissza.

### Solax (`solax_modbus` HACS integráció)

```yaml
# Indító
- action: select.select_option
  target: {entity_id: select.solax_charger_use_mode}
  data: {option: "Manual Mode"}
- action: select.select_option
  target: {entity_id: select.solax_manual_mode_select}
  data: {option: "Force Discharge"}
- action: number.set_value
  target: {entity_id: number.solax_battery_discharge_max_current}
  data: {value: 25}

# Leállító
- action: select.select_option
  target: {entity_id: select.solax_charger_use_mode}
  data: {option: "Self Use Mode"}
```

### Deye / Sunsynk (`solarman` vagy `deye` integráció)

A Deye-nál a „Solar Sell” kapcsoló + a TOU-idősáv SoC-je a kulcs:

```yaml
# Indító
- action: switch.turn_on
  target: {entity_id: switch.deye_solar_sell}
- action: number.set_value
  target: {entity_id: number.deye_sell_time_point_5_power}   # a 19:00-s idősáv
  data: {value: 3000}
- action: number.set_value
  target: {entity_id: number.deye_sell_time_point_5_soc}
  data: {value: 30}

# Leállító
- action: number.set_value
  target: {entity_id: number.deye_sell_time_point_5_power}
  data: {value: 0}
- action: switch.turn_off
  target: {entity_id: switch.deye_solar_sell}
```

### Huawei (`huawei_solar` HACS integráció – LUNA2000)

Itt van dedikált szolgáltatás:

```yaml
# Indító
- action: huawei_solar.forcible_discharge
  target: {device_id: <az_inverter_device_id>}
  data:
    power: 3000          # W
    duration: 180        # perc

# Leállító
- action: huawei_solar.stop_forcible_charge
  target: {device_id: <az_inverter_device_id>}
```

A `duration` miatt ez önmagában is leáll – de a leállító ág legyen ott, mert a SoC-tartalék elérésekor korábban kell megszakítani.

### SolarEdge (`solaredge_modbus_multi`)

```yaml
# Indító
- action: select.select_option
  target: {entity_id: select.solaredge_i1_storage_command_mode}
  data: {option: "Discharge to Maximize Export"}
- action: number.set_value
  target: {entity_id: number.solaredge_i1_storage_command_timeout}
  data: {value: 10800}   # 3 óra másodpercben
- action: number.set_value
  target: {entity_id: number.solaredge_i1_storage_discharge_limit}
  data: {value: 3000}

# Leállító
- action: select.select_option
  target: {entity_id: select.solaredge_i1_storage_command_mode}
  data: {option: "Maximize Self Consumption"}
```

### Sungrow (mkaiser Modbus csomag)

```yaml
# Indító
- action: select.select_option
  target: {entity_id: select.sungrow_ems_mode}
  data: {option: "Forced mode"}
- action: select.select_option
  target: {entity_id: select.sungrow_battery_forced_charge_discharge_cmd}
  data: {option: "Forced discharge"}
- action: number.set_value
  target: {entity_id: number.sungrow_battery_forced_charge_discharge_power}
  data: {value: 3000}

# Leállító
- action: select.select_option
  target: {entity_id: select.sungrow_ems_mode}
  data: {option: "Self-consumption mode (default)"}
```

### Ha nincs HA-ból vezérelhető inverter

Sok gyári alkalmazásban van „Time of Use” / idősáv-beállítás. Ott állítsd be kézzel a 19–22 sávot kisütésre, a többit hagyd változatlanul – a blueprint ilyenkor nem kell. **A szórás viszont így is számít: ne 19:00-t válaszd, hanem egy random időpontot 18:45 és 19:30 között.**

---
