---
title: Apache Maven 2
toc: true
id: 450
categories:
  - 'Building, Testing &amp; CI'
date: "2015-03-14T21:57:13+00:00"
---

Although I have some experience about Maven 2, this is a simple summary.
> <span style="color: #000080;">Maven  /ˈm<span style="color: #008000;">eɪ</span>v_ə_n/</span>

### Artifact Vector

> groupid:artifactid:packaging:version:scope
such as:
> org.springframework:spring:jar:2.5.6:compile
> 
> org.mockito:mockito-core:jar:4.7:test
Maven provides the following standard scope:
<table style="height: 291px;" border="1" width="745">
<tbody>
<tr>
<td>_Scope_</td>
<td>_Description_</td>
</tr>
<tr>
<td>compile</td>
<td>Needed for compilation, included in packages.</td>
</tr>
<tr>
<td>test</td>
<td>Needed for unit tests, not included in packages.</td>
</tr>
<tr>
<td>provided</td>
<td>Needed for compilation, but provided at runtime by the runtime container.</td>
</tr>
<tr>
<td>system</td>
<td>Needed for compilation, given as absolute path on disk, and not included in
packages.</td>
</tr>
<tr>
<td>import</td>
<td>An inline inclusion of a POM-type artifact facilitating dependency-declaring
POM snippets.</td>
</tr>
</tbody>
</table>

### Lifecycles, Phases, Plugins, Goals

![maven-lifecycle](/media/maven-lifecycle.png)
**Lifecycles** represent a well-recognized flw of steps (Phases) used in software assembly.
Each step in a lifecycle flow is called a **phase**. Zero or more plugin goals are bound to a phase.
A **plugin** is a logical grouping and distribution (often a single JAR) of related goals, such as JARing.
A **goal**, the most granular step in Maven, is a single executable task within a plugin. For example, discrete goals in the jar plugin include packaging the jar (jar:jar), signing the jar (jar:sign), and verifying the signature (jar:sign-verify).

### Built-in lifecycles

Maven provides 3 built-in lifecycles: clean, default, site.

The following lists all build phases and its default goals bound to them.

