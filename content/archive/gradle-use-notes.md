---
title: 'Gradle use notes'
toc: true
date: "2015-12-08T20:59:25+00:00"
---

Gradle is so powerful!

This is the comment after I read the tutorial of Gradle.

# Gradle configuration
There're mainly 3 configuration files:
- `build.gradle`: this is where you write your gradle script. Gradle script is written using Groovy.
- `gradle.properties`: you set some variables that can be used in your script
- `gradle.setting`: like `project.name` settings

# Gradle terms
- project: an abstraction of a build. you can use multiple projects.
- task: this is like the `target` in `Ant`.

# Simple use example


```groovy
group 'gradle'
version '1.0-SNAPSHOT'

apply plugin: 'java'

sourceCompatibility = 1.5

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.11'
}

task hello {
    group = "mypersonal"
    description = "this is a helloworld task"
}

hello << {
    print name
}
```


Explanations:
* `group` and `version` represent the group and version of the build distribution.
* `apply plugin: 'java'` means you use `java` plugin.
* `repositories` and `dependencies` sections are for dependency management. Here we use the `Maven`'s repository.
* `task` method defines my own task named `hello`, and I set its properties: `group` and `description`. The following line we use `<<` operator to add the closure to the end of action list. It's equivalent to `doLast`. Of course, there's also a method named `doFirst`. You can add new closure as an action to the list.

That's it. Easy, isn't it?

# Plugin
Gradle use plugin mechanism to extend its build model. If you don't use plugins, you cannot do somethings which really make sense.

Some commonly-used plugins are:
- `java`: java related build. Generate a JAR at last.
- `war`: obviously this is for packaging a webapp.

Actually, these plugins provide all sorts of tasks which are related to some goals.

If you don't use any plugin at all, you can still use some common tasks:


```txt
Build Setup tasks
-----------------
init - Initializes a new Gradle build. [incubating]
wrapper - Generates Gradle wrapper files. [incubating]

Help tasks
----------
components - Displays the components produced by root project ''. [incubating]
dependencies - Displays all dependencies declared in root project ''.
dependencyInsight - Displays the insight into a specific dependency in root project 'Richard'.
help - Displays a help message.
model - Displays the configuration model of root project ''. [incubating]
projects - Displays the sub-projects of root project ''.
properties - Displays the properties of root project ''.
tasks - Displays the tasks runnable from root project ''.
```




