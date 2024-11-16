Standard Map Templates: Site Locator, Population, Hydrosheds,
Topographic
================
Murphy, S.
2024-09-16

- [Site Locator Map (1:70,000)](#site-locator-map-170000)
- [Country Locator Map](#country-locator-map)

## Site Locator Map (1:70,000)

##### Derive `aoi` & `bbox` from site boundary & national borders,

``` r
aoi_site   = sf::read_sf("./inputs/chilwa_watershed_4326.shp")
aoi_malawi = giscoR::gisco_get_countries(country = "Malawi", resolution = "3")
aoi_region = giscoR::gisco_get_countries(
  country = c("Malawi", "Zambia", "Tanzania", "Mozambique"), resolution = "3"
  )

bbox_site  = terrainr::add_bbox_buffer(aoi_site, 20000, "meters")
bbox_malawi  = terrainr::add_bbox_buffer(aoi_malawi, 400000, "meters")
bbox_region = terra::vect(terra::ext(vect(aoi_region)) * 1.5) 
crs(bbox_region) = "epsg:4326"
```

##### Download basemap tiles for higher res mapping,

``` r
# 'zoom' = scale & resolution (https://wiki.openstreetmap.org/wiki/Zoom_levels)
basemap_150k = maptiles::get_tiles(
  bbox_site, 
  zoom      = 12, 
  crop      = T,
  provider  = "OpenTopoMap"
)

tmap::tm_shape(basemap_150k) + tm_rgb() + 
  tmap::tm_shape(aoi_site) +
  tmap::tm_borders(lwd = 1, col = "red") +
  tmap::tm_graticules(lines=T,labels.rot=c(0,90),lwd=0.2) +
  tmap::tm_credits("EPSG:4326", position = c("left", "bottom")) + 
  tmap::tm_scalebar(c(0, 10, 20, 40), position = c("right", "bottom"), text.size = .5) +
  tmap::tm_compass(
    type = "4star", size = 1.5,
    color.dark = "gray60", text.color = "gray60",
    position = c("left", "top")
    ) -> fieldmap
fieldmap

# width & height = res, dpi = size of add-ons
tmap::tmap_save(
  fieldmap, "./outputs/map_locator_site.png", 
  width=15120, height=15120, asp=0, dpi=2400
  )
```

![](outputs/map_locator_site.png)

## Country Locator Map
