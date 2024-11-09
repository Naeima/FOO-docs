# How to query FOO

# Competency Questions and their Formulated SPARQL Queries

## CQ 01: Where does Elephant Jasmine forage?

```sparql
# let the date be 2011-11-13
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT * {
    ?observation  a  foo:gPSObservation ;
            foo:localDate    "2011-11-13"^^xsd:date ;
            geo:latitude   ?Latitude ;
            geo:longitude  ?longitude .
}
```

## CQ 02: What are the daily movement patterns for Elephant X in June?

```sparql
# Let elephant X be Abaw
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT DISTINCT *
WHERE {
  ?observation a  foo:gPSObservation ;
              foo:madeBySensor  foo:abawGPS ;
              foo:localDate ?date;
              geo:longitude ?long;
              geo:latitude ?lat.
  FILTER(?date >= "2014-06-01"^^xsd:date && ?date <= "2014-06-30"^^xsd:date)
}
```

## CQ 03: What are the yearly movement patterns for Elephant X?

```sparql
# Let elephant X be Puteri
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT * {
  ?observation  a foo:gPSObservation ;
              foo:madeBySensor   foo:puteriGPS ;
              foo:localDate  ?date ;
              geo:latitude   ?Latitude ;
              geo:longitude  ?longitude .
  FILTER(?date >= "2013-12-31"^^xsd:date && ?date <= "2014-12-31"^^xsd:date)
}
```

## CQ 04: How do the movements of Elephant X relate to human and urban areas?

```sparql
# Let human and urban areas be the oil Palm Plantation and elephant X be Jasmine
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT * {
  ?observation  a foo:gPSObservation ;
            foo:madeBySensor  foo:jasminGPS ;
            foo:hasFeatureOfInterest  ?elephant ;
            foo:localDate    ?date ;
            geo:latitude   ?Latitude ;
            geo:longitude  ?longitude .
  foo:OilPalmPlantation  rdfs:label   ?Label
}
```

## CQ 05: Has elephant X died?

```sparql
# Let elephant X be Sandi 
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT DISTINCT * {
  ?observation  a foo:gPSObservation;
        foo:madeBySensor     foo:sandiGPS ;
        foo:hasFeatureOfInterest   foo:sandi ;
        foo:localDate ?date;
        foo:cov ?cov;
        foo:speed ?speed.  
   FILTER(?cov = "0.0"^^xsd:float && ?speed <= "0.1"^^xsd:float)
}
```

## CQ 06: Why did Elephant X die?

```sparql
# Searching near (10 Kilometer far) human-dominated landscapes.
PREFIX sosa: <http://www.w3.org/ns/sosa/>
PREFIX foo: <http://example.org/foo#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX unit: <http://qudt.org/vocab/unit#> 
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX time: <http://www.w3.org/2006/time#>
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
SELECT * {
  ?s            a    sosa:Observation;
                 time:gMTDate         "2011-11-13"^^xsd:date;
                 time:gMTTime         ?GMTTime;
                 pos:long ?long;                                
                 pos:lat    ?lat;
  geof:nearby (5.674 118.393  10 unit:Kilometer).
  # Assuming the oil palm plantation is located within 10 Kilometers from these coordinates.
  ?s1 
    pos:long ?long1;                                
    pos:lat ?lat1;
    time:gMTDate "2011-11-13"^^xsd:date;
  BIND (geof:distance(?s, ?s1, unit:Kilometer) as ?Distance)
}
```

## CQ 08: What can we learn from the movements of Elephants X, Y, and Z?

```sparql
# Let Elephants X, Y, and Z be Aqeela, Ita, and Dara.
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
SELECT DISTINCT *
WHERE {
  {
    ?AqeelaGPS  a  foo:gPSObservation;
                pos:longitude ?longX;
                pos:latitude ?latX;
                ?predicateX ?elephantAqeela.
    FILTER(?elephantAqeela = foo:aqeela)
  }
  UNION
  {
    ?ItaGPS  a  foo:gPSObservation;
                pos:longitude ?longY;
                pos:latitude ?latY;
                ?predicateY ?elephantIta.
    FILTER(?elephantIta = foo:ita)
  }
  UNION
  {
    ?DaraGPS  a  foo:gPSObservation;
                pos:longitude ?longZ;
                pos:latitude ?latZ;
                ?predicateZ ?elephantDara.
    FILTER(?elephantDara = foo:dara)
  }
}
```

## CQ 09: How does Elephant X use Habitat Site Y?

