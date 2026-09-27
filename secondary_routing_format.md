# Secondary Routing

Secondary routing allows the addition of objects such as canals,
ditches, drains etc to the river network.  These objects have no
watershed, they simply allow transfer of water from one location to
another location in the network.  The current network consists of blue,
purple (and possibly red) rivers, these additional objects will be
referred to as grey rivers.  Unlike the blue or purple rivers, grey
rivers can cross over existing lakes or rivers without intersecting or
interacting with them, e.g.  the Selwyn RDR comes from a blue river,
crosses over (and not interacting with) a lot of intermediary blue
rivers, finally discharging into another blue river.

## Specifying a grey river

A grey river is defined by its geometry and two 'ends'.  The geometry
is an ordered list of (unoriented) LineString or Polygon geometries.
Ordered means that the first and last elements of the list are at either
end of the grey river, and the elements in between are ordered along
it.  Because the individual geometries in the list might come from a
LINZ canal type layer, there is no requirement for those geometries to
be oriented (since LINZ layers often aren't).

The 'ends' of the grey river are point locations.  They might not be
exactly at the start or end of the geometry, nor intersect exactly with
existing network locations such as rivers or lakes.  They specify the
centre of a search location where the closest river, lake, or other
grey river is selected as the start or end of the grey river.

## JSON input file

A JSON file is a txt file with a prescribed format, and is editable in
programs such as notepad.  Here is an example of single grey river:
```
    {
        "name": "an optional name for us",
        "start": [1497544.0, 5175607.0],
        "end": [-43.6733, 171.8676],
        "snap_start": "river",
        "snap_end": "lake",
        "geoms": ["50250:8308217", "50250:8309456"]
    }
```
The name is an optional identifier which might help the user.  Both
start and end are coordinates of two points, in easting/northing
or lat/lng coordinates (either is acceptable).  `snap_start` and `snap_end` specify how the
start and end coordinates are used.  Possible values are "river",
"lake", or "grey":

* river means the closest point on a blue will be used as the
      start or end of the grey.
* lake means the closest point on the closest lake will be used.
* grey means the closest point on the closest grey river that is also
      specified in this file.


In the above example a search around the point easting=1497544.0, northing=5175607.0 for a river
will be made, the closest point will be prepended to the geometry
specified in the `geoms` field meaning the grey river will attach to
the blue river found.  The `snap_end` means the lake closest to the
point at latitude=-43.6733, longitude=171.8676 is selected as the end of
the grey line.  If `snap_start` or `snap_end` is not specified then no
snapping to the closest entity is performed.

In the above example the grey river will be made up of two geometries, both
comes from the LINZ
[layer](https://data.linz.govt.nz/layer/50250-nz-canal-centrelines-topo-150k/)
ID 50250 with `t50_fid` 8308217 and 8309456.

This example:
```
    {
        "name": "an optional name for us",
        "start": [1500611.981007509,5177214.466449265],
        "end": [1502807.1559468354,	5175786.872511624],
        "snap_start": "lake",
        "snap_end": "lake",
        "geoms": ["canalfile.gpkg:line:id=13"]
    }
```
is similar to the first, except the geometry comes from a GeoPackage you
provide called [canalfile.gpkg](canalfile.gpkg) (you can download that to see the example) with a layer `line` with `id` 13.  You can make
such lines in QGIS by creating a making a new layer and drawing the line.

