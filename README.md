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

## Use-Cases

**Example 1**: Find all vehicle traces started within a specified time period and select their respective WKT `LINESTRING` values, to be drawn on the map. Additionally, select the duration of each such trace, and calculate the distance between the starting point and the ending point of the trace.

- [SPARQL Query Definition via SPARQL Protocol URL](http://sage.demo.openlinksw.com/sparql?default-graph-uri=&qtxt=prefix+traces%3A+%3Chttp%3A%2F%2Fwww.tomtom.com%2Fontologies%2Ftraces%23%3E%0D%0Aprefix+geo%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0D%0Aprefix+sf%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fsf%23%3E%0D%0Aprefix+geof%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Ffunction%2Fgeosparql%2F%3E%0D%0Aprefix+units%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Fuom%2FOGC%2F1.0%2F%3E%0D%0A%0D%0ASELECT+%3Fwkt+%3Fdate+%3Fduration+%3Fdistance+%3FtraceID%0D%0AFROM+%3Chttp%3A%2F%2Fsageproject.eu%2Fdata%2Ftomtom%2Fleipzig%23%3E%0D%0AWHERE+%7B+%0D%0A++++%3Ftrace+a+traces%3ATrace+%3B%0D%0A++++++++geo%3AhasGeometry+%3FtraceGeom+%3B%0D%0A++++++++traces%3AhasStartPoint+%3Fstart+%3B%0D%0A++++++++traces%3AhasEndPoint+%3Fend+%3B%0D%0A++++++++traces%3AhasDuration+%3Fduration+%3B%0D%0A++++++++traces%3AnumID+%3FtraceID+.%0D%0A++++%3FtraceGeom+geo%3AasWKT+%3Fwkt+.%0D%0A++++%3Fstart+traces%3AhasTimestamp+%3Fdate+.%0D%0A++++%0D%0A++++FILTER+%28%3Fdate+%3E%3D+%222017-05-03T06%3A00%3A00Z%22%5E%5Exsd%3AdateTime+%26%26+%3Fdate+%3C%3D+%222017-05-03T23%3A45%3A00Z%22%5E%5Exsd%3AdateTime%29%0D%0A++++%0D%0A++++%3Fstart+geo%3AhasGeometry+%3FstartGeom+.%0D%0A++++%3FstartGeom+geo%3AasWKT+%3FstartWKT+.%0D%0A++++%3Fend+geo%3AhasGeometry+%3FendGeom+.%0D%0A++++%3FendGeom+geo%3AasWKT+%3FendWKT+.%0D%0A++++++++%0D%0A++++BIND%28geof%3Adistance%28%3FstartWKT%2C+%3FendWKT%2C+units%3Ameter%29+as+%3Fdistance%29%0D%0A%7D%0D%0AORDER+BY+%3Fdate%0D%0ALIMIT+20&format=text%2Fhtml&timeout=0&debug=on)
- [SPARQL Query Results via SPARQL Protocol URL](http://sage.demo.openlinksw.com/sparql?default-graph-uri=&query=prefix+traces%3A+%3Chttp%3A%2F%2Fwww.tomtom.com%2Fontologies%2Ftraces%23%3E%0D%0Aprefix+geo%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0D%0Aprefix+sf%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fsf%23%3E%0D%0Aprefix+geof%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Ffunction%2Fgeosparql%2F%3E%0D%0Aprefix+units%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Fuom%2FOGC%2F1.0%2F%3E%0D%0A%0D%0ASELECT+%3Fwkt+%3Fdate+%3Fduration+%3Fdistance+%3FtraceID%0D%0AFROM+%3Chttp%3A%2F%2Fsageproject.eu%2Fdata%2Ftomtom%2Fleipzig%23%3E%0D%0AWHERE+%7B+%0D%0A++++%3Ftrace+a+traces%3ATrace+%3B%0D%0A++++++++geo%3AhasGeometry+%3FtraceGeom+%3B%0D%0A++++++++traces%3AhasStartPoint+%3Fstart+%3B%0D%0A++++++++traces%3AhasEndPoint+%3Fend+%3B%0D%0A++++++++traces%3AhasDuration+%3Fduration+%3B%0D%0A++++++++traces%3AnumID+%3FtraceID+.%0D%0A++++%3FtraceGeom+geo%3AasWKT+%3Fwkt+.%0D%0A++++%3Fstart+traces%3AhasTimestamp+%3Fdate+.%0D%0A++++%0D%0A++++FILTER+%28%3Fdate+%3E%3D+%222017-05-03T06%3A00%3A00Z%22%5E%5Exsd%3AdateTime+%26%26+%3Fdate+%3C%3D+%222017-05-03T23%3A45%3A00Z%22%5E%5Exsd%3AdateTime%29%0D%0A++++%0D%0A++++%3Fstart+geo%3AhasGeometry+%3FstartGeom+.%0D%0A++++%3FstartGeom+geo%3AasWKT+%3FstartWKT+.%0D%0A++++%3Fend+geo%3AhasGeometry+%3FendGeom+.%0D%0A++++%3FendGeom+geo%3AasWKT+%3FendWKT+.%0D%0A++++++++%0D%0A++++BIND%28geof%3Adistance%28%3FstartWKT%2C+%3FendWKT%2C+units%3Ameter%29+as+%3Fdistance%29%0D%0A%7D%0D%0AORDER+BY+%3Fdate%0D%0ALIMIT+20&format=text%2Fhtml&timeout=0&debug=on)

**Example 2**: Find all vehicle traces which have a given map region as a destination. The query selects all traces which satisfy the constraints, gets their WKT `LINESTRING` values, their duration and calculates the distance between the starting point and the ending point of the trace.

- [SPARQL Query Definition via SPARQL Protocol URL](http://sage.demo.openlinksw.com/sparql?default-graph-uri=&qtxt=prefix+traces%3A+%3Chttp%3A%2F%2Fwww.tomtom.com%2Fontologies%2Ftraces%23%3E%0D%0Aprefix+geo%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0D%0Aprefix+sf%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fsf%23%3E%0D%0Aprefix+geof%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Ffunction%2Fgeosparql%2F%3E%0D%0Aprefix+units%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Fuom%2FOGC%2F1.0%2F%3E%0D%0A%0D%0ASELECT+%3Fwkt+%3Fdate+%3Fduration+%3Fdistance+%3FtraceID%0D%0AFROM+%3Chttp%3A%2F%2Fsageproject.eu%2Fdata%2Ftomtom%2Fleipzig%23%3E%0D%0AWHERE+%7B+%0D%0A++++%3Ftrace+a+traces%3ATrace+%3B%0D%0A++++++++geo%3AhasGeometry+%3FtraceGeom+%3B%0D%0A++++++++traces%3AnumID+%3FtraceID+%3B%0D%0A++++++++traces%3AhasDuration+%3Fduration+%3B%0D%0A++++++++traces%3AhasStartPoint+%3Fstart+%3B%0D%0A++++++++traces%3AhasEndPoint+%3Fend+.%0D%0A++++%3FtraceGeom+geo%3AasWKT+%3Fwkt+.%0D%0A++++%3Fstart+traces%3AhasTimestamp+%3Fdate+%3B%0D%0A++++++++geo%3AhasGeometry+%3FstartGeom+.%0D%0A++++%3FstartGeom+geo%3AasWKT+%3FstartWKT+.%0D%0A++++%3Fend+geo%3AhasGeometry+%3FendGeom+.%0D%0A++++%3FendGeom+geo%3AasWKT+%3FendWKT+.%0D%0A%0D%0A++++FILTER%28geof%3AsfContains%28bif%3AST_GeometryFromText%28%22POLYGON%28%2812.346821967457686+51.34679532759259%2C12.348366919850264+51.34703657322223%2C12.350898925160323+51.347063378213775%2C12.352486792897139+51.34655408069294%2C12.353817168568526+51.34574991518828%2C12.354503814076338+51.344490027525744%2C12.354503814076338+51.343417755424134%2C12.35394591460124+51.34258672728989%2C12.35347384581462+51.34175568408737%2C12.351928893422041+51.34097824293381%2C12.349997702931319+51.34062973054985%2C12.347508612965498+51.34081739139357%2C12.345491591786299+51.341809300232576%2C12.344676200245772+51.34301564691818%2C12.344590369557295+51.344490027525744%2C12.345148269032393+51.34566949786171%2C12.346821967457686+51.34679532759259%29%29%22%29%2C+%3FendWKT%29%29%0D%0A%0D%0A++++BIND%28geof%3Adistance%28%3FstartWKT%2C+%3FendWKT%2C+units%3Ameter%29+as+%3Fdistance%29%0D%0A%7D%0D%0AORDER+BY+%3Fdate%0D%0ALIMIT+20&format=text%2Fhtml&timeout=0&debug=on)
- [SPARQL Query Results via SPARQL Protocol URL](http://sage.demo.openlinksw.com/sparql?default-graph-uri=&query=prefix+traces%3A+%3Chttp%3A%2F%2Fwww.tomtom.com%2Fontologies%2Ftraces%23%3E%0D%0Aprefix+geo%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0D%0Aprefix+sf%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fsf%23%3E%0D%0Aprefix+geof%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Ffunction%2Fgeosparql%2F%3E%0D%0Aprefix+units%3A+%3Chttp%3A%2F%2Fwww.opengis.net%2Fdef%2Fuom%2FOGC%2F1.0%2F%3E%0D%0A%0D%0ASELECT+%3Fwkt+%3Fdate+%3Fduration+%3Fdistance+%3FtraceID%0D%0AFROM+%3Chttp%3A%2F%2Fsageproject.eu%2Fdata%2Ftomtom%2Fleipzig%23%3E%0D%0AWHERE+%7B+%0D%0A++++%3Ftrace+a+traces%3ATrace+%3B%0D%0A++++++++geo%3AhasGeometry+%3FtraceGeom+%3B%0D%0A++++++++traces%3AnumID+%3FtraceID+%3B%0D%0A++++++++traces%3AhasDuration+%3Fduration+%3B%0D%0A++++++++traces%3AhasStartPoint+%3Fstart+%3B%0D%0A++++++++traces%3AhasEndPoint+%3Fend+.%0D%0A++++%3FtraceGeom+geo%3AasWKT+%3Fwkt+.%0D%0A++++%3Fstart+traces%3AhasTimestamp+%3Fdate+%3B%0D%0A++++++++geo%3AhasGeometry+%3FstartGeom+.%0D%0A++++%3FstartGeom+geo%3AasWKT+%3FstartWKT+.%0D%0A++++%3Fend+geo%3AhasGeometry+%3FendGeom+.%0D%0A++++%3FendGeom+geo%3AasWKT+%3FendWKT+.%0D%0A%0D%0A++++FILTER%28geof%3AsfContains%28bif%3AST_GeometryFromText%28%22POLYGON%28%2812.346821967457686+51.34679532759259%2C12.348366919850264+51.34703657322223%2C12.350898925160323+51.347063378213775%2C12.352486792897139+51.34655408069294%2C12.353817168568526+51.34574991518828%2C12.354503814076338+51.344490027525744%2C12.354503814076338+51.343417755424134%2C12.35394591460124+51.34258672728989%2C12.35347384581462+51.34175568408737%2C12.351928893422041+51.34097824293381%2C12.349997702931319+51.34062973054985%2C12.347508612965498+51.34081739139357%2C12.345491591786299+51.341809300232576%2C12.344676200245772+51.34301564691818%2C12.344590369557295+51.344490027525744%2C12.345148269032393+51.34566949786171%2C12.346821967457686+51.34679532759259%29%29%22%29%2C+%3FendWKT%29%29%0D%0A%0D%0A++++BIND%28geof%3Adistance%28%3FstartWKT%2C+%3FendWKT%2C+units%3Ameter%29+as+%3Fdistance%29%0D%0A%7D%0D%0AORDER+BY+%3Fdate%0D%0ALIMIT+20&format=text%2Fhtml&timeout=0&debug=on)
