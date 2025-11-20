# BARTcom - Real-Time BART commute details

A minimal, zero-infrastructure TRMNL plugin that displays real-time BART (Bay Area Rapid Transit) departure and arrival times directly on your e-ink device.

You enter an origin station and a destintation station and you'll get the next 2-3 departure times. Multiple views for personalized dashboards with various commutes.

![BARTcom Fullscreen](https://github.com/jetsharklambo/BARTcom-TRMNL/blob/main/BARTcom.png) 

Multiple views for unlimited mashup potential.
![BARTcom Fullscreen](https://github.com/jetsharklambo/BARTcom-TRMNL/blob/main/mashup.png) 

## Features

✨ **Real-time BART data** - Shows next 3 upcoming departures and arrival times
🚂 **Origin & Destination tracking** - See when you leave and when you arrive
🎨 **E-ink optimized** - Black & white design for perfect readability
⚡  **Zero infrastructure** - No servers, no backends, no maintenance
📱 **Direct API calls** - TRMNL device communicates directly with BART's public API
🔧 **Easy setup** - Just copy & paste configuration into TRMNL
📐 **Multiple layouts** - Choose from grid, horizontal, or vertical layouts

## Quick Start

### Installation

#### Auto

1. **Visit** https://usetrmnl.com/recipes/181434
2. Click **install**
3. **Configure & Save** - Select your BART origin and destination stations
4. **Set polling interval** in your TRMNL device settings (default: 15 minutes)

#### Manual
1. **Log into TRMNL** at https://dashboard.trmnl.com
2. **Create a new Private Plugin**
3. **Paste Form Fields**
   - Go to "Form Fields" section
   - Copy and paste the content from `settings.yml` (see below for station list)
4. **Set Polling URL**
   - Go to "Polling URL" section
   - Copy and paste this URL:
   ```
   https://api.bart.gov/api/sched.aspx?cmd=depart&orig={{ bart_origin_station }}&dest={{ bart_destination_station }}&date=now&key=MW9S-E7SL-26DU-VV8V&b=0&a=4&json=y
   ```
   - TRMNL will automatically replace `{{ bart_origin_station }}` and `{{ bart_destination_station }}` with your configuration values
   - You can replace the public BART API key with your own api key
5. **Select Template Layout**
   - Choose based on your device orientation:
     - **Full Screen** → Use `full.liquid` (3 trips in horizontal grid)
     - **Horizontal** → Use `half-horizontal.liquid` (3 trips in row layout)
     - **Small** → Use `quadrant.liquid` (2 trips in 2-column grid)
     - **Tall** → Use `half-vertical.liquid` (3 trips in vertical stack)
6. **Paste Liquid Template**
   - Copy and paste your chosen template file
7. **Save & Configure** - Select your BART origin and destination stations
8. **Set polling interval** in your TRMNL device settings (default: 15 minutes)

### Configuration

#### Step 1: Add Form Fields

In TRMNL's "Form Fields" section, paste the content from **`settings.yml`**:

This file contains:
- **BART Origin Station** - Where you're boarding (dropdown with 49 stations)
- **BART Destination Station** - Where you're going (dropdown with 49 stations)

All 49 BART stations are included: FTVL, 19TH, LAKE, DBRK, EMBR, POWL, SFIA, and more.

#### Step 2: Add Display Template

In TRMNL's layout section (Full Screen, Quadrant, Small, or Tall), paste your chosen template:

**Available layouts:**
- **`=full.liquid`** - 3-column grid layout (shows 3 trips)
- **`half-horizontal.liquid`** - 3-column row layout (shows 3 trips)
- **`quadrant.liquid`** - 2-column grid layout (shows 2 trips)
- **`half-vertical.liquid`** - Vertical stack layout (shows 3 trips)

Each template will:
- Display your origin → destination as the header
- Show up to 2-3 upcoming trips in card format
- Display departure time, arrival time, and train line abbreviations
- Handle errors gracefully with a warning message

#### Step 3: Configure Your Commute

Once saved, TRMNL will show configuration options:
- Select your **Origin Station** (e.g., Fruitvale)
- Select your **Destination Station** (e.g., 19th St. Oakland)
- Save configuration

TRMNL will now poll the BART API every 15 minutes (configurable in TRMNL device settings).

## Files

| File | Purpose |
|------|---------|
| `settings.yml` | TRMNL plugin form fields (origin/destination stations) |
| `full.liquid` | 3-column grid template (Full Screen layout) |
| `half-horizontal.liquid` | 3-column row template (Quadrant layout) |
| `quadrant.liquid` | 2-column grid template (Small layout) |
| `half-vertical.liquid` | Vertical stack template (Tall layout) |
| `README.md` | This file |



## License

Public use. Same terms as BART's public API.

## Support

This is a community project. For TRMNL-specific issues, check the TRMNL docs. For BART API questions, see https://www.bart.gov

---

**Version:** 1.0
**Last Updated:** November 2025
**Repository:** https://github.com/jetsharklambo/BARTcom-TRMNL
