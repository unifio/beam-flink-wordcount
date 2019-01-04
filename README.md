# Beam Flink Runner Example

This project is a hello world Beam application that uses the flink runner. This example was based off of the [Beam QuickStart](https://beam.apache.org/get-started/quickstart-java/) workcount tutorial.

## Create fat JAR for deployment

* Build the jar

```bash

cd word-count-beam
mvn clean package -Pflink-runner -Dmaven.test.skip=true
```

* Will output needed fat jar to `./target/word-count-beam-bundled-0.1.jar`

### How to use jenv on macos

Java 8 is required to build this project. An example of setting this up on macos is provided below.

```bash
brew tap homebrew/cask-versions
brew search java
brew cask install java8
brew install jenv
echo 'eval "$(jenv init -)"' >> ~/.bash_profile
# Add each jdk you may already have installed
# So jenv can be used to switch between them
 jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_192.jdk/Contents/Home
 jenv add /Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home
# Set the global jdk to use
 jenv global 10.0
# Enable every plugin
 for i in $(jenv plugins); do jenv enable-plugin ${i}; done
# Add the export plugin
 jenv enable-plugin export
 source ~/.bashrc
# Now Make version 1.8 the version to use in the current shell.
 export JENV_VERSION=1.8
```

### Changes to WordCount

Several modifications were required in order to leverage the wordcount project to test the latest Flink deployed in AWS EMR.

* Adding AWS sdk dependencies for beam
* Adding the `beam-sdks-java-io-amazon-web-services` as a dependency.
* Excluding the jackson and aws sdks from the `beam-sdks-java-io-amazon-web-services` dependency and specifying the latest versions as dependencies. Without this change the current `beam-sdks-java-io-amazon-web-services` compiled dependencies fail due to an unknown property in the AWS SDK shown below:
```
Unrecognized field "disableHostPrefixInjection" (class com.amazonaws.ClientConfiguration), not marked as ignorable (6 known properties: "preemptiveBasicProxyAuth", "proxyUsername", "proxyHost", "useExpectContinue", "proxyPassword", "proxyPort"])at [Source: (String)"{"proxyHost":null,"proxyPort":-1,"proxyUsername":null,"proxyPassword":null,"preemptiveBasicProxyAuth":false,"useExpectContinue":true,"disableHostPrefixInjection":false}"; line: 1, column: 168] (through reference chain: com.amazonaws.ClientConfiguration["disableHostPrefixInjection"])
```
* Declaring the awsRegion as required for accessing the S3 endpoint.


