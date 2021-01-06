The Gradle team is excited to announce Gradle @version@.

This release features [1](), [2](), ... [n](), and more.

We would like to thank the following community members for their contributions to this release of Gradle:
<!-- 
Include only their name, impactful features should be called out separately below.
 [Some person](https://github.com/some-person)
-->

[Martin d'Anjou](https://github.com/martinda)
[Till Krullmann](https://github.com/tkrullmann)
[Andreas Axelsson](https://github.com/judgeaxl)
[Pedro Tôrres](https://github.com/t0rr3sp3dr0)

## Upgrade Instructions

Switch your build to use Gradle @version@ by updating your wrapper:

`./gradlew wrapper --gradle-version=@version@`

See the [Gradle 6.x upgrade guide](userguide/upgrading_version_6.html#changes_@baseVersion@) to learn about deprecations, breaking changes and other considerations when upgrading to Gradle @version@. 

For Java, Groovy, Kotlin and Android compatibility, see the [full compatibility notes](userguide/compatibility.html).

<!-- Do not add breaking changes or deprecations here! Add them to the upgrade guide instead. --> 

## Build reliability improvements

Gradle employs a number of optimizations to ensure that builds are executed as fast as possible.
These optimizations rely on the inputs and outputs of tasks to be well-defined.
Gradle already applies some validation to tasks to check whether they are well-defined.

### Disable optimizations for validation problems

If a task is found to be invalid, Gradle will now execute it without the benefit of parallel execution, up-to-date checks and the build cache.
For more information see the [user manual on runtime validation](userguide/more_about_tasks.html#sec:task_input_validation).

### Validate missing dependencies between tasks

One of the potential problems now flagged is a task that consumes the output produced by another without declaring an [explicit or inferred task dependency](userguide/more_about_tasks.html#sec:link_output_dir_to_input_files).
Gradle now detects the missing dependency between the consumer and the producer and emits a warning in that case.
For more information see the [user manual on input and output validation](userguide/more_about_tasks.html#sec:task_input_output_validation). 

## Plugin development improvements

### Included plugin builds

Developing plugins as part of a composite build was so far only possible for project plugins.
Settings plugins always had to be developed in isolation and published to a binary repository.

This release introduces a new DSL construct in the settings file for including plugin builds.
Build included like that can provide both project and settings plugins.
```
pluginManagement {
    includeBuild("../my-settings-plugin")
}
plugins {
    id("my.settings-plugin") 
}
```
The above example assumes that the included build defines a settings plugin with the id `my.settings-plugin`.

Library components produced by builds included though the `pluginManagement` block are not automatically visible to the including build.
However, the same build can be included as plugin build and normal library build:
```
pluginManagement {
    // contributes plugins
    includeBuild("../project-with-plugin-and-library") 
}
// contributes libraries
includeBuild("../project-with-plugin-and-library") 
```
This distinction reflects what Gradle offers for repository declarations - 
repositories are specified separately for plugin dependencies and for production dependencies.

<!-- 

================== TEMPLATE ==============================

<a name="FILL-IN-KEY-AREA"></a>
### FILL-IN-KEY-AREA improvements

<<<FILL IN CONTEXT FOR KEY AREA>>>
Example:
> The [configuration cache](userguide/configuration_cache.html) improves build performance by caching the result of
> the configuration phase. Using the configuration cache, Gradle can skip the configuration phase entirely when
> nothing that affects the build configuration has changed.

#### FILL-IN-FEATURE
> HIGHLIGHT the usecase or existing problem the feature solves
> EXPLAIN how the new release addresses that problem or use case
> PROVIDE a screenshot or snippet illustrating the new feature, if applicable
> LINK to the full documentation for more details 

================== END TEMPLATE ==========================


==========================================================
ADD RELEASE FEATURES BELOW
vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
ADD RELEASE FEATURES ABOVE
==========================================================

-->

## Promoted features
Promoted features are features that were incubating in previous versions of Gradle but are now supported and subject to backwards compatibility.
See the User Manual section on the “[Feature Lifecycle](userguide/feature_lifecycle.html)” for more information.

### Java Module Support

[Compiling](userguide/java_library_plugin.html#sec:java_library_modular),
[testing](userguide/java_testing.html#sec:java_testing_modular) and
[executing](userguide/application_plugin.html#sec:application_modular)
Java modules is now a stable feature.
It is no longer required to activate the functionality using `java.modularity.inferModulePath.set(true)`.

### Promoted APIs
In Gradle 7.0 we moved the following classes or methods out of incubation phase.

- Core
    - [Services](userguide/custom_gradle_types.html#service_injection)
        - org.gradle.api.file.ArchiveOperations
        - org.gradle.api.file.FileSystemOperations
        - org.gradle.process.ExecOperations
    - [Lazy configuration](userguide/lazy_configuration.html)
        - org.gradle.api.model.ObjectFactory.directoryProperty
        - org.gradle.api.model.ObjectFactory.fileCollection
        - org.gradle.api.model.ObjectFactory.fileProperty
        - org.gradle.api.model.ObjectFactory.sourceDirectorySet
        - org.gradle.api.file.FileCollection.getElements
        - org.gradle.api.provider.Provider.flatMap
        - org.gradle.api.provider.Provider.orElse(T)
        - org.gradle.api.provider.Provider.orElse(org.gradle.api.provider.Provider<? extends T>)
        - org.gradle.api.provider.HasConfigurableValue
        - org.gradle.api.provider.Property.value(T)
        - org.gradle.api.provider.Property.value(org.gradle.api.provider.Provider<? extends T>)
        - org.gradle.api.provider.Property.convention(T)
        - org.gradle.api.provider.Property.convention(org.gradle.api.provider.Provider<? extends T>)
        - org.gradle.api.provider.HasMultipleValues.empty
        - org.gradle.api.provider.HasMultipleValues.value(java.lang.Iterable<? extends T>)
        - org.gradle.api.provider.HasMultipleValues.value(org.gradle.api.provider.Provider<? extends java.lang.Iterable<? extends T>>)
        - org.gradle.api.provider.HasMultipleValues.convention(java.lang.Iterable<? extends T>)
        - org.gradle.api.provider.HasMultipleValues.convention(org.gradle.api.provider.Provider<? extends java.lang.Iterable<? extends T>>)
        - org.gradle.api.provider.MapProperty
        - org.gradle.api.file.FileSystemLocationProperty.getLocationOnly
        - org.gradle.api.file.FileSystemLocationProperty.fileValue
        - org.gradle.api.file.FileSystemLocationProperty.fileProvider
        - org.gradle.api.file.Directory.files
        - org.gradle.api.file.DirectoryProperty.files
- Dependency management
    - Dependency notations
        - org.gradle.api.artifacts.dsl.DependencyHandler.enforcedPlatform(java.lang.Object)
        - org.gradle.api.artifacts.dsl.DependencyHandler.enforcedPlatform(java.lang.Object, org.gradle.api.Action<? super org.gradle.api.artifacts.Dependency>)
        - org.gradle.api.artifacts.dsl.DependencyHandler.testFixtures(java.lang.Object)
        - org.gradle.api.artifacts.dsl.DependencyHandler.testFixtures(java.lang.Object, org.gradle.api.Action<? super org.gradle.api.artifacts.Dependency>)
    - [Capabilities resolution](userguide/dependency_capability_conflict#sec:handling-mutually-exclusive-deps)
        - org.gradle.api.artifacts.CapabilitiesResolution
        - org.gradle.api.artifacts.CapabilityResolutionDetails
        - org.gradle.api.artifacts.ResolutionStrategy.capabilitiesResolution
        - org.gradle.api.artifacts.ResolutionStrategy.getCapabilitiesResolution
    - [Resolution strategy tuning](userguide/resolution_strategy_tuning.html#reproducible)
        - org.gradle.api.artifacts.ResolutionStrategy.failOnDynamicVersions
        - org.gradle.api.artifacts.ResolutionStrategy.failOnChangingVersions
    - [Dependency locking improvements](userguide/dependency_locking.html#dependency-locking)
        - org.gradle.api.artifacts.ResolutionStrategy.deactivateDependencyLocking
        - org.gradle.api.artifacts.dsl.DependencyLockingHandler.unlockAllConfigurations
        - org.gradle.api.artifacts.dsl.DependencyLockingHandler.getLockMode
        - org.gradle.api.artifacts.dsl.DependencyLockingHandler.getLockFile
        - org.gradle.api.artifacts.dsl.DependencyLockingHandler.getIgnoredDependencies
        - org.gradle.api.artifacts.dsl.LockMode
    - [Dependency verification](userguide/dependency_verification.html#verifying-dependencies)
        - org.gradle.api.artifacts.ResolutionStrategy.enableDependencyVerification
        - org.gradle.api.artifacts.ResolutionStrategy.disableDependencyVerification
    - [Dependency constraints improvements](userguide/dependency_constraints.html#dependency-constraints)
        - org.gradle.api.artifacts.dsl.DependencyConstraintHandler.enforcedPlatform(java.lang.Object)
        - org.gradle.api.artifacts.dsl.DependencyConstraintHandler.enforcedPlatform(java.lang.Object, org.gradle.api.Action<? super org.gradle.api.artifacts.DependencyConstraint>)
    - [Component metadata rules improvements](userguide/component_metadata_rules.html#sec:component_metadata_rules)
        - org.gradle.api.artifacts.DirectDependencyMetadata.endorseStrictVersions
        - org.gradle.api.artifacts.DirectDependencyMetadata.doNotEndorseStrictVersions
        - org.gradle.api.artifacts.DirectDependencyMetadata.isEndorsingStrictVersions
        - org.gradle.api.artifacts.DirectDependencyMetadata.getArtifactSelectors
        - org.gradle.api.artifacts.ModuleDependency.endorseStrictVersions
        - org.gradle.api.artifacts.ModuleDependency.doNotEndorseStrictVersions
        - org.gradle.api.artifacts.ModuleDependency.isEndorsingStrictVersions
        - org.gradle.api.artifacts.ComponentMetadataDetails.maybeAddVariant
    - [Repositories](userguide/declaring_repositories.html#declaring-repositories)
        - org.gradle.api.artifacts.repositories.IvyArtifactRepository.getMetadataSources
        - org.gradle.api.artifacts.repositories.IvyArtifactRepository.MetadataSources.isGradleMetadataEnabled
        - org.gradle.api.artifacts.repositories.IvyArtifactRepository.MetadataSources.isIvyDescriptorEnabled
        - org.gradle.api.artifacts.repositories.IvyArtifactRepository.MetadataSources.isArtifactEnabled
        - org.gradle.api.artifacts.repositories.IvyArtifactRepository.MetadataSources.isIgnoreGradleMetadataRedirectionEnabled
        - org.gradle.api.artifacts.repositories.MavenArtifactRepository.getMetadataSources
        - org.gradle.api.artifacts.repositories.MavenArtifactRepository.MetadataSources.isGradleMetadataEnabled
        - org.gradle.api.artifacts.repositories.MavenArtifactRepository.MetadataSources.isMavenPomEnabled
        - org.gradle.api.artifacts.repositories.MavenArtifactRepository.MetadataSources.isArtifactEnabled
        - org.gradle.api.artifacts.repositories.MavenArtifactRepository.MetadataSources.isIgnoreGradleMetadataRedirectionEnabled
    - [Dependency substitution improvements](resolution_rules.html#sec:dependency_substitution_rules)
        - org.gradle.api.artifacts.ArtifactSelectionDetails
        - org.gradle.api.artifacts.DependencyArtifactSelector
        - org.gradle.api.artifacts.DependencyResolveDetails.artifactSelection
        - org.gradle.api.artifacts.DependencySubstitution.artifactSelection
        - org.gradle.api.artifacts.DependencySubstitutions.variant
        - org.gradle.api.artifacts.DependencySubstitutions.platform
        - org.gradle.api.artifacts.DependencySubstitutions.Substitution.withClassifier
        - org.gradle.api.artifacts.DependencySubstitutions.Substitution.withoutClassifier
        - org.gradle.api.artifacts.DependencySubstitutions.Substitution.withoutArtifactSelectors
        - org.gradle.api.artifacts.DependencySubstitutions.Substitution.using
        - org.gradle.api.artifacts.VariantSelectionDetails
    - Miscellaneous changes
        - org.gradle.api.artifacts.result.ResolutionResult.getRequestedAttributes
        - org.gradle.api.artifacts.result.ResolvedComponentResult.getDependenciesForVariant
        - org.gradle.api.artifacts.result.ResolvedDependencyResult.getResolvedVariant
- Tooling API
    - Eclipse models
        - org.gradle.tooling.model.eclipse.EclipseRuntime
        - org.gradle.tooling.model.eclipse.EclipseWorkspace
        - org.gradle.tooling.model.eclipse.EclipseWorkspaceProject
        - org.gradle.tooling.model.eclipse.RunClosedProjectBuildDependencies
    - Testing events
        - org.gradle.tooling.events.OperationType.TestOutput
        - org.gradle.tooling.events.test.Destination
        - org.gradle.tooling.events.test.TestOutputDescriptor
        - org.gradle.tooling.events.test.TestOutputEvent
- Java Ecosystem
    - Java Module System
        - org.gradle.api.jvm.ModularitySpec
        - org.gradle.api.plugins.JavaApplication.getMainModule()
        - org.gradle.api.plugins.JavaPluginExtension.getModularity()
        - org.gradle.api.tasks.compile.JavaCompile.getModularity()
        - org.gradle.api.tasks.compile.CompileOptions.getJavaModuleMainClass()
        - org.gradle.api.tasks.compile.CompileOptions.getJavaModuleVersion()
        - org.gradle.api.tasks.javadoc.Javadoc.getModularity()
        - org.gradle.external.javadoc.MinimalJavadocOptions.getModulePath()
        - org.gradle.external.javadoc.MinimalJavadocOptions.setModulePath()
        - org.gradle.external.javadoc.MinimalJavadocOptions.modulePath()
        - org.gradle.jvm.application.scripts.JavaAppStartScriptGenerationDetails.getModulePath()
        - org.gradle.jvm.application.tasks.CreateStartScripts.getMainClass()
        - org.gradle.jvm.application.tasks.CreateStartScripts.getMainModule()
        - org.gradle.jvm.application.tasks.CreateStartScripts.getModularity()
        - org.gradle.process.JavaExecSpec.getMainClass()
        - org.gradle.process.JavaExecSpec.getMainModule()
        - org.gradle.process.JavaExecSpec.getModularity()

- org.gradle.api.distribution.Distribution.getDistributionBaseName()

<!--
### Example promoted
-->

## Fixed issues

## Known issues

Known issues are problems that were discovered post release that are directly related to changes made in this release.

## External contributions

We love getting contributions from the Gradle community. For information on contributing, please see [gradle.org/contribute](https://gradle.org/contribute).

## Reporting Problems

If you find a problem with this release, please file a bug on [GitHub Issues](https://github.com/gradle/gradle/issues) adhering to our issue guidelines. 
If you're not sure you're encountering a bug, please use the [forum](https://discuss.gradle.org/c/help-discuss).

We hope you will build happiness with Gradle, and we look forward to your feedback via [Twitter](https://twitter.com/gradle) or on [GitHub](https://github.com/gradle).
