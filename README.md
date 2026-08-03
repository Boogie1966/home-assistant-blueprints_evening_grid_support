# Home Assistant Blueprints

Blueprints and packages for Home Assistant. *Magyar leírás: [docs/hu/README.hu.md](docs/hu/README.hu.md)*

---

## Evening grid-support battery discharge (vendor-agnostic)

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FBoogie1966%2Fhome-assistant-blueprints_evening_grid_support%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fboogie1966%2Fevening_grid_support_discharge.yaml)

In summer, the hardest hours for the grid are not at noon but between 19:00 and 22:00 — the sun is down and PV output has collapsed, but air conditioning, cooking and EV charging are still running. That gap is filled with gas peakers and imports.

If your home battery is still partly full every morning, that stored energy is sitting idle. This blueprint discharges it during the evening peak, down to a configurable reserve SoC, then reliably restores normal operation.

### Vendor-agnostic by design

The blueprint provides only the **logic** — scheduling, jitter, SoC guards, error handling, restoration. The inverter-specific commands go into three `action` selector inputs, so the same file works on Victron, Solax, Deye/Sunsynk, Huawei, SolarEdge, Sungrow and Growatt.

👉 **[Per-brand starting examples: docs/inverter-examples.md](docs/inverter-examples.md)**

### How it works

| Trigger | Behaviour |
|---|---|
| `start_time` (default 19:00) | 0–N minute **random jitter** → guards → start actions |
| `taper_time` (default 21:45) | Taper actions — soft ramp-down |
| `end_time` (default 22:00) **or** SoC < reserve | Stop actions, **unconditionally** |
| Home Assistant start | Inside window: resume (no jitter). Outside window: **stop** |

That last row is the important edge case: if a power cut or a restart left the inverter in export mode, the automation self-heals on the next start. `mode: restart` ensures a low-SoC trigger arriving *during* the jitter delay is not queued behind it.

### Safeguards

- **Random start jitter** (default 0–45 min)
- **Minimum starting SoC** (default 70%) — won't start on a half-empty battery
- **Reserve SoC** (default 30%) — keeps headroom for overnight use and backup duty
- **Optional tomorrow-PV-forecast condition** (Forecast.Solar / Solcast) — don't empty the battery ahead of a cloudy day
- **`unavailable` / `unknown` handling** — won't fire blind if the inverter integration drops out
- **Idempotent stop path** — runs unconditionally

### Why the jitter matters

If thousands of homes engage at exactly 19:00:00 and drop out at exactly 22:00:00, that is two sharp ramps for the transmission system operator — the opposite of helpful. **Please leave the randomisation enabled.**

### Requirements

Home Assistant **2024.6.0** or newer.

---

## Companion package: contribution counter

[`packages/evening_grid_support.yaml`](packages/evening_grid_support.yaml)

Measures what you actually contributed, using `min(grid export, battery discharge)` inside the evening window — so PV that is still producing at 19:00 is not counted as a battery contribution. Riemann-integrated to kWh, with daily / monthly / total utility meters, plus revenue, avoided CO₂, and a ready-made dashboard card.

Edit the two placeholder entity IDs at the top of the file, drop it in `config/packages/`, and make sure your `configuration.yaml` contains:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

---

## ⚠️ Please read before enabling

1. **Grid export must be permitted for your installation.** Many systems run with export limiting (zero-export / reverse-power protection) either by regulation or by grid-connection agreement. If yours does, this blueprint is not applicable.
2. **Charge the battery from PV only.** Charging from the grid and re-selling later is licensed energy trading in most jurisdictions, not self-generation.
3. **Check your tariff scheme first — this may cost you money.** Under gross metering, the feed-in rate is typically a small fraction of the import rate, so exporting stored energy is a net loss. Under net metering it is roughly neutral.
4. **The honest counter-argument:** if your battery simply covers your *own* evening consumption, you have already removed almost as much load from the grid. Export only adds the surplus above your own evening demand.
5. **Test with empty action fields first** and inspect the automation traces before going live.

---

## Installation

**Blueprint:** click the badge above, or **Settings → Automations & scenes → Blueprints → Import blueprint** and paste the URL of the YAML file.

**Manual:** copy the YAML to `config/blueprints/automation/boogie1966/`, then **Reload blueprints**.

To pick up later fixes, use the blueprint's overflow menu → **Re-import blueprint**.

---

## Contributing

Working configurations for inverters not yet covered are very welcome — open an issue or a PR with your `start_actions` / `stop_actions` and the integration you use.

## License

MIT — see [LICENSE](LICENSE).
