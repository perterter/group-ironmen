# What Needs to Be Updated for Sailing Items Support

## TL;DR - YES, You Need Both!

**You need BOTH:**
1. ✅ `site/public/data/item_data.json` - Item metadata (names, IDs, stats)
2. ✅ `site/public/icons/items/*.webp` - Item images

The cache update script (`npm run update`) generates **both** automatically.

## Why Both Are Needed

### 1. Item Data JSON (`item_data.json`)
**What it does:**
- Maps item IDs to item names
- Stores high-alch values
- Defines stack variants
- **Without this:** Items show console warning "Unrecognized item id: XXXXX" and don't display at all

**Example entry:**
```json
"31170": {
  "name": "Sailing book",
  "highalch": 60
}
```

### 2. Item Images (`icons/items/*.webp`)
**What it does:**
- Provides the visual icon for each item
- Referenced by item ID: `/icons/items/31170.webp`
- **Without this:** Items display with broken image icon or missing texture

**How it works:**
```javascript
// From site/src/data/item.js line 26:
return `/icons/items/${imageId}.webp`;
```

## Current State

**Existing data ends at ID:** 31169 (Hueycoatl hide armour set)
- ✅ 15,963 item images exist
- ✅ 14,857 items in JSON

**Sailing items start at ID:** ~31170+
- ❌ Not in JSON yet
- ❌ No images yet

## What the Cache Update Does

When you run `npm run update` in the `cache` directory, it:

1. **Downloads** latest OSRS cache from OpenRS2
2. **Extracts** ALL item definitions (including sailing)
3. **Generates** `item_data.json` with ALL items
4. **Renders** 3D models to create `.webp` images for ALL items
5. **Moves** everything to the correct directories

## Files You'll Need to Commit

### Essential (MUST include):
```
✅ site/public/data/item_data.json         (~5-10 MB JSON file)
✅ site/public/icons/items/*.webp           (NEW files for sailing items)
```

### Also Updated (consider including):
```
📦 site/public/data/map_icons.json         (map icon locations)
📦 site/public/data/map_labels.json        (map label locations)
📦 site/public/map/*.webp                   (map tiles - can be large)
📦 site/public/map/labels/*.webp            (map labels)
📦 server/collection_log_info.json         (collection log data)
```

### Can Exclude (if too large):
```
❌ site/public/map/*.webp  (thousands of map tiles, several hundred MB)
```

You can add this to `.gitignore` if needed:
```bash
echo "site/public/map/*.webp" >> .gitignore
```

But **keep** the map metadata JSON files.

## Size Expectations

- `item_data.json`: ~5-10 MB
- New item images: ~50-200 files × ~5-20 KB each = ~1-4 MB
- Map tiles (optional): ~100-300 MB

## Verification Steps

After running the update, verify both components:

### 1. Check JSON has new items:
```bash
python3 << 'EOF'
import json
with open('site/public/data/item_data.json', 'r') as f:
    items = json.load(f)
max_id = max(int(id) for id in items.keys())
print(f"Highest item ID: {max_id}")
if max_id > 31169:
    print("✅ New items found in JSON!")
else:
    print("❌ JSON not updated")
EOF
```

### 2. Check images exist:
```bash
# Check highest image ID
ls site/public/icons/items/*.webp | sed 's/.*\///;s/\.webp$//' | sort -n | tail -1

# If > 31169, you have new images!
```

### 3. Check a specific sailing item:
```bash
# Example: Check if item 31170 exists
test -f site/public/icons/items/31170.webp && echo "✅ Image exists" || echo "❌ Image missing"

# Check in JSON
grep '"31170"' site/public/data/item_data.json && echo "✅ In JSON" || echo "❌ Not in JSON"
```

## What Happens If You Only Update One?

### ❌ Only JSON, no images:
- Items appear in lists with names
- But show as broken/missing images
- **Result:** Functional but ugly

### ❌ Only images, no JSON:
- Console errors: "Unrecognized item id"
- Items completely hidden/skipped
- Images never used
- **Result:** Doesn't work at all

### ✅ Both JSON and images:
- Items display correctly with names and icons
- **Result:** Perfect!

## Quick Answer

**Run this one command and commit everything it generates:**

```bash
cd cache
npm run update
cd ..
git add site/public/data/item_data.json
git add site/public/icons/items/
git add server/collection_log_info.json
git add site/public/data/map_icons.json
git add site/public/data/map_labels.json
# Optionally add map tiles if you want full map updates:
# git add site/public/map/
git commit -m "Update game cache with sailing items and latest content"
```

That's it! The script handles everything - you just need to commit the results.
