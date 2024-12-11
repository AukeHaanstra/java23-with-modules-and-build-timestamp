# Maven compiler plugin failure with Java 23, Java modules and build timestamp property

## Description

For a multi-module maven project using Java 23 and java modules, the maven compiler plugin throws an error during the
build.
This happens when the maven-compiler plugin encounters the first maven module that contains a module-info.java during 
the build, and only happens when using the property project.build.outputTimestamp (for reproducible builds) in the
(parent) pom.
This only happens when using Java 23 (tested using Oracle JDK 23.0.1); with JDK 21 or 22 the maven build succeeds.

## Reproducer

Reproducer Github project:

https://github.com/AukeHaanstra/java23-with-modules-and-build-timestamp

./mvnw clean compile

The error occurs at least with the following configurations (tested on Ubuntu 22.04.5 LTS):
- Maven 3.9.9 (Maven compiler plugin 3.13.0)
    - Java 23
    - module-info.java in multi-module project
    - project.build.outputTimestamp property
- Maven 4.0.0-rc-1 (Maven compiler plugin 3.13.0)
    - Java 23
    - module-info.java in multi-module project
    - **no** project.build.outputTimestamp

The error disappears when using one of:
- no `project.build.outputTimestamp` property in the parent pom (Maven 3.9.9)
- `<project.build.outputTimestamp>x</project.build.outputTimestamp>` (Maven 4.0.0-rc-1)
- no module-info.java
- Java 21 or 22 instead of Java 23

Using maven-compiler-plugin:4.0.0-beta-1 with maven 4 completely fails for me, but this
[bug](https://issues.apache.org/jira/browse/MCOMPILER-599) has already been reported.

## Relevant documentation

Reproducible builds documentation:

https://maven.apache.org/guides/mini/guide-reproducible-builds.html

## Logs

Full stack trace:

```text
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile (default-compile) on project javamodule: Execution default-compile of goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile failed: Unsupported class file major version 67 -> [Help 1]
org.apache.maven.lifecycle.LifecycleExecutionException: Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile (default-compile) on project javamodule: Execution default-compile of goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile failed: Unsupported class file major version 67
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:333)
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:73)
at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:53)
at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:118)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:261)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
at org.apache.maven.cli.MavenCli.execute (MavenCli.java:906)
at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
at jdk.internal.reflect.DirectMethodHandleAccessor.invoke (DirectMethodHandleAccessor.java:103)
at java.lang.reflect.Method.invoke (Method.java:580)
at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:255)
at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:201)
at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:361)
at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:314)
Caused by: org.apache.maven.plugin.PluginExecutionException: Execution default-compile of goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile failed: Unsupported class file major version 67
at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:133)
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:328)
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:73)
at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:53)
at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:118)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:261)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
at org.apache.maven.cli.MavenCli.execute (MavenCli.java:906)
at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
at jdk.internal.reflect.DirectMethodHandleAccessor.invoke (DirectMethodHandleAccessor.java:103)
at java.lang.reflect.Method.invoke (Method.java:580)
at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:255)
at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:201)
at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:361)
at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:314)
Caused by: java.lang.IllegalArgumentException: Unsupported class file major version 67
at org.objectweb.asm.ClassReader.<init> (ClassReader.java:200)
at org.objectweb.asm.ClassReader.<init> (ClassReader.java:180)
at org.objectweb.asm.ClassReader.<init> (ClassReader.java:166)
at org.apache.maven.plugin.compiler.ModuleInfoTransformer.transform (ModuleInfoTransformer.java:45)
at org.apache.maven.plugin.compiler.AbstractCompilerMojo.patchJdkModuleVersion (AbstractCompilerMojo.java:1899)
at org.apache.maven.plugin.compiler.AbstractCompilerMojo.execute (AbstractCompilerMojo.java:1247)
at org.apache.maven.plugin.compiler.CompilerMojo.execute (CompilerMojo.java:215)
at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:126)
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:328)
at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:73)
at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:53)
at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:118)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:261)
at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
at org.apache.maven.cli.MavenCli.execute (MavenCli.java:906)
at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
at jdk.internal.reflect.DirectMethodHandleAccessor.invoke (DirectMethodHandleAccessor.java:103)
at java.lang.reflect.Method.invoke (Method.java:580)
at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:255)
at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:201)
at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:361)
at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:314)
```