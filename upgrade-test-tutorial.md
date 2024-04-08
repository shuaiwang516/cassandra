# Guidance to run the dtest on MacOS

By only following the guidance on the Cassandra testing webpage can't successfully pass the dtests.\
This guidance provides several steps to run those dtests on MacOS.

## 1. Null pointer in Versions.java

The problem is [here](https://dba.stackexchange.com/questions/338343/null-pointer-exception-when-running-cassandra-jvm-distributed-upgrade-tests-lo).

```java
[junit-timeout] Testcase: simpleUpgradeWithNetworkAndGossipTest(org.apache.cassandra.distributed.upgrade.UpgradeTest):  Caused an ERROR
[junit-timeout] null
[junit-timeout] java.lang.NullPointerException
[junit-timeout]     at org.apache.cassandra.distributed.shared.Versions.getLatest(Versions.java:127)
[junit-timeout]     at org.apache.cassandra.distributed.upgrade.UpgradeTestBase$TestCase.lambda$upgrades$1(UpgradeTestBase.java:152)
[junit-timeout]     at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:183)
[junit-timeout]     at java.base/java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:177)
[junit-timeout]     at java.base/java.util.Spliterators$ArraySpliterator.forEachRemaining(Spliterators.java:948)
[junit-timeout]     at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:484)
[junit-timeout]     at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:474)
```

The problem is due to the inconsistent version between the hardcode version in `UpgradeTestBase.java` and the version searched from `build/`.

The quick fix is to remove the hardcoded versions in `UpgradeTestBase.java` in this [commit](https://github.com/shuaiwang516/cassandra/commit/d280bfba7a643ef18e5f837f52b283d0559f61d0), but a correct fix should checking the null pointer in `Versions.java` when calling `getLatest()`.


## 2. Connection refused.

The upgrade tests would also use IP address such as `127.0.0.1`, `127.0.0.2` and so on.\
Without manual setting, MacOS would only allow `127.0.0.1` to be connected.

Call the following command to add other IP addresses.
```bash
$ sudo ifconfig lo0 alias 127.0.0.X up # replace X with the actual number.
```
