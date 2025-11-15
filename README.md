# BART Tiles - Real-Time Transit Plugin for TRMNL

A minimal, zero-infrastructure TRMNL plugin that displays real-time BART (Bay Area Rapid Transit) departure and arrival times directly on your e-ink device.

## Features

✨ **Real-time BART data** - Shows next 3 upcoming departures and arrival times
🚂 **Origin & Destination tracking** - See when you leave and when you arrive
🎨 **E-ink optimized** - Black & white design with gray accents for perfect readability
⚡ **Zero infrastructure** - No servers, no backends, no maintenance
📱 **Direct API calls** - TRMNL device communicates directly with BART's public API
🔧 **Easy setup** - Just copy & paste configuration into TRMNL

## Quick Start

### Installation

1. **Log into TRMNL** at https://dashboard.trmnl.com
2. **Create a new Private Plugin**
3. **Copy Form Fields** from `settings.yml` (see below)
4. **Select Template Layout** as "Full Screen"
5. **Paste Liquid Template** from `bart-tile-full.liquid` (see below)
6. **Save & Configure** - Select your BART origin and destination stations

### Configuration

#### Step 1: Add Form Fields

In TRMNL's "Form Fields" section, paste the content from **`settings.yml`**:

This file contains:
- **BART Origin Station** - Where you're boarding (dropdown with 49 stations)
- **BART Destination Station** - Where you're going (dropdown with 49 stations)

All 49 BART stations are included: FTVL, 19TH, LAKE, DBRK, EMBR, POWL, SFIA, and more.

#### Step 2: Add Display Template

In TRMNL's "Full Screen" layout section, paste the content from **`bart-tile-full.liquid`**:

This Liquid template will:
- Display your origin → destination as the header
- Show up to 3 upcoming trips in card format
- Display departure time, arrival time, and train line (e.g., "Richmond", "Concord")
- Handle errors gracefully with a warning message

#### Step 3: Configure Your Commute

Once saved, TRMNL will show configuration options:
- Select your **Origin Station** (e.g., Fruitvale)
- Select your **Destination Station** (e.g., 19th St. Oakland)
- Save configuration

TRMNL will now poll the BART API every 15 minutes (configurable in TRMNL device settings).

## How It Works

```
TRMNL Device
    ↓
BART Depart API (direct call)
https://api.bart.gov/api/sched.aspx?cmd=depart&orig=FTVL&dest=19TH&date=now&...
    ↓
Liquid Template parses response
    ↓
Renders on e-ink display
```

**Example API Response (Single-Leg Route):**
```json
{
  "root": {
    "origin": "FTVL",
    "destination": "19TH",
    "schedule": {
      "request": {
        "trip": [
          {
            "origTimeMin": "06:28 PM",
            "destTimeMin": "06:36 PM",
            "leg": {
              "trainHeadStation": "Richmond"
            }
          }
        ]
      }
    }
  }
}
```

**Example API Response (Multi-Leg Route with Transfer):**
```json
{
  "root": {
    "origin": "PLZA",
    "destination": "DALY",
    "schedule": {
      "request": {
        "trip": [
          {
            "origTimeMin": "06:28 PM",
            "destTimeMin": "06:52 PM",
            "leg": [
              {
                "trainHeadStation": "Richmond"
              },
              {
                "trainHeadStation": "Daly City"
              }
            ]
          }
        ]
      }
    }
  }
}
```

## Station Reference

**Common BART Stations:**
- FTVL = Fruitvale
- 19TH = 19th St. Oakland
- LAKE = Lake Merritt
- DBRK = Downtown Berkeley
- EMBR = Embarcadero
- POWL = Powell St.
- SFIA = San Francisco Int'l Airport

See `settings.yml` for all 49 stations.

## Files

| File | Purpose |
|------|---------|
| `settings.yml` | TRMNL plugin form fields (origin/destination stations) |
| `bart-tile-full.liquid` | E-ink display template (renders 3 upcoming trips - original) |
| `bart-tile-horizontal.liquid` | 4-column grid layout (6 trips, landscape display) |
| `bart-tile-vertical.liquid` | Stacked column layout (4 trips, portrait display) |
| `bart-tile-grid.liquid` | 2-column balanced layout (2 trips, medium display) |
| `README.md` | This file |

## Layout Variants

Choose the template that best fits your device display:

