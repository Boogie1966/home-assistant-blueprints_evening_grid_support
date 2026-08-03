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

### Growatt (SPH / MOD / MIN TL-XH / WIT)

> ⚠️ **Olvasd el ezt a bekezdést, mielőtt bármit átmásolsz.**
>
> A Growattnál a „**Grid First**" elnevezés két különböző dolgot jelent a két elterjedt dokumentációban. A Home Assistant beépített `growatt_server` integrációja szerint a `grid_first` azt jelenti, hogy *az inverter a hálózatra exportál, és ehhez kisüti az akkut* – ez az, amit ez a blueprint akar. A `Growatt_ModbusTCP` integráció dokumentációja viszont az SPH 1044-es regiszterének 2-es értékét úgy írja le, hogy „**a hálózat töltheti az akkut**" – ami pont az ellentéte, és Magyarországon HMKE-keretben **nem megengedett**.
>
> Ez firmware- és modellfüggő. **Először teszteld** üresen hagyott blueprinttel, kézzel átkapcsolva a módot, és nézd meg egy fogyasztásmérőn vagy az inverter kijelzőjén, hogy **melyik irányba folyik az energia**. Csak azután élesítsd.

Két út van, és érdemes tudatosan választani közülük.

**1. Beépített `growatt_server` integráció (felhő, API tokennel)**

Ez a HA-ban alapból benne van, de a Growatt felhőjén keresztül dolgozik: 5 percenként kérdez le, és az írás is a felhőn megy át. Felhasználónév/jelszó helyett **API tokent használj**, mert a klasszikus API-nál a gyakori kérés akár 24 órás fióklezárást is okozhat.

MIN sorozatnál időszak-alapú a vezérlés:

```yaml
# Indító – a 2-es idősáv exportra állítása
- action: growatt_server.update_time_segment
  data:
    device_id: <a_min_inverter_device_id>
    segment_id: 2
    batt_mode: grid_first
    start_time: "19:00"
    end_time: "22:00"
    enabled: true
- action: number.set_value
  target: {entity_id: number.growatt_discharge_power}
  data: {value: 80}          # %, a kisütési teljesítmény
- action: number.set_value
  target: {entity_id: number.growatt_discharge_stop_soc}
  data: {value: 30}          # %, egyezzen a blueprint tartalék SoC-jével

# Teljesítmény-visszavétel
- action: number.set_value
  target: {entity_id: number.growatt_discharge_power}
  data: {value: 40}

# Leállító
- action: growatt_server.update_time_segment
  data:
    device_id: <a_min_inverter_device_id>
    segment_id: 2
    batt_mode: load_first
    start_time: "19:00"
    end_time: "22:00"
    enabled: false
```

SPH sorozatnál ehelyett a `growatt_server.write_ac_discharge_times` szolgáltatás van, `discharge_power`, `discharge_stop_soc` és `period_1_start` / `period_1_end` / `period_1_enabled` mezőkkel.

**2. `Growatt_ModbusTCP` HACS integráció (helyi, felhő nélkül)**

Ez a jobb választás ehhez a feladathoz: helyben, azonnal ír, nem függ a Growatt felhőjétől. Modellcsaládonként más a vezérlési logika.

**SPH GEN3 és MIN TL-XH** – egyetlen globális üzemmód-választó (1044-es regiszter):

```yaml
# Indító
- action: select.select_option
  target: {entity_id: select.growatt_priority_mode}
  data: {option: "Grid First"}       # !! előbb ellenőrizd az irányt, lásd a figyelmeztetést

# Leállító
- action: select.select_option
  target: {entity_id: select.growatt_priority_mode}
  data: {option: "Load First"}
```

**MOD GEN4 (TL3-XH)** – 9 idősávos TOU-ütemezés. Fontos: a TOU-írások **csendben visszaállnak**, ha az „Allow Grid Charge" (3049-es regiszter) nincs engedélyezve – ezt egyszer, kézzel kell bekapcsolni:

```yaml
# Indító
- action: select.select_option
  target: {entity_id: select.growatt_tou_period_1_priority}
  data: {option: "Grid Priority"}
- action: select.select_option
  target: {entity_id: select.growatt_tou_period_1_enable}
  data: {option: "Enabled"}

# Leállító
- action: select.select_option
  target: {entity_id: select.growatt_tou_period_1_priority}
  data: {option: "Load Priority"}
```

**WIT TL3** – itt VPP-protokoll van, és minden parancs **időkorlátos felülbírálás**, nem tartós módváltás. Ez a blueprinthez kifejezetten kényelmes, mert magától lejár. Egyszeri előfeltétel: a `select.growatt_control_authority` legyen `Enabled`.

```yaml
# Indító – 3 órás kisütés a hálózatra
- action: number.set_value
  target: {entity_id: number.growatt_remote_charging_time}
  data: {value: 180}         # perc
- action: number.set_value
  target: {entity_id: number.growatt_remote_charge_discharge_power}
  data: {value: -80}         # %, NEGATÍV = kisütés
- action: select.select_option
  target: {entity_id: select.growatt_remote_power_control_enable}
  data: {option: "Enabled"}

# Leállító
- action: select.select_option
  target: {entity_id: select.growatt_remote_power_control_enable}
  data: {option: "Disabled"}
```

A WIT-nél az integráció **30 másodperces minimum szünetet** tart a vezérlőírások között; az ennél sűrűbb parancsokat eldobja. A blueprint alapbeállításaival ez nem probléma.

**Amire még figyelj Growattnál:**

- **A ShineWiFi dongle felülírhatja a beállításokat.** Ha az érték ~60 másodperc után visszaáll, jellemzően a felhő szinkronja írta felül. Ha a firmware engedi, kapcsold ki a dongle felhő-szinkronját.
- **SPH 8000–10000 TL-HU:** ezen a hardveren az akkumulátor-vezérlő regiszterek (1044, 1070–1071, 1090–1108) Modbus hibát adnak vissza – ezeket csak az inverter kijelzőjéről vagy a ShinePhone appból lehet állítani.
- **A hálózatra kötött, akku nélküli modelleknek (MIN TL-X, MIC, MID) nincs akkumulátor-vezérlésük**, ezekre a blueprint nem alkalmazható.

### Ha nincs HA-ból vezérelhető inverter

Sok gyári alkalmazásban van „Time of Use" / idősáv-beállítás. Ott állítsd be kézzel a 19–22 sávot kisütésre, a többit hagyd változatlanul – a blueprint ilyenkor nem kell. **A szórás viszont így is számít: ne 19:00-t válaszd, hanem egy random időpontot 18:45 és 19:30 között.**

---