**Clean Lifecycle**
<table class="bodyTable" border="1">
<tbody>
<tr>
<td align="left">**<span style="color: #333333;">Phrase</span>**</td>
<td align="left">**<span style="color: #333333;">Default goal bindings</span>**</td>
<td align="left">**<span style="color: #333333;">Phase description</span>**</td>
</tr>
<tr>
<td align="left"><tt>pre-clean</tt></td>
<td align="left"></td>
<td align="left">executes processes needed prior to the actual project cleaning</td>
</tr>
<tr>
<td align="left"><tt>clean</tt></td>
<td align="left">maven-clean-plugin:clean</td>
<td align="left">remove all files generated by the previous build</td>
</tr>
<tr>
<td align="left"><tt>post-clean</tt></td>
<td align="left"></td>
<td align="left">executes processes needed to finalize the project cleaning</td>
</tr>
</tbody>
</table>
**Default Lifecycle**
<table style="font-size: 0.8em;" border="1">
<tbody>
<tr>
<td rowspan="2" align="left">**<span style="color: #333333;">Phase</span>**</td>
<td style="text-align: center;" colspan="4" align="left">**<span style="color: #333333;">Default goal bindings</span>**</td>
<td rowspan="2" align="left">**<span style="color: #333333;">Phase description</span>**</td>
</tr>
<tr>
<td align="left">**packaging ejb/ejb3/jar/par/rar/war**</td>
<td align="left">**packaging ear**</td>
<td align="left">**packaging maven-plugin**</td>
<td align="left">**packing pom**</td>
</tr>
<tr>
<td align="left"><tt>validate</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">validate the project is correct and all necessary information is available.</td>
</tr>
<tr>
<td align="left"><tt>initialize</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">initialize build state, e.g. set properties or create directories.</td>
</tr>
<tr>
<td align="left"><tt>generate-sources</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">generate any source code for inclusion in compilation.</td>
</tr>
<tr>
<td align="left"><tt>process-sources</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">process the source code, for example to filter any values.</td>
</tr>
<tr>
<td align="left"><tt>generate-resources</tt></td>
<td align="left"></td>
<td align="left">maven-ear-plugin:generate-application-xml</td>
<td align="left">maven-plugin-plugin:descriptor</td>
<td align="left"></td>
<td align="left">generate resources for inclusion in the package.</td>
</tr>
<tr>
<td align="left"><tt>process-resources</tt></td>
<td align="left">maven-resources-plugin:resources</td>
<td align="left">maven-resources-plugin:resources</td>
<td align="left">maven-resources-plugin:resources</td>
<td align="left"></td>
<td align="left">copy and process the resources into the destination directory, ready for packaging.</td>
</tr>
<tr>
<td align="left"><tt>compile</tt></td>
<td align="left">maven-compiler-plugin:compile</td>
<td align="left"></td>
<td align="left">maven-compiler-plugin:compile</td>
<td align="left"></td>
<td align="left">compile the source code of the project.</td>
</tr>
<tr>
<td align="left"><tt>process-classes</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">post-process the generated files from compilation, for example to do bytecode enhancement on Java classes.</td>
</tr>
<tr>
<td align="left"><tt>generate-test-sources</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">generate any test source code for inclusion in compilation.</td>
</tr>
<tr>
<td align="left"><tt>process-test-sources</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">process the test source code, for example to filter any values.</td>
</tr>
<tr>
<td align="left"><tt>generate-test-resources</tt></td>
<td align="left">maven-resources-plugin:testResources</td>
<td align="left"></td>
<td align="left">maven-resources-plugin:testResources</td>
<td align="left"></td>
<td align="left">create resources for testing.</td>
</tr>
<tr>
<td align="left"><tt>process-test-resources</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">copy and process the resources into the test destination directory.</td>
</tr>
<tr>
<td align="left"><tt>test-compile</tt></td>
<td align="left">maven-compiler-plugin:testCompile</td>
<td align="left"></td>
<td align="left">maven-compiler-plugin:testCompile</td>
<td align="left"></td>
<td align="left">compile the test source code into the test destination directory</td>
</tr>
<tr>
<td align="left"><tt>process-test-classes</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">post-process the generated files from test compilation, for example to do bytecode enhancement on Java classes. For Maven 2.0.5 and above.</td>
</tr>
<tr>
<td align="left"><tt>test</tt></td>
<td align="left">maven-surefire-plugin:test</td>
<td align="left"></td>
<td align="left">maven-surefire-plugin:test</td>
<td align="left"></td>
<td align="left">run tests using a suitable unit testing framework. These tests should not require the code be packaged or deployed.</td>
</tr>
<tr>
<td align="left"><tt>prepare-package</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">perform any operations necessary to prepare a package before the actual packaging. This often results in an unpacked, processed version of the package. (Maven 2.1 and above)</td>
</tr>
<tr>
<td align="left"><tt>package</tt></td>
<td align="left">maven-ejb-plugin:ejb /
maven-ejb3-plugin:ejb3 /
maven-jar-plugin:jar /
maven-par-plugin:par /
maven-rar-plugin:rar /
maven-war-plugin:war</td>
<td align="left">maven-ear-plugin:ear</td>
<td align="left">maven-jar-plugin:jar
maven-plugin-plugin:addPluginArtifact
Metadata</td>
<td align="left">maven-site-plugin:attach-descriptor</td>
<td align="left">take the compiled code and package it in its distributable format, such as a JAR.</td>
</tr>
<tr>
<td align="left"><tt>pre-integration-test</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">perform actions required before integration tests are executed. This may involve things such as setting up the required environment.</td>
</tr>
<tr>
<td align="left"><tt>integration-test</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">process and deploy the package if necessary into an environment where integration tests can be run.</td>
</tr>
<tr>
<td align="left"><tt>post-integration-test</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">perform actions required after integration tests have been executed. This may including cleaning up the environment.</td>
</tr>
<tr>
<td align="left"><tt>verify</tt></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">run any checks to verify the package is valid and meets quality criteria.</td>
</tr>
<tr>
<td align="left"><tt>install</tt></td>
<td align="left">maven-install-plugin:install</td>
<td align="left">maven-install-plugin:install</td>
<td align="left">maven-install-plugin:install</td>
<td align="left">maven-install-plugin:install</td>
<td align="left">install the package into the local repository, for use as a dependency in other projects locally.</td>
</tr>
<tr>
<td align="left"><tt>deploy</tt></td>
<td align="left">maven-deploy-plugin:deploy</td>
<td align="left">maven-deploy-plugin:deploy</td>
<td align="left">maven-deploy-plugin:deploy</td>
<td align="left">maven-deploy-plugin:deploy</td>
<td align="left">done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects.</td>
</tr>
</tbody>
</table>
**Site Lifecycle**
<table class="bodyTable" border="1">
<tbody>
<tr class="a">
<td align="left"><span style="color: #333333;">**Phase**</span></td>
<td align="left"><span style="color: #333333;">**Default goal bindings**</span></td>
<td align="left"><span style="color: #333333;">**Phase description**</span></td>
</tr>
<tr class="a">
<td align="left">pre-site</td>
<td align="left"></td>
<td align="left">executes processes needed prior to the actual project site generation</td>
</tr>
<tr class="b">
<td align="left">site</td>
<td align="left">maven-site-plugin:site</td>
<td align="left">generates the project's site documentation</td>
</tr>
<tr class="a">
<td align="left">post-site</td>
<td align="left"></td>
<td align="left">executes processes needed to finalize the site generation, and to prepare for site deployment</td>
</tr>
<tr class="b">
<td align="left">site-deploy</td>
<td align="left">maven-site-plugin:deploy</td>
<td align="left">deploys the generated site documentation to the specified web server</td>
</tr>
</tbody>
</table>

