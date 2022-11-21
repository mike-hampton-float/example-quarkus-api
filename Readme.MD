# Quarkus Client Mapping issue

This repo is just a tiny example repo to illustrate a mapping issue I am seeing with the
quarkiverse/ quarkus-openapi-generator.

It contains a simple [openapi file](spec/openapi.yaml)

This file is "fed" into the generator in this [build file](generated/quarkus-java-client/pom.xml)

There is a [application.properties file here](generated/quarkus-java-client/src/main/resources/application.properties)

That provides a mapping to allow the openapi `DateTime` to be represented as a `java.time.Instant`
```
quarkus.openapi-generator.codegen.spec.openapi_yaml.import-mappings.Instant=java.time.Instant
quarkus.openapi-generator.codegen.spec.openapi_yaml.type-mappings.DateTime=Instant
```

Aftter running the build (`mvn clean install`) the [code produced](generated/quarkus-java-client/target/generated-sources/open-api-yaml/com/example/hammer/client/model/Thing.java)
shows the types specified as `DateTime` (`date-time`) are generated with a type of `java.time.OffsetDateTime`.

This is not working.  I use the same technique when building the server api in maven:
```xml
        <configuration>
            <inputSpec>${project.basedir}/../../spec/openapi.yaml</inputSpec>
            <generatorName>jaxrs-spec</generatorName>
            <templateDirectory>${project.basedir}/../../spec/generator-templates</templateDirectory>
            <configOptions>
                <basePackage>${basePackageName}</basePackage>
                <apiPackage>${basePackageName}.api</apiPackage>
                <configPackage>${basePackageName}.config</configPackage>
                <invokerPackage>${basePackageName}.invoker</invokerPackage>
                <modelPackage>${basePackageName}.model</modelPackage>
                <interfaceOnly>true</interfaceOnly>
                <useSwaggerAnnotations>false</useSwaggerAnnotations>
                <useTags>true</useTags>
            </configOptions>
            <typeMappings>
                <typeMapping>DateTime=Instant</typeMapping>
                <typeMapping>Date=LocalDate</typeMapping>
            </typeMappings>
            <importMappings>
                <importMapping>Instant=java.time.Instant</importMapping>
                <importMapping>LocalDate=java.time.LocalDate</importMapping>
            </importMappings>

```

This appears to be a bug in the generator.