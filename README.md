# sbt-kotlin
(a fork of [kotlin-plugin](https://github.com/pfn/kotlin-plugin) for sbt 1.x)

Build kotlin code using sbt

## Usage

* grab sbt 1.x
* Append to `project/plugins.sbt`:
  ``` sbt
  resolvers += Resolver.bintrayIvyRepo("aki-ks", "sbt-plugins")
  addSbtPlugin("me.aki.sbt" %% "sbt-kotlin" % "1.0.9")
  ```
* Kotlin code will build automatically from `src/XXX/kotlin`
* If necessary, add `kotlinLib("stdlib")`, it is not included by default.
  * Loading standard kotlin libraries and plugins: use `kotlinLib(NAME)` as
    above to load standard kotlin modules provided by JetBrains. For JetBrains
    kotlin compiler plugins, use `kotlinPlugin(NAME)` (e.g.
    `kotlinPlugin("android-extensions")`). The difference is that the latter
    marks the module as a `compile-internal` dependency and will be excluded
    from the final build product.
  * Any other libraries can be loaded using the normal `libraryDependencies`
    mechanism. Compiler plugins should be added as a normal `libraryDependency`
    but specified to be `% "compile-internal"`
* If a non-standard Classpath key needs to be added to the kotlin compile step,
  it can be added using the `kotlinClasspath(KEY)` function
  * For example, to compile with the android platform using `android-sdk-plugin`:
    `kotlinClasspath(Compile, bootClasspath in Android)`

## Options

* `kotlincPluginOptions`: specifies options to pass to kotlin compiler plugins.
  Use `val plugin = KotlinPluginOptions(PLUGINID)` and
  `plugin.option(KEY, VALUE)` to populate this setting
* `kotlinSource`: specifies kotlin source directory, defaults to
  `src/main/kotlin` and `src/test/kotlin`
* `kotlinVersion`: specifies versions of kotlin compiler and libraries to use,
   defaults to `1.2.41`
* `kotlinLib(LIB)`: load a standard kotlin library, for example
  `kotlinLib("stdlib")`; the library will utilize the version specified in
  `kotlinVersion`
  plugin
* `kotlincOptions`: options to pass to the kotlin compiler

### Limitations

* kotlin compilation runs outside of the normal sbt compilation infrastructure.
  As a result, sbt is unaware of classes generated during kotlin compilation.
  `run` and `test` will not work automatically since kotlin classes will not
  have been discovered. `runMain <main-class>` can be used in place of `run`.
  `testOnly <test-class>` may be able to test individual classes/suites in
  place of `test`. Any other tasks which rely on automatically detecting
  classes will fail similarly.
* currently requires kotlin 1.1.4+
