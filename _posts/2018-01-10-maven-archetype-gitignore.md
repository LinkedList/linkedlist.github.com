---
layout: post
title: "Maven archetype .gitignore missing bug"
description: "Fix for maven archetype not copying .gitignore into generated project"
category: 
tags: [maven, archetype, generate, gitignore, .gitignore, bug, fix]
---
While creating a maven archetype for a project skeleton, I encountered a weird bug with .gitignore that was not being copied into the generated project even though
it was specifically included in the corresponding `fileSet` in `archetype-metadata.xml`:

~~~xml
<fileSet encoding="UTF-8">
    <directory></directory>
    <includes>
        <include>.gitignore</include>
        <include>.editorconfig</include>
        <include>mvnw.cmd</include>
        <include>mvnw</include>
    </includes>
</fileSet>
~~~

Here `.editorconfig` and both `mvnw` scripts were being copied into the new project, but `.gitignore` was not

It turns out that there is a list of default excludes in `plexus-utils` used internally that also includes the `.gitignore` file (For specific discussion about this see [MRESOURCES-190](https://issues.apache.org/jira/browse/MRESOURCES-190)). 
The fix is simple but not obvious and somewhat hard to google. Simply add `maven-resources-plugin` with `addDefaultExcludes` configuration set to `false` to your archetype `pom.xml` and the problem should be fixed:

~~~xml
<build>
  <pluginManagement>
    <plugins>
      <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
          <configuration>
              <!-- Required so that .gitignore gets included in archetypes -->
              <!-- see https://issues.apache.org/jira/browse/MRESOURCES-190 -->
              <addDefaultExcludes>false</addDefaultExcludes>
          </configuration>
      </plugin>
    </plugins>
  </pluginManagement>
</build>
~~~
