# getCenterPointFromUTM
# Finding the Center Point from UTM Coordinates in PHP
Universal Transverse Mercator (UTM) is a global map projection system used for representing the Earth's surface. It divides the Earth into a series of 6° longitudinal zones, each with its own coordinate system. The UTM system provides a set of Easting (X) and Northing (Y) coordinates to define a specific location within a given zone.

Finding the "center point" within a specific area of interest may refer to determining the central UTM coordinates for a set of UTM points, such as a polygon or a series of GPS waypoints. This document outlines a function to compute the center point (often referred to as the centroid) from UTM coordinates.

# Usage/Examples

function getCenterPointFromUTM ($coords) {

    $count_coords = count($coords);
    $xcos = $ycos = $zsin = 0.0;
    foreach ($coords as $lnglat) {
        $xpos = $lnglat[0];
        $ypos = $lnglat[1];
        $zpos = $lnglat[2];
        $latlong = utm2ll($xpos, $ypos, $zpos, true);
    
        $lat = $latlong[0] * pi() / 180;
        $lon = $latlong[1] * pi() / 180;
    
        $acos = cos($lat) * cos($lon);
        $bcos = cos($lat) * sin($lon);
        $csin = sin($lat);
        $xcos += $acos;
        $ycos += $bcos;
        $zsin += $csin;
    }
            
    $xcos /= $count_coords;
    $ycos /= $count_coords;
    $zsin /= $count_coords;
    
    $lon  = atan2($ycos, $xcos);
    $sqrt = sqrt($xcos * $xcos + $ycos * $ycos);
    $lat  = atan2($zsin, $sqrt);
    
    $center_lat = $lat * 180 / pi();
    $center_lon = $lon * 180 / pi();
    
    $coords_utm = ll2utm($center_lat, $center_lon);
    $xpos       = $coords_utm[0];
    $ypos       = $coords_utm[1];
    $zpos       = $coords_utm[2];
    
    return [
        "lat"  => $center_lat,
        "lon"  => $center_lon,
        "xpos" => $xpos,
        "ypos" => $ypos,
        "zpos" => $zpos,
    ];
}