### bart-tile-horizontal.liquid (Landscape/Wide)
- **Grid Layout**: 4 columns (`grid--cols-4`)
- **Trip Limit**: 6 upcoming trips
- **Best For**: Wide displays, landscape orientation, ultra-portable devices
- **Card Size**: Compact with smaller text (0.9em departure time, 11px badges)
- **Features**: Maximum information density while maintaining readability

### bart-tile-vertical.liquid (Portrait/Narrow)
- **Layout**: Single column stacking (`layout--col`)
- **Trip Limit**: 4 upcoming trips
- **Best For**: Portrait orientation, tall narrow displays, phones
- **Card Size**: Full-width with readable text (1.4em departure time, 14px badges)
- **Features**: Excellent readability, clean vertical flow, proper spacing

### bart-tile-grid.liquid (Balanced/Medium)
- **Grid Layout**: 2 columns (`grid--cols-2`)
- **Trip Limit**: 2 upcoming trips
- **Best For**: Balanced displays, square screens, desktop monitors
- **Card Size**: Medium with good visibility (1.2em departure time, 14px badges)
- **Features**: Two-card display with optimal readability

### bart-tile-full.liquid (Original)
- **Grid Layout**: 3 columns (`grid--cols-3`)
- **Trip Limit**: 3 upcoming trips
- **Best For**: Standard displays, original configuration
- **Card Size**: Standard sizing

**To use a different layout:**
1. Log into TRMNL dashboard
2. Go to your plugin settings
3. In the "Full Screen" template section, replace the template content with your chosen layout file
4. Save and refresh your device

## Troubleshooting

**"No schedule available" error**
- Verify origin and destination station codes are uppercase
- Confirm both stations exist in the BART system
- Check your device timezone is set to America/Los_Angeles

**Wrong times displayed**
- Ensure device time is set correctly
- Times are in 12-hour format with AM/PM
- Times are shown in Pacific Time

**No data appearing on device**
- Wait 15 minutes for first poll (or configure polling interval)
- Check TRMNL dashboard for configuration errors
- Verify form fields were copied correctly

## API Details

**Endpoint:** BART Depart API
```
https://api.bart.gov/api/sched.aspx?cmd=depart&orig={{ bart_origin_station }}&dest={{ bart_destination_station }}&date=now&key=MW9S-E7SL-26DU-VV8V&b=0&a=4&json=y
```

**Parameters:**
- `orig` - Origin station code (e.g., FTVL)
- `dest` - Destination station code (e.g., 19TH)
- `date=now` - Current schedule
- `b=0&a=3` - Show 3 upcoming departures
- `json=y` - JSON format response
- `key` - BART public API key (included)

**Response includes:**
- Next 4 trip options with departure/arrival times
- Train line information (destination station)
- Platform and direction info

## Polling URL Configuration

The **polling URL** is how your TRMNL device knows where to fetch BART data. It's defined in `settings.yml` and TRMNL uses it to periodically request fresh train schedules.

### How Polling Works

```
TRMNL Device (every 15 minutes by default)
    ↓
Fetches: https://api.bart.gov/api/sched.aspx?cmd=depart&orig=FTVL&dest=19TH&...
    ↓
Receives JSON response
    ↓
Liquid template renders on display
```

**Polling Interval:** TRMNL polls every 15 minutes by default. You can customize this in your TRMNL device settings (configurable from 1-60 minutes).

### The Polling URL Structure

The URL in `settings.yml` uses **template variables** that TRMNL fills in with your configuration:

```
https://api.bart.gov/api/sched.aspx?cmd=depart&orig={{ bart_origin_station }}&dest={{ bart_destination_station }}&date=now&key=MW9S-E7SL-26DU-VV8V&b=0&a=3&format=json
```

**After you configure the plugin, TRMNL converts it to:**

```
https://api.bart.gov/api/sched.aspx?cmd=depart&orig=FTVL&dest=19TH&date=now&key=MW9S-E7SL-26DU-VV8V&b=0&a=3&format=json
```

### Parameter Breakdown

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `cmd` | `depart` | Request departure schedule (not real-time ETD) |
| `orig` | Station code (e.g., FTVL) | Your boarding station (from BART Origin Station form field) |
| `dest` | Station code (e.g., 19TH) | Your destination station (from BART Destination Station form field) |
| `date` | `now` | Get today's schedule |
| `key` | `MW9S-E7SL-26DU-VV8V` | BART public demo API key (free, no signup required) |
| `b` | `0` | Show 0 previous departures |
| `a` | `3` | Show 3 upcoming departures |
| `format` | `json` | Return JSON format |

### Customizing Polling Behavior

#### Show More or Fewer Trips

