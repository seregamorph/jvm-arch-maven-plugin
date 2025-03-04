
[![Maven Central Version](https://img.shields.io/maven-central/v/com.github.seregamorph/jvm-arch-maven-plugin?style=flat-square)](https://central.sonatype.com/artifact/com.github.seregamorph/jvm-arch-maven-plugin/overview)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

# CPU architecture validation plugin
Checks if the JVM is started for the real CPU architecture to avoid performance overhead of running Rosetta.

## How it works (macOS X)
If the JVM architecture is `"x86_64"` and the OS is `"Mac OS X"`, the plugin executes the following command:
```shell
sysctl -n machdep.cpu.brand_string
``` 
to ensure if it's a Rosetta ~~emulation~~ translation or not.

## How it works (Windows)
If the JVM architecture is `"amd64"` and the OS is `"Windows 11"`, the plugin checks the `PROCESSOR_IDENTIFIER`
environment variable to ensure if it's a CPU emulation or not.

## Usage
Add to the root pom.xml:
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.github.seregamorph</groupId>
            <artifactId>jvm-arch-maven-plugin</artifactId>
            <version>0.2</version>
            <inherited>false</inherited>
            <executions>
                <execution>
                    <id>jvm-arch</id>
                    <goals>
                        <goal>jvm-arch</goal>
                    </goals>
                    <configuration>
                        <!-- Can be WARN or FAIL -->
                        <policy>FAIL</policy>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

Supported operating systems:
* Windows (both Intel- and ARM-based)
* macOS (both Intel- and ARM-based)

For other operating systems like Linux this plugin is just no-op.

Sample failure:
```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  4.015 s (Wall Clock)
[INFO] Finished at: 2024-12-14T18:01:12+01:00
[INFO] ------------------------------------------------------------------------
[INFO] 3 goals, 3 executed
[ERROR] Failed to execute goal com.github.seregamorph:jvm-arch-maven-plugin:0.2:jvm-arch 
(jvm-arch) on project server: The Maven is started on macOS x64-based JVM
/Users/user/Java/amazon-corretto-17-x64.jdk/Contents/Home but the real CPU is 
'Apple M3 Pro'. To avoid emulation performance overhead, please use the proper JVM for 
Apple Silicon (aarch64).
```

Hint: to skip this validation, use `-DskipJvmArch=true` option.
