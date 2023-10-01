# geojson2osmnotes
The purpose of this script is to import geojson features as osm notes.

The purpose is to help curate OSM based on messy data from some source.

In the example below we have unreliable data for bathing sites from a Swedish goverment agency.
They don't have names for the site, nor a stable identifier. 

Still the points are useful for OSM editors to know about and check out on the ground.

# Features
Compares a dataset with OSM and creates notes 
if the source has more information than OSM

# Limitations
* None known

# Installation
Clone the repository.

Run

`$ pip install poetry && poetry install`

Copy config.
`$cp sample_config.py config.py`

Edit the config and add your credentials.

# Use
## CLI
`python main.py --source-geojson source.geojson --osm-geojson osm.geojson --notes-file notes.csv`

# Examples
## Bathing sites
First we get the geojson from the source.
In this case we got the data  
from the Swedish Lantmäteriet and extracted the geojson
via https://github.com/dpriskorn/LantmaterietHikingPaths/blob/main/extract_bathing_site_pois.py

The OSM data was fetched via overpass-api using this query:
```
/*
This has been generated by the overpass-turbo wizard.
The original search was:
“amenity=public_bath or leisure=bathing_place in Sweden”
*/
[out:json][timeout:25];
// fetch area “Sweden” to search in
{{geocodeArea:Sweden}}->.searchArea;
// gather results
(
  // query part for: “amenity=public_bath”
  node["amenity"="public_bath"](area.searchArea);
  way["amenity"="public_bath"](area.searchArea);
  relation["amenity"="public_bath"](area.searchArea);
  // query part for: “leisure=bathing_place”
  node["leisure"="bathing_place"](area.searchArea);
  way["leisure"="bathing_place"](area.searchArea);
  relation["leisure"="bathing_place"](area.searchArea);
);
// print results
out body;
>;
out skel qt;
```

Both files were fed to the script like this

`$ python main.py --source-geojson badplatser_sverige.geojson --osm-geojson export.geojson --notes-file notes.csv`

# License
GPLv3+

# Thanks
To the Magnus Sälgö and the Swedish OSM community for feedback.

# What I learned
* Chatgpt is not good at handling shapely.geometry.point.Point 
without explicitly telling it to.
* Geopandas and geopy are super nice and fast.