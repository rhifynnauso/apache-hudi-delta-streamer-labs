![Screenshot 2023-11-19 at 11.14.55 AM.png](..%2F..%2F..%2FDesktop%2FScreenshot%202023-11-19%20at%2011.14.55%20AM.png)


# Hudi Example Setup

### Step 1: Install PySpark 3.4.0

```bash
pip install pyspark==3.4.0
```

### Step 2: Install Java 11

```
brew install openjdk@11
# For Windows, you can download and install Java 11 from the official website:
# https://adoptopenjdk.net/?variant=openjdk11&jvmVariant=hotspot
```

### Step 3: Install Hadoop 3.7
```
# For macOS:
brew install hadoop@3.7
# For Windows Dollow the Guide
https://www.youtube.com/watch?v=0Quqj3DLC2w
```

### Step 4: Download Dataset and JAR Files
* https://drive.google.com/drive/folders/1BwNEK649hErbsWcYLZhqCWnaXFX3mIsg?usp=share_link
* https://repo.maven.apache.org/maven2/org/apache/hudi/hudi-utilities-slim-bundle_2.12/0.14.0/

### Step 5: Create hudi_tbl.props
```
# Create a file named hudi_tbl.props with the following content:
hoodie.datasource.write.keygenerator.class=org.apache.hudi.keygen.SimpleKeyGenerator
hoodie.datasource.write.recordkey.field=invoiceid
hoodie.datasource.write.partitionpath.field=destinationstate
hoodie.streamer.source.dfs.root=file:///Users/soumilnitinshah/Downloads/sampledata/
hoodie.datasource.write.precombine.field=replicadmstimestamp
```

### Step 6: Create spark-config.properties
```
# Create a file named spark-config.properties with the following content:
spark.serializer=org.apache.spark.serializer.KryoSerializer
spark.sql.catalog.spark_catalog=org.apache.spark.sql.hudi.catalog.HoodieCatalog
spark.sql.hive.convertMetastoreParquet=false
```

#### Step 7: Submit Spark Job
```
# Submit Spark Job
spark-submit \
  --class org.apache.hudi.utilities.streamer.HoodieStreamer \
  --packages org.apache.hudi:hudi-spark3.4-bundle_2.12:0.14.0 \
  --properties-file spark-config.properties \
  --master 'local[*]' \
  --executor-memory 1g \
  path/to/hudi-utilities-slim-bundle_2.12-0.14.0.jar \
  --table-type COPY_ON_WRITE \
  --op UPSERT \
  --source-ordering-field replicadmstimestamp \
  --source-class org.apache.hudi.utilities.sources.ParquetDFSSource \
  --target-base-path file:///Users/soumilnitinshah/Downloads/hudidb/ \
  --target-table invoice \
  --props hudi_tbl.props

```