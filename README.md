# Maven metadata double deploy

This causes problems like https://issues.apache.org/jira/browse/MDEPLOY-221

Problem:
Maven deploys version metadata multiple times. Seems it is triggered by any
plugin that attaches artifact to MavenProject?

Expected:
Maven deploy version (and any other) metadata only once with proper timestmaps
(those that have the deployed artifacts) whatever count of attached 
artifacts are there.

Reported as:
https://issues.apache.org/jira/browse/MNG-6581

## Reproducing

Check out this git repository and run:

```
mvn clean deploy -DaltDeploymentRepository=localhost-nexus::default::http://localhost:8081/content/repositories/snapshots/
```

(basically just deploy it somewhere).

Output with Maven 3.6.0, that clearly shows metadata uploaded twice (just as explained in MDEPLOY-221):

```
cstamas@Urnebes ~/tmp/mvn-md-bug  (master)$ mvn -V clean deploy -DaltDeploymentRepository=localhost-nexus::default::http://localhost:8081/content/repositories/snapshots/
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-24T20:41:47+02:00)
Maven home: /home/linuxbrew/.linuxbrew/Cellar/maven/3.6.0/libexec
Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-8-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-43-generic", arch: "amd64", family: "unix"
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------------< org.test:site >----------------------------
[INFO] Building Maven 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.0.0:clean (default-clean) @ site ---
[INFO] Deleting /home/cstamas/tmp/mvn-md-bug/target
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ site ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/cstamas/tmp/mvn-md-bug/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ site ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ site ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/cstamas/tmp/mvn-md-bug/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ site ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ site ---
[INFO] No tests to run.
[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ site ---
[WARNING] JAR will be empty - no content was marked for inclusion!
[INFO] Building jar: /home/cstamas/tmp/mvn-md-bug/target/site-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-assembly-plugin:2.2-beta-5:single (bundle) @ site ---
[INFO] Reading assembly descriptor: /home/cstamas/tmp/mvn-md-bug/src/main/assembly/bundle.xml
[INFO] Building zip: /home/cstamas/tmp/mvn-md-bug/target/site-1.0-SNAPSHOT-bundle.zip
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ site ---
[INFO] Installing /home/cstamas/tmp/mvn-md-bug/target/site-1.0-SNAPSHOT.jar to /home/cstamas/.m2/repository-wlabs/org/test/site/1.0-SNAPSHOT/site-1.0-SNAPSHOT.jar
[INFO] Installing /home/cstamas/tmp/mvn-md-bug/pom.xml to /home/cstamas/.m2/repository-wlabs/org/test/site/1.0-SNAPSHOT/site-1.0-SNAPSHOT.pom
[INFO] Installing /home/cstamas/tmp/mvn-md-bug/target/site-1.0-SNAPSHOT-bundle.zip to /home/cstamas/.m2/repository-wlabs/org/test/site/1.0-SNAPSHOT/site-1.0-SNAPSHOT-bundle.zip
[INFO] 
[INFO] --- maven-deploy-plugin:2.7:deploy (default-deploy) @ site ---
[INFO] Using alternate deployment repository localhost-nexus::default::http://localhost:8081/content/repositories/snapshots/
Downloading from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml
Downloaded from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 23 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13.jar
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13.jar (2.0 kB at 66 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13.pom
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13.pom (3.0 kB at 114 kB/s)
Downloading from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml
Downloaded from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml (270 B at 21 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 57 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml (270 B at 9.6 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13-bundle.zip
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.133013-13-bundle.zip (37 kB at 876 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 41 kB/s)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.383 s
[INFO] Finished at: 2019-01-28T14:30:13+01:00
[INFO] ------------------------------------------------------------------------
cstamas@Urnebes ~/tmp/mvn-md-bug  (master *)$ 
```
