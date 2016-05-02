# bdutil-spatial
 Google Cloud Platform: Dataproc - deployment of Hadoop cluster with several spatial frameworks






#Notes
##SerDe
SerDe from version Hive 1.2 is included in hcatalog core
```
add jar ${env:HIVE_HOME}/hcatalog/share/hcatalog/hcatalog-core-0.12.0.jar;
```
```
CREATE EXTERNAL TABLE links1 (
type string,
properties map<string,string>,
geometry string
)ROW FORMAT SERDE 'org.apache.hcatalog.data.JsonSerDe'
STORED AS TEXTFILE;
```
!! SerDe from hcatalog doesn`t work properly with GeoJson. Hcatalog serde not allows to parse array in array structures as string which can be usefeful for conversion to geom datatype(for Esri Spatial Framework)
```
#GeoJson
{ "geometry": { "type": "LineString", "coordinates": [ [ 15.7912, 50.2392 ], [ 15.7451, 50.23 ] ] } }
#
select ST_GeomFromGeoJSON(geometry) from links1;
```

Solution is to use [SerDe](https://github.com/rcongiu/Hive-JSON-Serde) .
Compiling SerDe for Google Cloud must be with CDH4 mvn profile
```
sudo apt-get install -y maven openjdk-7-jdk git  && \
git clone https://github.com/rcongiu/Hive-JSON-Serde.git && \
cd Hive-JSON-Serde && \
mvn -Pcdh4 clean package
```

##Hive SerDe schema generator
1) [install scala](http://www.scala-sbt.org/download.html)

2) Clone repository of hive schema generator
```
$ git clone https://github.com/strelec/hive-serde-schema-gen.git
```

3) Convert
```
$ cd hive-serde-schema-gen
$ touch test.json && echo '"{ "type": "Feature", "properties": { "cat": 546 }, "geometry": { "type": "LineString", "coordinates": [ [ 14.4881, 50.1503 ], [ 14.5033, 50.1508 ] ] } }"' > test.json
$ sbt "run test.json"
```

4) Output
```
CREATE TABLE data (
	type VARCHAR(7),
	properties STRUCT<
		cat: SMALLINT
	>,
	geometry STRUCT<
		coordinates: ARRAY<
			ARRAY<
				FLOAT
			>
		>
		type: VARCHAR(10)
	>
) ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.JsonSerde';

LOAD DATA LOCAL INPATH 'test.json' INTO TABLE data;
```


