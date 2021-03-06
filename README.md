# runnable-jar maven tile

Maven tile that uses the spring boot loader maven plugin to create a runnable jar.
There is no need to use Spring DI etc we just need a `main method`. 


## Example use

### 1. Main method
You need a class with a `main` method.

```java
package org.example.main;

public class Hello {

  public static void main(String[] args) {

    System.out.println("Hello world");
  }
}
```


### 2. main-class property
In your project pom add a `main-class` property that specifies the class with the `main` method.

```xml
  <properties>
    <main-class>org.example.main.Hello</main-class>
  </properties>
```

### 3. Add runnable-jar tile
In your project pom under build / plugins add the tiles-maven-plugin with the `runnable-jar` tile like below.

Note that you will may often add multiple tiles as desired/needed (like java-compile, dependency-tree etc)

```xml
      <!-- maven build / plugins -->

      <plugin>
        <groupId>io.repaint.maven</groupId>
        <artifactId>tiles-maven-plugin</artifactId>
        <version>2.8</version>
        <extensions>true</extensions>
        <configuration>
          <tiles>
            <!-- ADD the runnable-jar Tile -->
            <tile>org.avaje.tile:runnable-jar:1.1</tile>

            <!-- Add some other tiles if you like/need -->
            <tile>org.avaje.tile:java-compile:1.1</tile>
            <tile>org.avaje.tile:kotlin-compile:1.1</tile>
            <tile>org.avaje.tile:dependency-tree:1.1</tile>
            <tile>org.avaje.tile:pygments-doclet:1.1</tile>
          </tiles>
        </configuration>
      </plugin>

```

### 4. mvn clean package
Package up the project as a runnable jar. Any dependencies are embedded into the jar (under a *lib* path).

```sh
mvn clean package
```

### 5. run your jar
Uses java -jar to run the application.
```sh
java -jar target/myapp-1.1-SNAPSHOT.jar
```

## What it does

Effectively this tile brings in the *spring-boot-maven-plugin* with configuration the *main* method.
When you go `mvn clean package` this will package up a *jar* with the manifest refering to the
*main* class and embedded dependent jars into a *lib* path inside the jar.

Spring boot loader contains the necessary class loader to combine the jars contained in the *lib* path
along with the normal *.class* files etc.

#### the tile effectively brings in ...

```xml

  <!-- defaults, override in your project pom if needed -->

  <properties>
    <main-class>YouNeedToSpecifyMainClassProperty</main-class>
    <spring-boot-maven-plugin.version>1.3.3.RELEASE</spring-boot-maven-plugin.version>
  </properties>


  <!-- brought into build / plugins -->

  <build>
    <plugins>

      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>${spring-boot-maven-plugin.version}</version>
        <configuration>
          <mainClass>${main-class}</mainClass>
          <layout>ZIP</layout>
        </configuration>
        <executions>
          <execution>
            <goals>
              <goal>repackage</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

    </plugins>

  </build>

```
