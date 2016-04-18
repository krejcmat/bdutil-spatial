# bdutil-spatial
 Google Cloud Platform: Dataproc - deployment of Hadoop cluster with several spatial frameworks

#Hive SerDe schema generator
1)[install scala](http://www.scala-sbt.org/download.html)

2)Clone repository with generator
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

