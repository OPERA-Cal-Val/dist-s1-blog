+++
author = "Charlie Marshak, Talib Oliver-Cabrera, Richard West"
title = "The Los Angeles Fires 2025 - preliminary take"
date = "2025-10-19"
description = "Wildfire of 2025"
+++

This is our first (rough) post and shows what we can do relatively quickly with a blog and provides some loose instructions on how to create it.

We are going to show the perimeters extracted from this: https://data-nifc.opendata.arcgis.com/datasets/nifc::wfigs-current-interagency-fire-perimeters/about. To get all the relevant perimeters (Eaton, Pallisades, Kenneth, Sunset), we took +/- 20 days from January 4th, 2025 from the perimeters linked.


Some notes about the product:

- We are going to look at the DIST-S1 product over Los Angeles after the wildfires: `OPERA_L3_DIST-ALERT-S1_T11SLT_20250303T140056Z_20251016T224900Z_S1A_30_v0.1`. This is generated with version `dist-s1` v`2.0.6`.
- the product we display was from a Sentinel-1 pass `20250303T140056Z`. Interestingly, there are lot of provisional changes within Los Angeles. Looking at the time-series of products (Status layers not shown) - there are two passes of data from [Sentinel-1](https://search.asf.alaska.edu/#/?zoom=9.389&center=-117.799,33.669&dataset=OPERA-S1&productTypes=RTC&polygon=POLYGON((-118.7185%2033.8735,-117.8756%2033.8735,-117.8756%2034.3332,-118.7185%2034.3332,-118.7185%2033.8735))&resultsLoaded=true&granule=OPERA_L2_RTC-S1_T064-135520-IW2_20250214T015043Z_20250215T161954Z_S1A_30_v1.0&start=2025-02-13T08:00:00Z&end=2025-02-16T07:59:59Z). During that time, there was an atmospheric river: https://cw3e.ucsd.edu/cw3e-event-summary-12-14-february-2025/ - that means we have two observations of this event in 1 day!
- We still need to provide some information on the color-coding (it's not straightforward to do this in leaflet) - for now, we just are going to note it matches DIST-HLS's colorbar (see [here](https://glad.earthengine.app/view/dist-alert#lon=-118.41608925455033;lat=34.13252521035229;zoom=11;))
- Previously, we had better results over Eaton, we are looking into this.
- All these products are unvalidated!


{{< rawhtml >}}
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://unpkg.com/pmtiles@3.0.7/dist/pmtiles.js"></script>

<div id="fire-map" style="height: 600px; width: 100%; margin: 20px 0;"></div>

<script>
(async () => {
  // Initialize map centered on Los Angeles
  const map = L.map('fire-map').setView([34.05, -118.25], 10);

  // Add base ESRI imagery tiles
  const esriImagery = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
    maxZoom: 19
  }).addTo(map);

  // Add OpenStreetMap as alternative basemap
  const osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors',
    maxZoom: 19
  });

  // Load PMTiles - construct path based on current page URL
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/') ? '/dist-s1-blog' : '';
  const pmtilesUrl = `${pathPrefix}/map_data/los_angeles/los_angeles_2025.pmtiles`;
  const p = new pmtiles.PMTiles(pmtilesUrl);

  // Debug: Check if PMTiles loads
  p.getHeader().then(h => {
    console.log('PMTiles header:', h);
  }).catch(e => {
    console.error('Error loading PMTiles:', e);
  });

  // Custom GridLayer for PMTiles raster
  const PMTilesLayer = L.GridLayer.extend({
    createTile: function(coords, done) {
      const tile = document.createElement('img');

      // Fetch tile from PMTiles
      p.getZxy(coords.z, coords.x, coords.y).then(data => {
        if (data) {
          const blob = new Blob([data.data], { type: 'image/png' });
          const url = URL.createObjectURL(blob);
          tile.src = url;

          // Clean up blob URL after image loads
          tile.onload = () => {
            URL.revokeObjectURL(url);
            done(null, tile);
          };
          tile.onerror = () => {
            done(new Error('Tile load error'), tile);
          };
        } else {
          done(new Error('No tile data'), tile);
        }
      }).catch(err => {
        console.error('Error fetching tile:', err);
        done(err, tile);
      });

      return tile;
    }
  });

  // Add PMTiles layer to map
  const pmtilesLayer = new PMTilesLayer({
    opacity: 0.8,
    attribution: 'DIST-S1',
    maxZoom: 16,
    minZoom: 0
  });

  pmtilesLayer.addTo(map);

  // Load and add GeoJSON layer
  const geojsonResponse = await fetch(`${pathPrefix}/map_data/los_angeles/los_angeles_fires.geojson`);
  const geojsonData = await geojsonResponse.json();

  const geojsonLayer = L.geoJSON(geojsonData, {
    style: {
      color: '#ff0000',
      weight: 2,
      opacity: 0.8,
      fillOpacity: 0.3
    },
    onEachFeature: function(feature, layer) {
      if (feature.properties) {
        let popupContent = '<div>';
        for (const [key, value] of Object.entries(feature.properties)) {
          popupContent += `<strong>${key}:</strong> ${value}<br>`;
        }
        popupContent += '</div>';
        layer.bindPopup(popupContent);
      }
    }
  }).addTo(map);

  // Add layer control with clickable basemaps
  const baseLayers = {
    "ESRI Imagery": esriImagery,
    "OpenStreetMap": osmLayer
  };

  const overlays = {
    "DIST-S1 Status": pmtilesLayer,
    "Fire Perimeters": geojsonLayer
  };

  L.control.layers(baseLayers, overlays).addTo(map);
})();
</script>
{{< /rawhtml >}}
