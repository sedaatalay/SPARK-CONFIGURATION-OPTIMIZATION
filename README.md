# SPARK CONFIGURATION OPTIMIZATION

<p> </br>
<p> </br>

## What is Spark optimization?

### Spark optimization techniques are used to modify the settings and properties of Spark to ensure that the resources are utilized properly and the jobs are executed quickly. All this ultimately helps in processing data efficiently.


#### Configurable Fields

##### The Cluster Size fields can be changed to match your cluster’s specs. It is not recommended that you change the Additional Param fields, but some use-cases might require customization. The Cluster Size fields can be changed to match your cluster’s specs. It is not recommended that you change the Additional Param fields, but some use-cases might require customization.

<img width="989" alt="Ekran Resmi 2022-05-13 17 20 59" src="https://user-images.githubusercontent.com/91700155/168304101-6f1496bf-4039-44ea-9824-6ff4516d116f.png">

 **CLUSTER SIZE**                                                                                                                                                                                         | **ADDITIONAL PARAMS**                                                                                                                                 
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------
 One of the best Spark memory optimization techniques when dealing with partitions and executors is to first choose the number of partitions, then pick an executor size to meet the memory requirements. | The following parameters help to fine tune the overall optimized configuration. We recommend to leave them as defaults.  <p> </br>  Parallelism Per Core :  The level of parallelism per allocated core. This field is used to determine the spark.default.parallelism configuration. <p> </br>  Memory overhead (%) : The percentage of memory in each executor that will be reserved for spark.executor.memoryOverhead.                                                                                                                     


<p> </br>

#### Reference Table

##### Once the configurable fields on the left-hand side of the image have been set to the desired values, the resultant cluster configuration will be reflected in the reference image. There is some degree of subjectivity in selecting the Executors Per Node setting that will work best for your use case, so I selected to use a reference image rather than selecting the number automatically.

#### Spark Configs

##### Now that we have selected an optimal number of Executors Per Node, we are ready to generate the Spark configs with which we will run our job. We enter the optimal number of executors in the Selected Executors Per Node field. The correct settings will be generated automatically.
Now that we have selected an optimal number of Executors Per Node, we are ready to generate the Spark configs with which we will run our job. We enter the optimal number of executors in the Selected Executors Per Node field. The correct settings will be generated automatically.

<img width="989" alt="Ekran Resmi 2022-05-13 18 30 29" src="https://user-images.githubusercontent.com/91700155/168317189-341b5d4a-944d-45cd-ac04-b986470434a9.png">
                                                                          

 **CALCULATED CLUSTER RESOURCES**                                                                                                                                                                                         | **SPARK-DEFAULTS.CONF**                                                                                                                                 
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------
Total memory (GB) : Default number of partitions in RDDs. We recommend that you estimate the size of each partition and adjust this number accordingly using coalesce or repartition. <p> </br> Total overhead memory : Total overhead memory in the cluster. <p> </br> Total available cores : Total cluster cores availible for node containers. <p> </br> Total available memory (GB) : Total cluster memory availible for node containers. <p> </br>  Memory per executor (GB) : This total memory per executor includes the executor memory and overhead (spark.executor.memoryOverhead). <p> </br> **UNUSED RESOURCES** <p> </br> A good rule of thumb for selecting the optimal number of Executors Per Node would be to select the setting that minimizes Unused Memory Per Node and Unused Cores Per Node while keeping Total Memory Per Executor below the Executor Memory Upper Bound and Core Per Executor below the Executor Core Upper Bound. | spark.default.parallelism : Default number of partitions in RDDs. We recommend that you estimate the size of each partition and adjust this number accordingly using coalesce or repartition. <p> </br> spark.executor.memory (GB) : Amount of memory to use per executor process. <p> </br> spark.executor.instances : Final number of executor instances. <p> </br> spark.driver.cores : Amount of memory to use for the driver process. <p> </br>  spark.executor.cores : The number of cores to use on each executor. <p> </br> spark.driver.memory (GB) : Amount of memory to use for the driver process.  <p> </br> spark.driver.maxResultSize (GB) : Limit of total size of serialized results of all partitions for each Spark action (e.g. collect). <p> </br> spark.driver.memoryOverhead (MB) : Amount of non-heap memory to be allocated per driver process in cluster mode, in MiB unless otherwise specified. This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. This tends to grow with the container size (typically 6-10%). This option is currently supported on YARN, Mesos and Kubernetes. <p> </br> spark.executor.memoryOverhead (MB) : Amount of additional memory to be allocated per executor process in cluster mode, in MiB unless otherwise specified. This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. This tends to grow with the executor size (typically 6-10%). This option is currently supported on YARN and Kubernetes. <p> </br> spark.dynamicAllocation.enabled : Spark on YARN can dynamically scale the number of executors used for a Spark application based on the workloads. This is the configuration responsible for it. <p> </br> spark.sql.adaptive.enabled : Adaptive Query Execution is an optimization technique in Spark SQL that makes use of the runtime statistics to choose the most efficient query execution plan.
                                                                                                        

