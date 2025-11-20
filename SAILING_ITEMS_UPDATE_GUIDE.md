# Quick Guide: Update Item Cache for Sailing Items

## What You Need
- Node.js (v14+) and npm
- Java 17+ 
- Maven
- Git
- ~30 minutes of time (most is automated)
- Good internet connection (will download ~500MB game cache)

## Step-by-Step Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/perterter/group-ironmen.git
cd group-ironmen
```

### 2. Create a New Branch
```bash
git checkout -b update-sailing-items
```

### 3. Install Cache Update Dependencies
```bash
cd cache
npm install
```

### 4. Run the Cache Update Script
```bash
npm run update
```

**This will take 10-30 minutes.** The script will:
- Download the latest OSRS game cache from OpenRS2 (~500MB)
- Clone RuneLite repository
- Extract all item data (including new sailing items)
- Generate item images
- Update multiple data files

You'll see output like:
```
Step: Setting up runelite
Step: Unpacking item data from cache
Step: Fetching unalchable items from wiki
Step: Build item_data.json
Step: Extract item model images
...
```

### 5. Verify the Changes
```bash
cd ..
git status
```

You should see changes to:
- `site/public/data/item_data.json`
- `site/public/icons/items/*.webp` (many new image files)
- `site/public/data/map_icons.json`
- `site/public/data/map_labels.json`
- `site/public/map/*.webp` (map tiles)
- `site/public/map/labels/*.webp`
- `server/collection_log_info.json`

### 6. Check for Sailing Items
```bash
# Quick check to see if sailing items are included
python3 << 'EOF'
import json
with open('site/public/data/item_data.json', 'r') as f:
    items = json.load(f)
    
# Get highest item ID
max_id = max(int(id) for id in items.keys())
print(f"Highest item ID: {max_id}")
print(f"Total items: {len(items)}")

# Look for items added after the old max (31169)
new_items = {id: data for id, data in items.items() if int(id) > 31169}
if new_items:
    print(f"\n{len(new_items)} new items found!")
    print("\nFirst 10 new items:")
    for item_id in sorted(new_items.keys(), key=int)[:10]:
        print(f"  {item_id}: {new_items[item_id]['name']}")
else:
    print("\nNo new items found (may already be up to date)")
EOF
```

### 7. Commit the Changes
```bash
git add .
git commit -m "Update item cache to include sailing items and latest game content"
```

### 8. Push and Create PR
```bash
# If you have a fork:
git push origin update-sailing-items

# If you're pushing to the main repo (with permissions):
git push origin update-sailing-items
```

Then go to GitHub and create a Pull Request from your branch.

## What If Something Goes Wrong?

### "Cannot resolve host: archive.openrs2.org"
- Check your internet connection
- Try again (sometimes the server is busy)
- Check if your firewall/antivirus is blocking the connection

### "JAVA_HOME not set" or Java errors
```bash
# Check Java version (need 17+)
java -version

# On Linux/Mac, you may need to set JAVA_HOME
export JAVA_HOME=/path/to/java17
```

### Script hangs or fails partway through
- Delete the `cache/cache` directory and try again
- Delete the `cache/runelite` directory and try again
- Make sure you have enough disk space (~2GB free)

### Too many file changes / want to exclude some files
You can exclude map tiles if they're too large:
```bash
# Add to .gitignore before committing
echo "site/public/map/*.webp" >> .gitignore
```

But **DO include**:
- `site/public/data/item_data.json` (essential!)
- `site/public/icons/items/*.webp` (item images)

## Expected Result

After your PR is merged, sailing items will display correctly in the web client instead of showing console warnings about unrecognized item IDs.

## Questions?

If you run into issues, check:
1. Console output for specific error messages
2. Make sure all prerequisites are installed
3. Try the troubleshooting steps above

The main file that fixes the sailing items issue is `site/public/data/item_data.json` - this is the critical one that must be updated!