Edit the `a` parameter in `settings.yml`:
- `b=0&a=3` → `b=0&a=5` to show 5 upcoming trips
- `b=0&a=3` → `b=0&a=2` to show 2 upcoming trips

Then update `bart-tile-full.liquid` to add or remove Card sections accordingly.

#### Custom API Key (For High Volume)

The public demo key is suitable for single-user setups polling every 15 minutes. If you deploy this to many devices, consider requesting a custom BART API key:

1. Visit: https://www.bart.gov/about/faqs/article/891
2. Request a production API key
3. Replace `MW9S-E7SL-26DU-VV8V` with your key in `settings.yml`

#### Adjust Polling Interval

The polling URL itself doesn't control interval—that's a TRMNL device setting:
1. Log in to https://dashboard.trmnl.com
2. Go to your device settings
3. Set polling interval (1-60 minutes, default 15 minutes)
4. Changes take effect immediately

### What Happens During a Poll

1. **Device requests** the polling URL with your configured stations
2. **BART API responds** with next 3 trains from origin to destination
3. **Liquid template** parses the response and formats the display
4. **E-ink display updates** with fresh times
5. **Device waits** until next polling interval (default 15 minutes)

If the polling URL fails (network error, API down, invalid station), the device displays the last successful response or an error message.

## Multi-Leg Routes & Transfers

The template automatically detects and displays routes with transfers (multi-leg journeys). When a trip has multiple legs:

- **Single-Leg Routes** (e.g., FTVL → 19TH direct): Shows one badge with the destination (e.g., "Richmond")
- **Multi-Leg Routes** (e.g., PLZA → DALY with transfer): Shows up to 2 badges displaying both destinations (e.g., "Richmond → Daly City")

The template intelligently abbreviates station names when possible:

| Full Station Name | Abbreviation |
|---|---|
| Pittsburg/Bay Point | PITT |
| OAK Airport / Berryessa/North San Jose | BERY |
| San Francisco Int'l Airport/Millbrae | SFOM |
| Millbrae (Caltrain Transfer Platform) | MLBR |
| Richmond | RICH |
| SF / Daly City | DALY |
| Warm Springs/South Fremont | WARM |
| Antioch | ANTC |
| Dublin/Pleasanton | DUBL |
| SFO International Airport | SFIA |

If a station name isn't in the abbreviation map, the full name is displayed.

## Customization

### Show More/Fewer Trips

Edit `settings.yml` polling URL parameter:
- `b=0&a=3` → `b=0&a=5` for 5 trips
- `b=0&a=3` → `b=0&a=2` for 2 trips

Then edit `bart-tile-full.liquid` to adjust the `limit:3` in the trip loop (line 13).

### Add More Station Abbreviations

Edit `bart-tile-full.liquid` to add more full-name→abbreviation mappings. Find the `line_map` at the top of the template (line 1-2) and add more entries:

```liquid
{% assign line_map =
  'Pittsburg/Bay Point:PITT,Richmond:RICH,Your Station:ABBR' | split: ',' %}
```

Each entry follows the format: `FullStationName:ABBREVIATION`

### Change Colors

In `bart-tile-full.liquid`, modify inline styles:
- `background-color: #000` - Card background (black)
- `color: white` - Text color
- `bg--gray-40` - Gray badges (TRMNL class)

### Add More Information

The BART API response includes:
- `originPlatform` - Platform number
- `destinationPlatform` - Arrival platform
- `line` - Route number

You can display these in the template by adding Liquid variables.

## Dependencies

- TRMNL account & device
- BART's public API (no key required, key is included)
- Internet connection on TRMNL device

## FAQ

**Q: Is this official?**
A: No, this is a community plugin. It uses BART's public API.

**Q: Do I need a Cloudflare Worker?**
A: No! This plugin calls BART's API directly from your device. Zero infrastructure needed.

**Q: What if BART changes their API?**
A: You'd need to update the polling URL in `settings.yml`. The template should still work.

**Q: Can I display multiple routes?**
A: Currently, the plugin shows one route (origin → destination). You can create separate TRMNL devices for different routes.

**Q: Why only 3 trips?**
A: E-ink displays are small. 3 trips fit nicely in a grid. You can customize this (see above).

## License

Public use. Same terms as BART's public API.

## Support

This is a community project. For TRMNL-specific issues, check the TRMNL docs. For BART API questions, see https://www.bart.gov

---

**Version:** 1.0
**Last Updated:** November 2024
**Repository:** https://github.com/jetsharklambo/bart-tiles
