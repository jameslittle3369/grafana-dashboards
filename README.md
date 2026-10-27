# MiCasa Grafana dashboards

Exported dashboard JSON for the MiCasa Grafana instance on `rpi4-app`.
Every panel reads from the `sensors-backend-fastapi` Postgres database
(schema `api`). These files hold no credentials: the database login lives
only in Grafana's datasource config, and the dashboards point at that
datasource by its UID.

All dashboards use Grafana's v2 dashboard schema
(`apiVersion: dashboard.grafana.app/v2`) and share the `micasa` tag, so
the "MiCasa Dashboards" link dropdown on each one lists the others.

---

## Dashboards

| File | Title | Main tables |
|---|---|---|
| `grafana-micasa-updated.json` | MiCasa | Overview across most tables below |
| `grafana-micasa-energy.json` | MiCasa - Energy (Emporia) | `energy_circuits`, `energy_circuit_logs` |
| `grafana-micasa-tplink.json` | MiCasa - TP-Link / Kasa | `energy_circuits`, `energy_circuit_logs` |
| `grafana-micasa-hvac.json` | MiCasa - HVAC | `hvac_zone_logs` |
| `grafana-micasa-indoor-climate.json` | MiCasa - Indoor Climate | `tstats_thermometer`, `tstats_thermohygrometer`, `aqsensors_aqlog` |
| `grafana-micasa-garden.json` | MiCasa - Indoor Garden | `tstats_thermometer`, `tstats_thermohygrometer` |
| `grafana-micasa-weather.json` | MiCasa - Weather / Outdoor | `anemometer*`, `raingauge*`, `photo_uvmeter*`, `tstats_*` |
| `grafana-micasa-weather-extras.json` | MiCasa - Weather Extras | same as Weather, plus `aqsensors_aqlog` |
| `grafana-micasa-device-health.json` | MiCasa - Device Health | `ikea_devices`, `ring_devices` and their `*_device_logs` |
| `grafana-micasa-sun.json` | MiCasa - Sun | `almanac_days` |

`battery_device_names.json` is not a dashboard. It's the device list the
Device Health battery panel is built from. Add a name there when hardware
is added or replaced.

## Importing

Use **Dashboards → New → Import**, then upload or paste the file.

Don't paste a whole file into an existing dashboard's **Settings → JSON
Model**. That editor refuses any change to `metadata` ("Editing
dashboard metadata is not yet supported (namespace)"). To update an
existing dashboard in place, keep its `apiVersion`, `kind` and
`metadata` blocks as Grafana shows them, and replace only `spec`.

### On a fresh Grafana

The panels reference the Postgres datasource by UID (`cfvq4sip0ynlsf`).
A new Grafana instance will give its datasource a different UID, so
either create the datasource with that UID, or find-and-replace the UID
in these files before importing.

## Sun dashboard notes

- The House Compass panel is a Text panel in HTML mode. It embeds an
  `<img>` of the backend's
  `http://10.0.0.13/v1/almanac-days/home/compass.svg?day=${day}`
  endpoint, so it only renders on the LAN. If the image is blank, open
  that URL directly before suspecting Grafana.
- `$day` is filled from `almanac_days` as `YYYY-MM-DD` text. Use it as
  plain `${day}` in SQL. The `${day:date}` format reformats it and stops
  it from matching the `day` column.

## Exporting changes

After editing a dashboard in Grafana, export it (**Share → Export →
Export as JSON**, v2 format), overwrite the file here, and commit.