```sparql
# Let elephant X be Aqeela.
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX unit: <http://www.opengis.net/def/uom/OGC/1.0/>
SELECT * {
  SELECT DISTINCT ?elephantGPS  ?Longitude ?Latitude
  WHERE {
    ?observation  a  foo:gPSObservation;
                 foo:madeBySensor   ?elephantGPS ;
                 foo:hasFeatureOfInterest   ?elephant ;
                 pos:longitude   ?Longitude ;
                 pos:latitude    ?Latitude.
    BIND(STRDT(CONCAT("POINT(", STR(?Longitude), " ", STR(?Latitude), ")"),
    geo:wktLiteral) AS ?observationPoint)
    BIND(STRDT("POINT(118.3019 5.510)", geo:wktLiteral) AS ?referencePoint)
    FILTER(geof:distance(?observationPoint, ?referencePoint, unit:metre) < 100)
  }
}
```

## CQ 10: What is the range of habitat sites used by Elephants X, Y, and Z?

```sparql
# Let Elephants X, Y, Z be Ita, Abaw, and Jasmine.
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT *
WHERE {
  ?observation a foo:gPSObservation ;
                foo:madeBySensor     ?gpsCollar   ;
                geo:latitude ?latitude ;
                geo:longitude ?longitude .
  FILTER ( ?gpsCollar IN (foo:jasminGPS, foo:abawGPS, foo:itaGPS ))
}
```

## CQ 11: Where was Elephant X located during the flood season in the Lower Kinabatangan area?

```sparql
# Note: Flooding occurs between November and March during the west monsoon.
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT DISTINCT *
WHERE {
  ?observation a    foo:gPSObservation;
                    foo:madeBySensor    ?elephantGPS ;
                    foo:hasFeatureOfInterest   ?elephant ;
                    foo:localDate     ?date ;
                    pos:longitude ?long ;
                    pos:latitude  ?lat  .
  FILTER(?date >= "2011-11-01"^^xsd:date && ?date <= "2012-03-30"^^xsd:date)
}
```

## CQ 12: What was the average speed of Elephant X during the flood season?

```sparql
# Note: Flooding occurs between November and March during the west monsoon.
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT (AVG(?speed) AS ?AVGspeed)
WHERE {
  ?observation a foo:gPSObservation;
               foo:speed  ?speed ;
               foo:date   ?date .
  FILTER (?date >= "2012-02-07"^^xsd:date && ?date < "2012-02-15"^^xsd:date)
}
```

## CQ 13: Is Elephant Dara near (5 Kilometers far) the danger zone (poachers' area) today?

```sparql
# Let poacher area be POINT(117.30193 5.510).
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX unit: <http://qudt.org/vocab/unit#>
SELECT DISTINCT *
WHERE {
  ?observation a foo:gPSObservation;
             pos:longitude ?Longitude;
             pos:latitude ?Latitude.
  BIND(STRDT(CONCAT("POINT(", STR(?Longitude), " ", STR(?Latitude), ")"),
  geo:wktLiteral) AS ?observationPoint)
  BIND("POINT(117.30193 5.510)"^^geo:wktLiteral AS ?referencePoint)
  FILTER(geof:distance(?observationPoint, ?referencePoint, unit:Kilometer) < 5)
}
```

## CQ 14: How did Elephant X's movements change with climate change in 2014?

```sparql
# We need weather data to answer this.
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
SELECT *
WHERE {
  ?observation a   foo:gPSObservation ;
                foo:localDate   ?date ;
                pos:latitude ?latitude ;
                pos:longitude ?longitude .
  FILTER (?date >= "2014-01-01"^^xsd:date && ?date <= "2014-12-31"^^xsd:date)
}
```

## CQ 15: What are Elephant X's preferred habitats based on prolonged stays in areas?

```sparql
# Foraging area focus between Abai and Batu Puteh (5.18°N - 5.42°N, 117.54°E - 118.33°E)
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
SELECT ?location ?latitude ?longitude (COUNT(?date) AS ?stayDuration)
WHERE {
  ?observation a  foo:gPSObservation ;
               foo:FeatureOfInterest ?elephant ;
               foo:date              ?date ;
               pos:latitude          ?latitude ;
               pos:longitude         ?longitude .
  FILTER (?latitude >= "5.18"^^xsd:float && ?latitude <= "5.42"^^xsd:float)
  FILTER (?longitude >= "117.54"^^xsd:float && ?longitude <= "118.33"^^xsd:float)
  BIND(CONCAT(STR(?latitude), ",", STR(?longitude)) AS ?location)
}
GROUP BY ?location ?latitude ?longitude
HAVING (?stayDuration >= 0)
ORDER BY DESC(?stayDuration)
```

## CQ 16: How far was Elephant X from the oil plantation fencing?

