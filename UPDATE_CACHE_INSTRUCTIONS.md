# Instructions to Update Item Data for Sailing Items

## Problem
The web client's `item_data.json` file (last updated September 2025) doesn't include new sailing items introduced in the November 2024 OSRS sailing update. When the RuneLite plugin transmits these item IDs to the server, they are stored correctly but the web client cannot display them because they're missing from the item data file.

## Solution
The `item_data.json` file needs to be regenerated from the latest OSRS game cache using the cache update script.

## How to Update (Requires Network Access)

### Prerequisites
- Node.js and npm
- Java 17+ and Maven
- Git
- Network access to archive.openrs2.org (OpenRS2 cache archive)

### Steps

1. **Navigate to the cache directory:**
   ```bash
   cd cache
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Run the update script:**
   ```bash
   npm run update
   ```

This process will:
- Download the latest OSRS cache from OpenRS2's archive
- Clone/update RuneLite repository
- Extract all item data from the cache including:
  - Item names and IDs
  - High alchemy values
  - Stack variants
  - Item model images
- Generate item images in WebP format
- Update `site/public/data/item_data.json` with all items
- Update `site/public/icons/items/` with item images

The process can take 10-30 minutes depending on your system and network speed.

### What Gets Updated
- `site/public/data/item_data.json` - Complete item database
- `site/public/icons/items/*.webp` - Item images
- `site/public/data/map.json`, `map_icons.json`, `map_labels.json` - Map data
- `server/collection_log_info.json` - Collection log data

## Network Restriction Workaround

If you cannot access archive.openrs2.org (e.g., in a restricted environment):

1. **Run the update on a machine with network access**
2. **Copy the generated files** to your deployment:
   - `site/public/data/item_data.json`
   - `site/public/icons/items/*.webp` (new items only if you want to minimize transfer)

## Verification

After updating, sailing items should appear correctly in the web client. You can verify by:

1. Checking the browser console for "Unrecognized item id" warnings
2. Viewing player inventories/banks with sailing items
3. Searching for sailing items in the item database

##Expected Sailing Items (November 2024 Update)

The sailing update introduced numerous new items including boats, boat equipment, navigation tools, sailing materials, and rewards. The exact item IDs start around 31170+ based on the game's item ID progression.

## Automation

Consider setting up a scheduled task (e.g., monthly) to run the cache update script to keep item data current with game updates.
