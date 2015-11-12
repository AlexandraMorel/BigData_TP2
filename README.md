# BigData_TP2

### Generation of Maven project
```
mvn archetype:generate -DgroupId=com.bigdata.tp2 -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
mvn eclipse:eclipse
```

We change the POM.xml file to add some dependencies to HBase server and client and to have acces to jdk.tools.
```
<dependency>
  <groupId>jdk.tools</groupId>
  <artifactId>jdk.tools</artifactId>
  <version>1.7.0</version>
  <scope>system</scope>
  <systemPath>${env.JAVA_HOME}/lib/tools.jar</systemPath>
</dependency>
<dependency>
  <groupId>org.apache.hbase</groupId>
  <artifactId>hbase-server</artifactId>
  <version>0.98.9-hadoop2</version>
</dependency>
<dependency>
  <groupId>org.apache.hbase</groupId>
  <artifactId>hbase-client</artifactId>
  <version>0.98.4-hadoop2</version>
</dependency>
```

We add this part to create a ressource `conf/hbase-site.xml` which contains configuration information for HBase.  
It also configures Maven Compiler Plugin and Maven Shade Plugin.
```
<build>
  <sourceDirectory>src</sourceDirectory>
  <resources>
    <resource>
      <directory>${basedir}/conf</directory>
      <filtering>false</filtering>
      <includes>
        <include>hbase-site.xml</include>
      </includes>
    </resource>
  </resources>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
      <configuration>
        <source>1.6</source>
        <target>1.6</target>
      </configuration>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shade-plugin</artifactId>
      <version>2.3</version>
      <configuration>
        <transformers>
          <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
          </transformer>
        </transformers>
      </configuration>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

On a new repository `conf` we create a xml file to have HBase configuration into the Java project directory.
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<property>
	<name>hbase.rootdir</name>
	<value>file:///home/hduser/hbase</value>
	</property>
</configuration>
```

We redo the command:
```
mvn eclipse:eclipse
```

### Create table MusicLibrary
```
kinit hbase@HADOOP.RYBA
hbase > shell
hbase > create table 'MusicLibrary', 'artist', 'song', 'album'
```

`artist` is one of the family column which contains informations about the author of the song (singer's name, producter's name).  
`song` is also a family column, it gives details about the song (name, path, user's mark).  
`album` is the last family column with informations about the album (name, path of the cover, number of songs).  

### Management of data 

We program ManageHBase.java which allows to create a new record and delete one with its key.
