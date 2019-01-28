# Maven metadata double deploy

This causes problems like https://issues.apache.org/jira/browse/MDEPLOY-221

Problem:
Maven deploys version metadata multiple times. Seems it is triggered by any
plugin that attaches artifact to MavenProject?

Expected:
Maven deploy version (and any other) metadata only once with proper timestmaps
(those that have the deployed artifacts) whatever count of attached 
artifacts are there.



## Reproducing

Check out this git repository and run:

```
mvn clean deploy -DaltDeploymentRepository=localhost-nexus::default::http://localhost:8081/content/repositories/snapshots/
```

(basically just deploy it somewhere).

Output with Maven 3.6.0:

```
cstamas@Urnebes ~/tmp/mvn-md-bug $ mvn clean deploy -DaltDeploymentRepository=localhost-nexus::default::http://localhost:8081/content/repositories/snapshots/
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
Downloaded from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 25 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12.jar
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12.jar (2.0 kB at 58 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12.pom
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12.pom (3.0 kB at 103 kB/s)
Downloading from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml
Downloaded from localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml (270 B at 17 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 52 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/maven-metadata.xml (270 B at 12 kB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12-bundle.zip
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/site-1.0-20190128.131815-12-bundle.zip (37 kB at 1.1 MB/s)
Uploading to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml
Uploaded to localhost-nexus: http://localhost:8081/content/repositories/snapshots/org/test/site/1.0-SNAPSHOT/maven-metadata.xml (1.1 kB at 48 kB/s)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.435 s
[INFO] Finished at: 2019-01-28T14:18:15+01:00
[INFO] ------------------------------------------------------------------------
cstamas@Urnebes ~/tmp/mvn-md-bug $
```
