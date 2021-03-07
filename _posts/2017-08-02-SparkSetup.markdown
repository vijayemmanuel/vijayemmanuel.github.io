---
layout: post
title: Apache Spark on Windows
date: 2017-08-02 19:20:23 +0900
category: spark
---

Finally I was able to set up Apache Spark on my Windows. I always thought setting up Spark on Windows would be tough to do , as it was build to run on Linux based systems, but now after setting it up, its not that tough, not easy either.

So let me list out the steps I followed to set up Spark :

 ### Step 1: 
 
 Go to <https://spark.apache.org/downloads.html> and download Spark package

![Download Spark](/assets/sparksetup/download-spark.png){:class="img-responsive"}

### Step 2: 

Unzip the package on the Windows PC

### Step 3:  

Create new environment variable SPARK_HOME pointing to the unzipped Spark package location. (directory location where we have the bin folder). Update PATH environment variable with %SPARK_HOME%\bin

### Step 4: 

Ensure that  you already have JAVA configured correctly on PC and we have JAVA_HOME environment variable set correctly.

### Step 5: 

Ensure that you have the correct Scala version available. Normally I checked it by looking into the jars folder available in %SPARK_HOME%. If we have the jars compiled for version Scala 2.11.X , I configured SCALA_HOME to point to Scala 2.11.X. Scala binaries can be downloaded from https://www.scala-lang.org/. Update PATH environment variable with %SCALA_HOME%\bin

![Download Spark](/assets/sparksetup/download-scala.png){:class="img-responsive"}

### Step 6: 

Ensure that you have SBT set up. Simple Built Tool (SBT) can be downloaded from http://www.scala-sbt.org/. Update PATH environment variable with the location where SBT was unzipped.

![Download Spark](/assets/sparksetup/download-sbt.png){:class="img-responsive"}

### Step 7: Download winutils.exe binary from https://github.com/steveloughran/winutils  repository.

Save the downloaded hadoop zip package in a directory of your choice, unzip it under a folder named “bin” and set the environment variable HADOOP_HOME to reflect the directory with winutils.exe (without bin).Update PATH environment variable with %HADOOP_HOME%\bin.
Create a directory called C:\tmp\hive. If you do have access to create tmp folder in parent drive, you could set to other directory location as well. but you need to create appropriate configuration setting for hive. This can be done by pointing hive.exec.scratchdir to the new location.
Create a hive-site.xml file with the content below. You need to create a environment variable HADOOP_CONF_DIR pointing to location of hive-site.xml

![Download Spark](/assets/sparksetup/hive-site.png){:class="img-responsive"}

### Step 8: 

Run command window and type spark-shell, which should give the result below.

![Download Spark](/assets/sparksetup/spark-shell.png){:class="img-responsive"}

### Step 9: 

Enjoy Sparking…