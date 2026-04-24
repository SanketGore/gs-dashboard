# GS Agencies — Web Dashboard

A mobile-friendly, browser-based business dashboard for GS Agencies.  
Hosted for free on GitHub Pages — no server needed.

## How It Works

```
Desktop App                          GitHub Pages
┌─────────────┐    JSON file    ┌──────────────────┐
│ Inventory DB │ ──────────────►│ index.html        │
│ (SQLite)     │  Export button │ (Chart.js + JS)   │
└─────────────┘                 │                   │
                                │ ← loads JSON      │
                                │ ← renders charts  │
                                │ ← works on mobile │
                                └──────────────────┘
```

1. **Desktop app** exports a `dashboard_data.json` snapshot  
2. **User uploads** the JSON file to the web dashboard  
3. **Dashboard renders** entirely in the browser — no backend  
4. **Data is cached** in localStorage so you don't re-upload every time

---

## Setup (One-Time)

### 1. Create a GitHub repository

1. Go to https://github.com/new
2. Name it `gs-dashboard` (or anything you like)
3. Set it to **Public**
4. Click **Create repository**

### 2. Upload the dashboard file

1. Click **Add file → Upload files**
2. Upload `index.html` from the `web/` folder
3. Commit with message: "Add web dashboard"

### 3. Enable GitHub Pages

1. Go to **Settings → Pages**
2. Under **Source**, select **Deploy from a branch**
3. Branch: `main`, Folder: `/ (root)`
4. Click **Save**
5. Wait 1-2 minutes — your dashboard will be live at:
   ```
   https://YOUR_USERNAME.github.io/gs-dashboard/
   ```

---

## Daily Usage

### Export data from desktop app

Add this button to your Export page, or run from the command line:

```python
from dashboard_export import export_dashboard_json

# Export to desktop
export_dashboard_json("C:/Users/YourName/Desktop/dashboard_data.json")
```

Or integrate into the Export page by adding a button that calls:
```python
filepath = export_dashboard_json(
    os.path.join(os.path.expanduser("~"), "Desktop", "dashboard_data.json")
)
```

### View on mobile

1. Open `https://YOUR_USERNAME.github.io/gs-dashboard/` on your phone
2. First time: tap the upload area and select the JSON file
3. Next time: the app shows "Previously loaded data available" — tap to use cached data
4. To update: export fresh JSON from the desktop app, upload again

---

## Quick Integration — Add Export Button to Desktop App

In `pages/export_page.py`, add this alongside the existing DB backup button:

```python
from dashboard_export import export_dashboard_json

# In the button row layout:
self.web_btn = QPushButton("🌐  Export for Web Dashboard")
self.web_btn.setStyleSheet(
    "background-color: #8e44ad; color: white; font-size: 14px; padding: 10px 28px;"
)
self.web_btn.clicked.connect(self._export_web)
btn_row.addWidget(self.web_btn)

# Method:
def _export_web(self):
    from dashboard_export import export_dashboard_json
    filepath, _ = QFileDialog.getSaveFileName(
        self, "Save Dashboard Data",
        os.path.join(os.path.expanduser("~"), "Desktop", "dashboard_data.json"),
        "JSON Files (*.json)"
    )
    if not filepath:
        return
    try:
        export_dashboard_json(filepath)
        QMessageBox.information(
            self, "Exported",
            f"Dashboard data exported to:\\n{filepath}\\n\\n"
            "Upload this file to your web dashboard."
        )
    except Exception as e:
        QMessageBox.critical(self, "Export Failed", str(e))
```

---

## Features

- **5 KPI cards**: Revenue, Profit, Outstanding, Stock Valuation, Purchases
- **Revenue & COGS trend**: Daily (line) / Monthly / Yearly (grouped bars)
- **Revenue by Product**: Horizontal bar chart, top 15
- **Outstanding Dues**: Horizontal bar chart by customer
- **Low Stock Alerts**: Color-coded table
- **Filters**: Date range, Period, Metric (Revenue/Profit), Location, Customer, Product
- **Mobile responsive**: Works on any screen size
- **Offline capable**: Data cached in browser localStorage
- **No server needed**: 100% client-side JavaScript

---

## File Structure

```
gs-dashboard/
├── index.html              ← Upload this to GitHub
├── dashboard_export.py     ← Add this to your desktop app folder
└── README.md               ← This file
```
