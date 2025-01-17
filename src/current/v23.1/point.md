---
title: POINT
summary: A POINT is a geometry object; a sizeless location identified by its X and Y coordinates.
toc: true
docs_area: reference.sql
---

A `POINT` is a sizeless location identified by its X and Y coordinates. These coordinates are then translated according to the current [spatial reference system](architecture/glossary.html#spatial-reference-system) (denoted by an [SRID](architecture/glossary.html#srid)) to determine what the Point "is", or what it "means" relative to the [other spatial objects](spatial-data-overview.html#spatial-objects) (if any) in the data set. 

{% include {{page.version.version}}/spatial/zmcoords.md %}

## Examples

### SQL

A Point can be created in SQL by the `st_point` function.

The statement below creates a Point (using the common [SRID 4326](architecture/glossary.html#srid)).

{% include_cached copy-clipboard.html %}
~~~ sql
SELECT ST_SetSRID(ST_Makepoint(0,0), 4326);
~~~

~~~
                      st_setsrid
------------------------------------------------------
  0101000020E610000000000000000000000000000000000000
(1 row)
~~~

### Well known text

A Point can be created from SQL by calling the `st_geomfromtext` function on a Point definition expressed in the [Well Known Text (WKT)](architecture/glossary.html#wkt) format as shown below.

{% include_cached copy-clipboard.html %}
~~~ sql
SELECT ST_GeomFromText('POINT(0 0)');
~~~

~~~
               st_geomfromtext
----------------------------------------------
  010100000000000000000000000000000000000000
(1 row)
~~~

## See also

- [Spatial tutorial](spatial-tutorial.html)
- [Spatial objects](spatial-data-overview.html#spatial-objects)
- [LINESTRING](linestring.html)
- [POLYGON](polygon.html)
- [MULTIPOINT](multipoint.html)
- [MULTILINESTRING](multilinestring.html)
- [MULTIPOLYGON](multipolygon.html)
- [GEOMETRYCOLLECTION](geometrycollection.html)
- [Using GeoServer with CockroachDB](geoserver.html)
