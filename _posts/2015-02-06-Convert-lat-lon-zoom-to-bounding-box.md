---
layout: post
title: Convert lat lon zoom to bounding box
published: true
---

For [plotting geographical data](http://nbviewer.ipython.org/github/buntworthy/notebooks/blob/master/Cambridge%20house%20price%20data.ipynb) onto a map I needed to convert from centre latitude and longitude and zoom level (and image size), to the bounding box latitude and longitude. Handily there was a [stackoverflow question](http://stackoverflow.com/questions/13669960/calculate-bounding-box-for-openstreetmap) with some Perl code just for that. Here is a Python version for anyone interested:

{% highlight python %}
def getTileNumber(lat,lon,zoom):
    
    xtile =  (lon+180)/360 * 2**zoom  ;
    ytile =  (1 - np.log(np.tan(np.radians(lat)) + 1/np.cos(np.radians(lat)))/np.pi)/2 * 2**zoom 
    return (xtile, ytile)


def getLonLat(xtile, ytile, zoom):
    n = 2 ** zoom
    lon_deg = xtile / n * 360.0 - 180.0
    lat_deg = np.degrees(np.arctan(np.sinh(np.pi * (1 - 2 * ytile / n))))
    return (lon_deg, lat_deg)

def latLon2Box(lat, lon, zoom, width, height):
    
    tile_size = 256.0

    (xtile, ytile) = getTileNumber(lat, lon, zoom)

    xtile_s = (xtile * tile_size - width/2) / tile_size;
    ytile_s = (ytile * tile_size - height/2) / tile_size;
    xtile_e = (xtile * tile_size + width/2) / tile_size;
    ytile_e = (ytile * tile_size + height/2) / tile_size;

    (lon_s, lat_s) = getLonLat(xtile_s, ytile_s, zoom);
    (lon_e, lat_e) = getLonLat(xtile_e, ytile_e, zoom);

    return (lon_s, lat_s, lon_e, lat_e)
{% endhighlight %}