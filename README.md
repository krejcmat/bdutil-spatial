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