#### Using Configs

##### Now that we have the proper numbers for our configs, using them is fairly simple. Below, I’ve demonstrated 2 different ways the configs might be used:

#### spark-submit
```console
./bin/spark-submit --name "luminousmen app" --class <app class> --master yarn --deploy-mode cluster --num-executors 14 --executor-memory 9g --executor-cores 5 --driver-memory 9g --driver-cores 5 --conf spark.default.parallelism=140 --conf spark.driver.maxResultSize=9g --conf spark.driver.memoryOverhead=921m --conf spark.executor.memoryOverhead=921m --conf spark.dynamicAllocation.enabled=false --conf spark.sql.adaptive.enabled=true <application jar>
```
#### aws
```console
"Configurations":[
    {
        "Classification": "yarn-site",
        "Properties": {
        "yarn.nodemanager.vmem-check-enabled": "false",
        "yarn.nodemanager.pmem-check-enabled": "false"
        }
    },
    {
        "Classification": "spark",
        "Properties": {
            "maximizeResourceAllocation": "false"
        }
    },
    {
        "Classification": "spark-defaults",
        "Properties": {
            "spark.dynamicAllocation.enabled": "false",
            "spark.sql.adaptive.enabled": "true",
            "spark.driver.memory": "9G",
            "spark.executor.memory": "9G",
            "spark.executor.cores": "5",
            "spark.executor.instances": "14",
            "spark.executor.memoryOverhead": "921M",
            "spark.driver.memoryOverhead": "921M",
            "spark.memory.fraction": "0.80",
            "spark.executor.extraJavaOptions": "-XX:+UseG1GC -XX:+UnlockDiagnosticVMOptions -XX:+G1SummarizeConcMark -XX:InitiatingHeapOccupancyPercent=35 -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:OnOutOfMemoryError='kill -9 %p'",
            "spark.driver.extraJavaOptions": "-XX:+UseG1GC -XX:+UnlockDiagnosticVMOptions -XX:+G1SummarizeConcMark -XX:InitiatingHeapOccupancyPercent=35 -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:OnOutOfMemoryError='kill -9 %p'",
            "spark.yarn.scheduler.reporterThread.maxFailures": "5",
            "spark.storage.level": "MEMORY_AND_DISK_SER",
            "spark.rdd.compress": "true",
            "spark.shuffle.compress": "true",
            "spark.shuffle.spill.compress": "true",
            "spark.default.parallelism": "140"
        }
    },
    {
        "Classification": "mapred-site",
        "Properties": {
            "mapreduce.map.output.compress": "true"
        }
    },
    {
        "Classification": "hadoop-env",
        "Configurations": [{
            "Classification": "export",
            "Configurations": [],
            "Properties": {
                "JAVA_HOME": "/usr/lib/jvm/java-1.8.0"
            }
        }],
        "Properties": {}
    },
    {
        "Classification": "spark-env",
        "Configurations": [{
            "Classification": "export",
            "Properties": {
                "JAVA_HOME": "/usr/lib/jvm/java-1.8.0"
            }
        }],
        "Properties": {}
    }
]
```

<p> </br>
<img width="989" alt="Ekran Resmi 2022-05-13 17 46 28" src="https://user-images.githubusercontent.com/91700155/168308801-85ed2d6e-5cab-4992-9a4b-86564c3f1fdc.png">
                                  

| **RECOMMENDED CONFIGURATION**                                                                                                                                                                                             |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Though the following parameters are not required but they can help in running the applications smoothly to avoid timeout and memory-related errors. We advise that you set these in the spark-defaults configuration file. <p> </br> spark.memory.fraction : Fraction of JVM heap space used for Spark execution and storage. <p> </br> spark.scheduler.barrier.maxConcurrentTasksCheck.maxFailures : Maximum number executor failures allowed before YARN can fail the application. <p> </br> spark.rdd.compress : Whether to compress serialized RDD partitions. <p> </br> spark.shuffle.compress : Whether to compress the map output. <p> </br> spark.shuffle.spill.compress : Whether to compress the data spilled during shuffles. <p> </br> spark.serializer : Class to use for serializing objects that will be sent over the network or need to be cached in serialized form. <p> </br> spark.executor.extraJavaOptions : A string of extra JVM options to pass to executors. <p> </br> spark.driver.extraJavaOptions : A string of extra JVM options to pass to the driver. |



<p> </br>
<p> </br>

Thank you :)

