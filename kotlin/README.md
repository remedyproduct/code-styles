## Installation

> Skip all the way to the "Integration" section if you don't plan to use `ktlint`'s command line interface.

```sh
curl -sSLO https://github.com/pinterest/ktlint/releases/download/0.39.0/ktlint &&
  chmod a+x ktlint &&
  sudo mv ktlint /usr/local/bin/
```

... or just download `ktlint` from the [releases](https://github.com/pinterest/ktlint/releases) page

* `ktlint.asc` contains PGP signature which you can verify with:
  * (Releases up through 0.31.0) `curl -sS https://keybase.io/shyiko/pgp_keys.asc | gpg --import && gpg --verify ktlint.asc`
  * (Releases from 0.32.0 on) `curl -sS https://keybase.io/ktlint/pgp_keys.asc | gpg --import && gpg --verify ktlint.asc`

On macOS ([or Linux](http://linuxbrew.sh/)) you can also use [brew](https://brew.sh/) - `brew install ktlint`.

> If you don't have curl installed - replace `curl -sL` with `wget -qO-`.

> If you are behind a proxy see -
[curl](https://curl.haxx.se/docs/manpage.html#ENVIRONMENT) / 
[wget](https://www.gnu.org/software/wget/manual/wget.html#Proxies) manpage. 
Usually simple `http_proxy=http://proxy-server:port https_proxy=http://proxy-server:port curl -sL ...` is enough. 

## Usage

```bash
# check the style of all Kotlin files inside the current dir (recursively)
# (hidden folders will be skipped)
$ ktlint --color [--color-name="RED"]
  src/main/kotlin/Main.kt:10:10: Unused import
  
# check only certain locations (prepend ! to negate the pattern,
# Ktlint uses .gitignore pattern style syntax)
$ ktlint "src/**/*.kt" "!src/**/*Test.kt"

# auto-correct style violations
# (if some errors cannot be fixed automatically they will be printed to stderr) 
$ ktlint -F "src/**/*.kt"

# print style violations grouped by file
$ ktlint --reporter=plain?group_by_file
# print style violations as usual + create report in checkstyle format 
$ ktlint --reporter=plain --reporter=checkstyle,output=ktlint-report-in-checkstyle-format.xml

# install git hook to automatically check files for style violations on commit
# Run "ktlint installGitPrePushHook" if you wish to run ktlint on push instead
$ ktlint installGitPreCommitHook
```

> on Windows you'll have to use `java -jar ktlint ...`. 

`ktlint --help` for more.

### Integration 

#### ... with [Gradle](https://gradle.org/)

#### (with a plugin - Recommended)

Gradle plugins (in order of appearance):
- [jlleitschuh/ktlint-gradle](https://github.com/jlleitschuh/ktlint-gradle)  
Gradle plugin that automatically creates check and format tasks for project Kotlin sources,
supports different kotlin plugins and Gradle build caching.

- [jeremymailen/kotlinter-gradle](https://github.com/jeremymailen/kotlinter-gradle)  
Gradle plugin featuring incremental build support, file reports, and `*.kts` source support.

You might also want to take a look at [diffplug/spotless](https://github.com/diffplug/spotless/tree/master/plugin-gradle#applying-ktlint-to-kotlin-files) or [autostyle/autostyle](https://github.com/autostyle/autostyle/tree/master/plugin-gradle#applying-ktlint-to-kotlin-files) that have a built-in support for ktlint. In addition to linting/formatting kotlin code it allows you to keep license headers, markdown documentation, etc. in check.

#### (without a plugin)

> build.gradle

```groovy
// kotlin-gradle-plugin must be applied for configuration below to work
// (see https://kotlinlang.org/docs/reference/using-gradle.html)

apply plugin: 'java'

repositories {
    jcenter()
}

configurations {
    ktlint
}

dependencies {
    ktlint "com.pinterest:ktlint:0.39.0"
    // additional 3rd party ruleset(s) can be specified here
    // just add them to the classpath (e.g. ktlint 'groupId:artifactId:version') and 
    // ktlint will pick them up
}

task ktlint(type: JavaExec, group: "verification") {
    description = "Check Kotlin code style."
    classpath = configurations.ktlint
    main = "com.pinterest.ktlint.Main"
    args "src/**/*.kt"
    // to generate report in checkstyle format prepend following args:
    // "--reporter=plain", "--reporter=checkstyle,output=${buildDir}/ktlint.xml"
    // see https://github.com/pinterest/ktlint#usage for more
}
check.dependsOn ktlint

task ktlintFormat(type: JavaExec, group: "formatting") {
    description = "Fix Kotlin code style deviations."
    classpath = configurations.ktlint
    main = "com.pinterest.ktlint.Main"
    args "-F", "src/**/*.kt"
}
```

To check code style - `gradle ktlint` (it's also bound to `gradle check`).  
To run formatter - `gradle ktlintFormat`.

See [Making your Gradle tasks incremental](https://proandroiddev.com/making-your-gradle-tasks-incremental-7f26e4ef09c3) by [Niklas Baudy](https://github.com/vanniktech) on how to make tasks above incremental. 


#### (without a plugin) for Gradle Kotlin DSL (build.gradle.kts)

> build.gradle.kts

```kotlin
val ktlint by configurations.creating

dependencies {
    ktlint("com.pinterest:ktlint:0.39.0")
    // ktlint(project(":custom-ktlint-ruleset")) // in case of custom ruleset
}

val outputDir = "${project.buildDir}/reports/ktlint/"
val inputFiles = project.fileTree(mapOf("dir" to "src", "include" to "**/*.kt"))

val ktlintCheck by tasks.creating(JavaExec::class) {
    inputs.files(inputFiles)
    outputs.dir(outputDir)

    description = "Check Kotlin code style."
    classpath = ktlint
    main = "com.pinterest.ktlint.Main"
    args = listOf("src/**/*.kt")
}

val ktlintFormat by tasks.creating(JavaExec::class) {
    inputs.files(inputFiles)
    outputs.dir(outputDir)

    description = "Fix Kotlin code style deviations."
    classpath = ktlint
    main = "com.pinterest.ktlint.Main"
    args = listOf("-F", "src/**/*.kt")
}
```

#### ... with [IntelliJ IDEA](https://www.jetbrains.com/idea/)

> While this is not strictly necessary it makes Intellij IDEA's built-in formatter produce 100% ktlint-compatible 
 code. 

##### Option #1 (recommended)

> (inside project's root directory)  

```sh
ktlint applyToIDEAProject
# or if you want to be compliant with Android Kotlin Style Guide
ktlint --android applyToIDEAProject
```

##### Option #2

Apply to all IDEA projects:
```sh
ktlint applyToIDEA
```
Or if you want to use android specific code style:
```sh
ktlint --android applyToIDEA
```

##### Option #3

Go to <kbd>File</kbd> -> <kbd>Settings...</kbd> -> <kbd>Editor</kbd>
- <kbd>General</kbd> -> <kbd>Auto Import</kbd>
  - check `Optimize imports on the fly (for current project)`.
- <kbd>Code Style</kbd> -> <kbd>Kotlin</kbd>
  - <kbd>Set from...</kbd> -> <kbd>Predefined style</kbd> -> <kbd>Kotlin style guide</kbd> (Kotlin plugin 1.2.20+).
  - open <kbd>Code Generation</kbd> tab
    - uncheck `Line comment at first column`;
    - select `Add a space at comment start`.
  - open <kbd>Imports</kbd> tab
    - select `Use single name import` (all of them);
    - remove `import java.util.*` from `Packages to Use Import with '*'`.
  - open <kbd>Blank Lines</kbd> tab
    - change `Keep Maximum Blank Lines` / `In declarations` & `In code` to 1 and `Before '}'` to 0.
  - (optional but recommended) open <kbd>Wrapping and Braces</kbd> tab
    - uncheck `Method declaration parameters` / `Align when multiline`.     
  - (optional but recommended) open <kbd>Tabs and Indents</kbd> tab
    - change `Continuation indent` to the same value as `Indent` (4 by default).   
- <kbd>Inspections</kbd> 
  - change `Severity` level of `Unused import directive` and `Redundant semicolon` to `ERROR`.
    

#### Access to the latest `master` snapshot

Whenever a commit is added to the `master` branch a snapshot build is automatically uploaded to [Sonatype's snapshots repository](https://oss.sonatype.org/content/repositories/snapshots/com/pinterest/ktlint/).
If you are eager to try upcoming changes (that might or might not be included in the next stable release) you can do 
so by changing version of ktlint to `<latest-version>-SNAPSHOT` + adding a repo: 

##### Maven

```xml
...
<repository>
    <id>sonatype-snapshots</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots</url>
    <snapshots>
        <enabled>true</enabled>
    </snapshots>
    <releases>
        <enabled>false</enabled>
    </releases>
</repository>
...
```

##### Gradle

```groovy
repositories {
  maven {
    url "https://oss.sonatype.org/content/repositories/snapshots"
  }
}
```

##### Kotlin development version snapshot

Additionally, project publishes snapshots build against latest kotlin development version. To use them, change version
of ktlint to `<latest-version>-kotlin-dev-SNAPSHOT`.

### Documentation
[All docs, faq and more other info](https://ktlint.github.io/)