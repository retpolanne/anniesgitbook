# Configuring Hadoop YARN for containers



* Example command:

```bash
spark-shell --master yarn --conf spark.executorEnv.YARN_CONTAINER_RUNTIME_TYPE=docker \
    --conf spark.executorEnv.YARN_CONTAINER_RUNTIME_DOCKER_IMAGE=hadoop-docker \
    --conf spark.yarn.appMasterEnv.YARN_CONTAINER_RUNTIME_DOCKER_IMAGE=hadoop-docker \
    --conf spark.yarn.appMasterEnv.YARN_CONTAINER_RUNTIME_TYPE=docker \
    --conf spark.log.level=DEBUG
```

<pre class="language-bash"><code class="lang-bash"><strong>cat &#x3C;&#x3C;EOF> main.py
</strong>import sys
print("Python version")
print (sys.version)
<strong>EOF
</strong><strong>
</strong><strong>spark-submit --master yarn --deploy-mode cluster \
</strong>    --conf spark.executorEnv.YARN_CONTAINER_RUNTIME_TYPE=docker \
    --conf spark.executorEnv.YARN_CONTAINER_RUNTIME_DOCKER_IMAGE=apache/spark-py \
    --conf spark.yarn.appMasterEnv.YARN_CONTAINER_RUNTIME_TYPE=docker \
    --conf spark.yarn.appMasterEnv.YARN_CONTAINER_RUNTIME_DOCKER_IMAGE=apache/spark-py \
    --num-executors 2 main.py -v
</code></pre>

* `hdfs getconf` allows us to check the applied yarn configurations. \[2]
*   In YARN client mode, the driver runs in the submission client’s JVM on the gateway machine. Spark client mode is typically used through Spark-shell.

    The YARN application is submitted as part of the SparkContext initialization at the driver. In YARN Client mode the ApplicationMaster is a proxy for forwarding YARN allocation requests, container status, etc., from and to the driver.

    In this mode, the Spark driver runs on the gateway hosts as a java process, and not in a YARN container. Hence, specifying any driver-specific YARN configuration to use Docker or Docker images will not take effect. Only Spark executors will run in Docker containers. \[4]
* In the "classic" distributed application YARN cluster mode, a user submits a Spark job to be executed, which is scheduled and executed by YARN. The ApplicationMaster hosts the Spark driver, which is launched on the cluster in a Docker container. \[4]

## Improvement opportunities

* [https://issues.apache.org/jira/browse/SPARK-29474](https://issues.apache.org/jira/browse/SPARK-29474)
*

## References

\[1] [https://hadoop.apache.org/docs/r3.1.1/hadoop-yarn/hadoop-yarn-site/DockerContainers.html](https://hadoop.apache.org/docs/r3.1.1/hadoop-yarn/hadoop-yarn-site/DockerContainers.html)

\[2] [https://stackoverflow.com/questions/34666416/get-a-yarn-configuration-from-commandline](https://stackoverflow.com/questions/34666416/get-a-yarn-configuration-from-commandline)

\[3] [https://blog.cloudera.com/introducing-apache-spark-on-docker-on-top-of-apache-yarn-with-cdp-datacenter-release/](https://blog.cloudera.com/introducing-apache-spark-on-docker-on-top-of-apache-yarn-with-cdp-datacenter-release/)

\[4] [https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.4/running-spark-applications/content/running\_spark\_in\_docker\_containers\_on\_yarn.html](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.4/running-spark-applications/content/running\_spark\_in\_docker\_containers\_on\_yarn.html)
