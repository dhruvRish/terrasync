# TerraSync 🌊
### Al-Batinah Smart Salinity Monitoring System

## 📁 Repository Structure

```
terrasync/
├── index.html                  ← Main website (deploy to GitHub Pages)
├── arduino/
│   └── terrasync_sensor.ino    ← Arduino Uno sketch (Sohar Farm A)
├── python/
│   └── terrasync_bridge.py     ← Serial → Google Sheets bridge
├── credentials.json            ← YOUR Google API key (DO NOT COMMIT)
└── README.md
```

---

## 🚀 Quick Start

### Step 1 — Deploy Website to GitHub Pages
1. Fork or upload this repo to GitHub
2. Go to **Settings → Pages → Deploy from main branch**
3. Your live URL: `https://yourusername.github.io/terrasync`

### Step 2 — Setup Google Sheets
1. Go to [sheets.google.com](https://sheets.google.com)
2. Create new sheet called `TerraSync-Sohar`
3. Share → **Anyone with link → Viewer**
4. Copy the Sheet ID from the URL:
   `https://docs.google.com/spreadsheets/d/`**[SHEET_ID]**`/edit`

### Step 3 — Google API Credentials
1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create project: `TerraSync`
3. Enable: **Google Sheets API** + **Google Drive API**
4. Create → **Service Account** → Download JSON key
5. Rename to `credentials.json` — place in this folder
6. Copy the `client_email` from credentials.json
7. Share your Google Sheet with that email (Editor access)

> ⚠️ **IMPORTANT:** Never commit `credentials.json` to GitHub!
> It is already in `.gitignore`

### Step 4 — Wire the Arduino Sensor

```
Soil Moisture Sensor    Arduino Uno
────────────────────    ───────────
VCC          ───────→   Pin 7  (software power)
GND          ───────→   GND
AO (Analog)  ───────→   A0
```

### Step 5 — Calibrate the Sensor
1. Open `arduino/terrasync_sensor.ino`
2. Uncomment `#define CALIBRATION_MODE` (line ~26)
3. Upload → Open Serial Monitor at 9600 baud
4. Hold sensor in **dry air** → note RAW ADC → set `DRY_VAL`
5. Hold sensor in **water** → note RAW ADC → set `WET_VAL`
6. Comment out `#define CALIBRATION_MODE` again → re-upload

### Step 6 — Upload Arduino Sketch
1. Open Arduino IDE
2. Open `arduino/terrasync_sensor.ino`
3. **Tools → Board → Arduino Uno**
4. **Tools → Port → your COM port**
5. Click **Upload (→)**

### Step 7 — Install Python Dependencies
```bash
pip install pyserial gspread google-auth
```

### Step 8 — Update Config Values
In **`python/terrasync_bridge.py`** (lines 26-28):
```python
SERIAL_PORT = "COM3"                    # your Arduino port
SHEET_ID    = "YOUR_SHEET_ID_HERE"      # from Step 2
```

In **`index.html`** (line ~220):
```javascript
const SHEET_ID = "YOUR_GOOGLE_SHEET_ID_HERE";
```

### Step 9 — Run the Bridge
```bash
cd python
python terrasync_bridge.py
```

You should see:
```
====================================================
  TerraSync Bridge — Sohar Farm A
====================================================
  ✓ Arduino connected
  ✓ Google Sheets connected
  ✓ Arduino ready

  ✅  Sohar Farm A  ·  Reading #1
  Moisture : [████████░░░░░░░░░░░░] 42.3%
  EC est.  : 5.08 dS/m
  Status   : WARNING
```

---

## 🔬 The Science

### GEE Satellite Equations
| Index | Formula | Detects |
|-------|---------|---------|
| NDSI | (B4 − B8) / (B4 + B8) | Salt in soil |
| NDVI | (B8 − B4) / (B8 + B4) | Vegetation health |
| SI | √(B3 × B4) | Salt crust brightness |

### Arduino Sensor Equations
| Equation | Formula |
|----------|---------|
| Moisture % | (DRY − raw) / (DRY − WET) × 100 |
| EC (dS/m) | Moisture% × 0.12 |

### Alert Thresholds
| EC Level | Status |
|----------|--------|
| < 2 dS/m | ✅ Safe |
| 2–6 dS/m | ⚡ Warning |
| > 6 dS/m | 🚨 Critical |

---

## 🗺️ Data Flow

```
[Arduino Uno]
     ↓ USB Serial (9600 baud)
[terrasync_bridge.py]
     ↓ HTTPS API
[Google Sheets]  ←──────── make PUBLIC (viewer)
     ↓ JSON endpoint
[index.html]  fetches every 5 seconds
     ↓
[Live Dashboard]  Sohar Farm A = real data
                  Other 5 nodes = simulated
```

---

## ⚡ Total Cost

| Item | Cost |
|------|------|
| Arduino Uno | 
| Soil Moisture Sensor | 
| Google Sheets | 
| GitHub Pages | 
| Python libraries |


---

## 📊 10-Year Satellite Evidence (2015–2024)
- NDSI increased **+41%** (salinity worsening)
- NDVI declined **−28%** (vegetation dying)
- **312 km²** classified as high-risk in 2024
- Critical threshold first breached in **2019**

---

## 🛡️ .gitignore
```
credentials.json
__pycache__/
*.pyc
.DS_Store
```

---


