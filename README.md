# TrafficData-to-GeoSPARQL

This project represents a set of SPARQL-based transformation which turn an RDF dataset with traffic data (from the TomTom synthetic traces generator) into a GeoSPARQL compliant dataset.

## Data Structure

The original RDF dataset has `Trace` entities, which consist of `Point` entities. Each `Point` has a `latitude` value, a `longitude` value, a `timestamp` and a `Speed` entity. The `Speed` entity has a `velocity` value and a `metric`.

## Trace Transformations

Each `Trace` is enhanced to represent a `geo:Feature` entity, which has a `geo:hasGeometry` relation with a geometry entity specified as a `sf:LineString` entity. The `sf:LineString` entity has a `geo:asWKT` relation to a `geo:wktLiteral` value, which represents the entire `Trace` as a GeoSPARQL `LINESTRING`.

## Point Transformations

Each `Point` is enhanced to represent a `geo:Feature` entity, which has a `geo:hasGeometry` relation with a geometry entity specified as a `sf:Point` entity. The `sf:Point` entity has a `geo:asWKT` relation to a `geo:wktLiteral` value, which represents the `Point` as a GeoSPARQL `POINT`.

## Additional Transformations

To support specific use-cases as part of our [SAGE Project](http://sageproject.eu/), we add several additional transformations, which do not necessarily relate to GeoSPARQL:

- Adding a numerical ID to each trace, via a new property: `traces:numID`;
- Adding an explicit relation between a `Trace` and its start and end points, via new properties: `traces:hasStartPoint` and `traces:hasEndPoint`, respectively;
- Adding an explicit relation between a `Trace` and its calculated duration, in seconds, via a new property: `traces:hasDuration`;

## Variables

The queries denote the named graph as `<myGraphIRI>`, which should be replaced with the actual graph IRI you want to use the enhancement queries on.