```sparql
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX unit: <http://qudt.org/vocab/unit#>
SELECT DISTINCT ?s ?plantationLocation (geof:distance(?geo1, ?plantationLocation, unit:Kilometer) AS ?Distance)
WHERE {
  ?s a foo:gPSObservation;
     pos:longitude ?long;                                
     pos:latitude ?lat;
     foo:localDate ?date.
  BIND(STRDT(CONCAT("POINT(", STR(?long), " ", STR(?lat), ")"), geo:wktLiteral) AS ?geo1)
  BIND("POINT(118.393 5.674)"^^geo:wktLiteral AS ?plantationLocation)
  FILTER (geof:distance(?plantationLocation, ?geo1, unit:Kilometer) <= 5)
}
```

## CQ 17: When was Elephant X near the oil plantation fencing?

```sparql
# Let elephant X be Ita.
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX unit: <http://qudt.org/vocab/unit#>
SELECT *
WHERE {
  ?s  a  foo:gPSObservation ;
     foo:madeBySensor  foo:itaGPS;
     foo:hasFeatureOfInterest   foo:ita; 
     foo:localDate ?localDate;
     foo:localTime ?localTime;
     pos:longitude ?long;
     pos:latitude ?lat.    
  BIND(STRDT(CONCAT("POINT(", STR(?long), " ", STR(?lat), ")"), geo:wktLiteral) AS ?observationPoint)
  BIND("POINT(118.393 5.674)"^^geo:wktLiteral AS ?referencePoint)  
  BIND(geof:distance(?observationPoint, ?referencePoint, unit:Kilometer) AS ?Distance)
  FILTER(?Distance <= 5)
}
```

## CQ 18: What is the distance traveled between each of Elephant X's stops (sleeping)?

```sparql
# Calculating distances between stops where significant time differences indicate resting.
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX unit: <http://qudt.org/vocab/unit#>
PREFIX foo: <https://w3id.org/def/foo#>
SELECT ?prevStop ?prevDate ?nextStop ?nextDate ?distanceTraveled
WHERE {
  ?prevStop a foo:gPSObservation ;
            foo:hasFeatureOfInterest foo:ita ;
            foo:localDate ?prevDate ;
            pos:longitude ?prevLong ;
            pos:latitude ?prevLat .
  ?nextStop a foo:gPSObservation;
            foo:hasFeatureOfInterest foo:ita ;
            foo:localDate ?nextDate ;
            pos:longitude ?nextLong ;
            pos:latitude ?nextLat .
  FILTER (?nextDate > ?prevDate)
  BIND(STRDT(CONCAT("POINT(", STR(?prevLong), " ", STR(?prevLat), ")"), geo:wktLiteral) AS ?prevLocation)
  BIND(STRDT(CONCAT("POINT(", STR(?nextLong), " ", STR(?nextLat), ")"), geo:wktLiteral) AS ?nextLocation)
  BIND(geof:distance(?prevLocation, ?nextLocation, unit:Kilometer) AS ?distanceTraveled)
}
ORDER BY ?prevDate
```

## CQ 19: Which elephants met this month?

```sparql
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX foo:<https://w3id.org/def/foo#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
SELECT DISTINCT ?ElephantX ?ElephantY ?dateX ?dateY
WHERE {
  ?ObservationX a foo:gPSObservation;
                foo:hasFeatureOfInterest   ?ElephantX ;
                foo:localDate ?dateX;
                pos:longitude ?longX;
                pos:latitude ?latX.
  ?ObservationY a  foo:gPSObservation;
                foo:hasFeatureOfInterest   ?ElephantY ;
                foo:localDate ?dateY;
                pos:longitude ?longY;
                pos:latitude ?latY.
  FILTER(?ElephantX != ?ElephantY)
  FILTER(MONTH(xsd:date(?dateX)) = MONTH(NOW()) && MONTH(xsd:date(?dateY)) = MONTH(NOW()))
  FILTER(?longX = ?longY && ?latX = ?latY)
}
```

## CQ 20: Which sites were revisited by Elephant X this month?

```sparql
# Let elephant X be Abaw
PREFIX foo: <https://w3id.org/def/foo#>
PREFIX pos: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
SELECT ?location (COUNT(?location) AS ?visits)
WHERE {
  ?observation a  foo:gPSObservation;
               foo:hasFeatureOfInterest  foo:abaw;
               foo:localDate ?date;
               pos:longitude ?long;
               pos:latitude ?lat.
  BIND(CONCAT(STR(?long), "-", STR(?lat)) AS ?location)
  FILTER(MONTH(xsd:date(?date)) = MONTH(NOW()) && YEAR(xsd:date(?date)))
}
GROUP BY ?location
HAVING (COUNT(?location) > 1)
```