# Gradle WAR overlay plugin

A plugin for gradle to provide functionality similar to the war overlay function in maven. This plugin is initially
developed to support building CAS servers, but should work in other situations. This plugin enhances the [Gradle WAR plugin](http://www.gradle.org/docs/current/userguide/war_plugin.html).

## Configuration

Import the plugin:

```groovy
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath net.the_deadmans.gradle:0.9.3"
    }
}
apply plugin: 'waroverlay'
```

Alternatively, import the plugin in one line:

```groovy
apply from: "http://dl.bintray.com/scalding/generic/waroverlay.gradle"
```

Add the WAR dependency you want to overlay as a runtime dependency:

```groovy
dependencies {
    runtime("org.jasig.cas:cas-server-webapp:3.5.2@war") {
        transitive = true // setting this to true calculates transitive dependencies for addition to the target WAR
    }
}
```

Most other configuration is handled like the regular [Gradle WAR plugin](http://www.gradle.org/docs/current/userguide/war_plugin.html). There is one added convention:

```groovy
war {
    includeWarJars = false // whether to include the jar files in the WAR. Default is false. If false, it might be useful to set transitive to `true` for the WAR dependency so its dependencies will be calculated and added
}
```

To exclude files from the war besides the war's jars, you can pass in an array of exclusion patterns. 
Note that if the war being overlaid has dependencies and you are trying to exclude a specific jar, you will also need to exclude the transitive dependency of the war via gradle's dependency exclusion mechanism.   
```groovy
war {
    overlayExcludes = [ "**/*.properties" ] // files to exclude from the war being overlaid
}
```

For a sample, see https://github.com/Unicon/unicon-cas-overlay/blob/CAS4/build.gradle

# Troubleshooting

If you include any signed JARs in your WAR, you'll likely encounter signature verification errors at runtime.  To get around this, exclude the signature files from inclusion in your WAR like this:

```groovy
war {
    // ... whatever else you may need
    exclude "META-INF/*.RSA", "META-INF/*.SF", "META-INF/*.DSA"
}
```
