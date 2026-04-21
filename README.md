# 2.5D-Costmap
A repo explaining 2.5d mapping using oak-d camera 
Digital maps are the foundation of all other location-based services such as navigation, positioning and geofencing. 2.5D maps add heights to flat 2D maps with occupied space represented as a series of columns of different heights that show what space is filled.

With rich and in-depth 2.5D maps, the accuracy of location-based services such as navigation for people and automated devices is increased, allowing for a smarter, better managed facility.
🧠 What IS a 2.5D Map?

A 2.5D map is an intermediate between 2D and 3D:

📌 2D map = flat ground (x, y)
📌 3D map = full volumetric data (x, y, z)

👉 2.5D map = grid map (x, y), but each cell stores height information or surface characteristics.

So instead of just saying “occupied / free”, a cell can store:

Elevation

Slope

Roughness

Traversability score

This is perfect for wheeled or legged robots.

Depth Camera → PointCloud2
      ↓
Extract X, Y, Z points
      ↓
Build 2D height grid (histogram)
  Each cell = average height of points in that area
      ↓
For each 3x3 patch of cells:
  - Fit a plane using PCA (eigenvalues)
  - Calculate: slope angle, roughness, height difference
      ↓
Assign cost:
  slope>25° AND height_diff>8cm → 100 (obstacle)
  height_diff > 15cm            → 100 (obstacle)  
  height_diff > 10cm            → 80  (warning)
  height_diff > 5cm             → 60  (caution)
  height_diff > 3cm             → 30  (slight)
  flat                          → 0   (free)
      ↓
Apply inflation around obstacles
      ↓
Publish as OccupancyGrid → /local_costmap
