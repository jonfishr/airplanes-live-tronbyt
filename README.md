# Overhead

A [Pixlet](https://github.com/tidbyt/pixlet) app for a 64×32 Tronbyt/Tidbyt display that shows the
**single closest aircraft** to a location, using an [Airplanes.live](https://airplanes.live) feed.
One readable plane beats a cramped list. Built for personal, non-commercial use against a private
feeder — no API key required.

![preview](preview.webp)

The frame, top to bottom:

- **Callsign** in amber (falls back to registration, then ICAO hex). Scrolls if it overflows.
- **Type · altitude** — e.g. `B738 · 37000ft`, or `GND` when on the ground.
- **Distance · bearing · speed** — e.g. `4.2nm NW · 410kt`.
- A small **plane glyph rotated to the aircraft's heading** (`track`).

If any aircraft is squawking an emergency code, it is surfaced ahead of the closest plane and flagged
(`7700 EMERG`, `7600 RADIO`, `7500 HIJACK`) with the whole frame in red.

## Configuration

| Field | Default | Notes |
|-------|---------|-------|
| **Location** | OKC metro center | Center point to search around. Set your own. |
| **Radius (nm)** | `10` | Search radius in nautical miles (clamped 1–250). |
| **Altitude units** | Feet | Feet or meters. |
| **Speed units** | Knots | Knots or MPH. **Distance follows this** — knots → `nm`, MPH → statute `mi`. |
| **Airborne only** | off | Skip aircraft on the ground. |
| **Highlight emergencies** | on | Surface any 7500/7600/7700 squawk first. |

The app fetches `https://api.airplanes.live/v2/point/{lat}/{lon}/{radius}` with a 45-second cache TTL,
so device refreshes stay well under the API's 1 request/second limit. Records with a stale position
(>60s) are dropped. On a non-200 response it shows `NO SIGNAL`; with no aircraft in range, `NO AIRCRAFT`.

## Preview locally

```sh
# Live, interactive preview (configure fields in the browser):
pixlet serve overhead.star

# Render a single frame to a WebP:
pixlet render overhead.star && open overhead.webp

# Render with config (location is a JSON string; flags are key=value):
pixlet render overhead.star \
  location='{"lat":"35.47","lng":"-97.52"}' \
  radius=15 speed_units=mph only_airborne=true \
  -o preview.webp

# Validate before installing:
pixlet check overhead.star
```

## Install into Tronbyt

Drop `overhead.star` and `manifest.yaml` into your Tronbyt server's community-apps directory (the
same layout Tidbyt community apps use), then add **Overhead** to a device and configure it. Tronbyt
serves the same Pixlet render/schema/http API, so no changes are needed.

## v2 ideas (out of scope)

- Multiple aircraft / a scrolling list
- A mini map or position trails
- Push alerts on emergency squawks or specific types/registrations
