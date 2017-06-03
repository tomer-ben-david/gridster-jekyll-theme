---
layout: post
title:  "Hadoop job with scalding on docker"
date:   2015-01-08 22:18:00
comments: true
categories: scala,functional-programming,hadoop
---
Explaining how to run docker with hadoop and then run a sample scalding hadoop job on it.

you must compile your code with:

```scala
-Xmax-classfile-name 200
```

otherwise you will get this error [docker only supports up to 242 byte long file names](http://stackoverflow.com/questions/23094365/scala-filename-too-long):

```scala
bash-4.1# bin/hadoop jar /home/myjob.jar com.twitter.scalding.Tool com.myjob.SimpleScaldingJob --local --input input.txt --output output.txt
Exception in thread "main" java.io.FileNotFoundException: /tmp/hadoop-unjar3786039967272100280/com/twitter/bijection/GeneratedTupleCollectionInjections$$anon$31$$anonfun$invert$10$$anonfun$apply$46$$anonfun$apply$47$$anonfun$apply$48$$anonfun$apply$49$$anonfun$apply$50$$anonfun$apply$51$$anonfun$apply$52$$anonfun$apply$53$$anonfun$apply$54$$anonfun$apply$55.class (File name too long)
	at java.io.FileOutputStream.open(Native Method)
	at java.io.FileOutputStream.<init>(FileOutputStream.java:221)
	at java.io.FileOutputStream.<init>(FileOutputStream.java:171)
	at org.apache.hadoop.util.RunJar.unJar(RunJar.java:105)
	at org.apache.hadoop.util.RunJar.unJar(RunJar.java:81)
	at org.apache.hadoop.util.RunJar.run(RunJar.java:209)
	at org.apache.hadoop.util.RunJar.main(RunJar.java:136)
```

so to add the above `scala compiler parameters` your `maven-scala-plugin` plugin should look as following:

```xml
<plugin>
    <groupId>org.scala-tools</groupId>
    <artifactId>maven-scala-plugin</artifactId>
    <version>2.15.2</version>

    <executions>
        <execution>
            <id>scala-compile-first</id>
            <phase>process-resources</phase>
            <goals>
                <goal>add-source</goal>
                <goal>compile</goal>
            </goals>
            <configuration>
                <args>
                    <arg>-make:transitive</arg>
                    <arg>-dependencyfile</arg>
                    <arg>${project.build.directory}/.scala_dependencies</arg>
                    <arg>-Xmax-classfile-name</arg>
                    <arg>200</arg>
                </args>
            </configuration>
        </execution>
        <execution>
            <id>scala-test-compile</id>
            <phase>process-test-resources</phase>
            <goals>
                <goal>testCompile</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

add to your maven build plugin to pack all jars (jar with dependencies):

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>2.4</version>
            <configuration>
                <archive>
                    <manifest>
                        <!-- using main class in the MANIFEST was removed to enable user to choose what main to run
                             to support running the tester with hooks main
                        <addClasspath>true</addClasspath>
                        <mainClass>${main}</mainClass>
                        -->
                    </manifest>
                </archive>
            </configuration>
            <executions>
                <execution>
                    <id>jar-with-libs</id>
                    <goals>
                        <goal>single</goal>
                    </goals>
                    <phase>package</phase>
                    <configuration>
                        <finalName>my-hadoop-job</finalName>
                        <appendAssemblyId>false</appendAssemblyId>
                        <attach>false</attach>
                        <descriptor>src/main/assembly/assembly-jar-with-libs.xml</descriptor>
                    </configuration>
                </execution>
                <!--<execution>-->
                <!--<id>assembly-zip</id>-->
                <!--<goals>-->
                <!--<goal>single</goal>-->
                <!--</goals>-->
                <!--<phase>package</phase>-->
                <!--<configuration>-->
                <!--<finalName>pt-data-scripts-${project.version}</finalName>-->
                <!--<attach>false</attach>-->
                <!--<appendAssemblyId>false</appendAssemblyId>-->
                <!--<descriptor>src/main/assembly/assembly-descriptor.xml</descriptor>-->
                <!--</configuration>-->
                <!--</execution>-->
            </executions>
        </plugin>

    </plugins>
</build>
```

add the `assembly-descriptor.xml` file:

```xml
<assembly
        xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
    <id>job</id>
    <formats>
        <format>jar</format>
    </formats>
    <includeBaseDirectory>false</includeBaseDirectory>
    <dependencySets>
        <dependencySet>
            <unpack>true</unpack>
            <scope>runtime</scope>
            <outputDirectory>/</outputDirectory>
            <unpackOptions>
                <excludes>
                    <exclude>**/NOTICE*</exclude>
                    <exclude>**/LICENSE*</exclude>
                    <exclude>**/DEPENDENCIES*</exclude>
                    <exclude>META-INF/**</exclude>
                    <!-- Exclude folders - this removes "skipping" messages -->
                    <!--<exclude>%regex[.*/]</exclude>-->
                </excludes>
            </unpackOptions>
            <excludes>
                <exclude>${artifact.groupId}:${artifact.artifactId}</exclude>
            </excludes>
        </dependencySet>
    </dependencySets>
    <fileSets>
        <fileSet>
            <directory>${basedir}/target/classes</directory>
            <outputDirectory>/</outputDirectory>
            <excludes>
                <exclude>*.jar</exclude>
            </excludes>
        </fileSet>
    </fileSets>
</assembly>
```

run docker:

```bash
docker run -i -t sequenceiq/hadoop-docker:2.6.0 /etc/bootstrap.sh -bash
```

copy your jar with dependencies into docker (from your parent host):

```bash
sudo cp myjob.jar /var/lib/docker/aufs/mnt/6cee084649a49fcb5d1943faec26b2f6e615a5322521684fb480bff0e1394c32/home/
```

finally run the hadoop job:

```bash
bin/hadoop jar /home/myjob.jar com.twitter.scalding.Tool com.myjob.SimpleScaldingJob --local --input input.txt --output output.txt
```