### Project packaging

we set the packaging for your project via the equally named POM element <tt><packaging></tt>. Some of the valid packaging values are <tt>jar</tt>, <tt>war</tt>, <tt>ear</tt> and <tt>pom</tt>. If no packaging value has been specified, it will default to <tt>jar</tt>.

### Phase and goal binding

A goal may be bound to zero or more build phases. A goal not bound to any build phase could be executed outside of the build lifecycle by direct invocation.

Moreover, if a goal is bound to one or more build phases, that goal will be called in all those phases.

Furthermore, a build phase can also have zero or more goals bound to it. If a build phase has no goals bound to it, that build phase will not execute.

Usually, plugin bind its goals on default phases. That's defined on plugin jar's MATE-INF/maven/plugin.xml.

Let's see the plugin.xml of maven-compiler-plugin.jar:


```java
<plugin>
  <mojos>
    <mojo>
      <goal>compile</goal>
      ....
      <phase>compile</phase>
      ....
    </mojo>
    <mojo>
      <goal>help</goal>
      ....
    </mojo>
    <mojo>
      <goal>testCompile</goal>
      ....
      <phase>test-compile</phase>
      ....
    </mojo>
  </mojos>
</plugin>
```

maven-compiler-plugin defines 3 goals, and "compile" and "testCompiler" goals have the default phase, but "help" goal not.

&nbsp;

But a goal can be bound on different phases which can be reconfigured by plugin configuration section.


```java
...
 <plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>process-test-resources</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
     <execution>
       <phase>process-resources</phase>
       <goals>
         <goal>anotherGoal</goal>
         <goal>anotherGoalAgain</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```


#### Default goal

The default goal codifis the author’s intended usage of the build script. Only one goal or lifecycle can be set as the
default.


```java
<project>
 [...]
 <build>
 <defaultGoal>install</defaultGoal>
 </build>
 [...]
</project>
```


### Execute a Phase or Goal

If you ask Maven to run a specific goal, then only that goal is run.

For org.apache.maven:maven-***-plugin, you can run it using the plugin-prefix for short.
> mvn <plugin-prefix>:<goal>
or

For your customized plugins, run it:
> mvn <plugin-group-id>:<plugin-artifact-id>[:<plugin-version>]:<goal>
For example:
> mvn compiler:compile  jar:jar
This example runs two plugin goals: compilation of code, then JARing the result, skipping over any intermediate steps.

mvn <phase-name>

Conversely, if you ask Maven to execute a phase, all phases and bound plugin goals up to that point in the lifecycle are also executed. This example requests the deploy lifecycle phase, which will also execute the verifiation, compilation, testing and packaging phases.
> mvn deploy

### Profiles

Profies are a means to conditionally turn on portions of Maven confiuration, including plugins, pathing and confiuration.
The most common uses of profies are for Windows/Unix platform-specifi variations and build-time customization of JAR dependencies based on the use of a specifi Weblogic, Websphere or JBoss J2EE vendor.


```java
<project>
 ...
 <profiles>
 <profile>
 <id>YourProfile</id>
 ...settings, build, plugins etc...
 <dependencies>
 <dependency>
 <groupId>com.yourcompany</groupId>
 <artifactId>yourlib</artifactId>
 </dependency>
 <dependencies>
 </profile>
 </profiles>
 ...
</project>
```


#### Profile activation

Profiles can be activated manually from the command line or through an activation rule (OS, fie existence, Maven version, etc.).

Profiles are primarily additive, so best practices suggest leaving most off by default, and activating based on specific conditions.

_**Manual Profie Activation**_
> mvn –P YourProfie
_**Automatic Profie Activation**_


```java
<project>
 ...
 <profiles>
 <profile>
 <id>YourProfile</id>
 ...settings, build, etc...
 <activation>
 <os>
 <name>Windows XP</name>
 <family>Windows</family>
 <arch>x86</arch>
 <version>5.1.2600</version>
 </os>
 <fie>
 <missing>somefolder/somefie.txt</missing>
 </fie>
 </activation>
 </profile>
 </profiles>
 ...
</project>
```
