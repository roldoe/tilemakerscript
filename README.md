# Openstreetmap Vector Tile Server

## Voorbereiden data

### Download config en process-openmaptiles.lua
`wget "https://raw.githubusercontent.com/roldoe/tilemakerscript/refs/heads/master/process-openmaptiles.lua"`
`wget "https://raw.githubusercontent.com/roldoe/tilemakerscript/refs/heads/master/tilemaker_config-openmaptiles.json"`

### Bewaar alle geodata op een plek tbv containers
`mkdir data`

### Download OpenStreetMaps file
`# wget -O data/europe-latest.osm.pbf "https://download.geofabrik.de/europe-latest.osm.pbf"`
`wget -O data/nl-latest.osm.pbf "https://download.geofabrik.de/europe/netherlands-latest.osm.pbf"`

### Download coastlines and landcover
```
wget -O data/water-polygons-split-4326.zip "https://osmdata.openstreetmap.de/download/water-polygons-split-4326.zip"
unzip -o -j -d data/coastline/ data/water-polygons-splits-4326.zip
wget -O data/ne_10m_antarctic_ice_shelves_polys.zip "https://naciscdn.org/naturalearth/10m/physical/ne_10m_antarctic_ice_shelves_polys.zip"
unzip -o -j -d data/landcover/ data/ne_10m_antarctic_ice_shelves_polys.zip
wget -O data/ne_10m_urban_areas.zip "https://naciscdn.org/naturalearth/10m/cultural/ne_10m_urban_areas.zip"
unzip -o -j -d data/landcover/ data/ne_10m_urban_areas.zip
wget -O data/ne_10m_glaciated_areas.zip "https://naciscdn.org/naturalearth/10m/physical/ne_10m_glaciated_areas.zip"
unzip -o -j -d data/landcover/ data/ne_10m_glaciated_areas.zip
```

### Build the vector tiles met alle data
`docker run -it -v $(pwd)/process-openmaptiles.lua:/config/process.lua \
               -v $(pwd)/tilemaker_config-openmaptiles.json:/config/config.json \
               -v $(pwd)/data:/data ghcr.io/systemed/tilemaker:master \
            --config /config/config.json --process /config/process.lua /data/nl-latest.osm.pbf --output /data/nl.pmtiles`


## TILESERVER-GL ######

### Download tileserver-config.json
`wget "https://raw.githubusercontent.com/roldoe/tilemakerscript/refs/heads/master/tileserver_config.json"`

### install the style and fonts 
# (source: from the repo https://github.com/openmaptiles/openmaptiles.git; make download-fonts; make build-style)
# simpeler:
`wget -O data/styles_fonts.zip XXXX` 
`unzip data/styles_fonts.zip`

`docker run -it -v $(pwd)/tileserver_config.json:/tileserver_config.json -v $(pwd)/data:/data -p 8080:8080 maptiler/tileserver-gl -c /tileserver_config.json`

