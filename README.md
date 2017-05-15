# Google Cloud Translate | Android [![Build Status](https://travis-ci.org/ahmetb/go-linq.svg?branch=master)](https://github.com/GoogleCloudPlatform/google-cloud-java/tree/master/google-cloud-translate)
Google Cloud Translate with Word Translation on Android.
* There is Dependencies and Code !

## Usage

Usage is as easy as chaining methods like:

`build.gradle`  

```go
android {
	...
	defaultConfig {
		...
		multiDexEnabled true
	}
	
	configurations.all {
	    resolutionStrategy.force 'com.google.code.findbugs:jsr305:1.3.9'
	}

	packagingOptions {
		exclude 'META-INF/LICENSE'
		exclude 'META-INF/io.netty.versions.properties'
		exclude 'META-INF/INDEX.LIST'
	}
}

dependencies {
	...
	compile ('com.google.apis:google-api-services-translate:v2-rev47-1.22.0') {
	    exclude group: 'com.google.guava'
	    exclude module: 'httpclient' 
	}
	compile ('com.google.cloud:google-cloud-translate:0.5.0') {
	    exclude group: 'io.grpc', module: 'grpc-all'
	    exclude group: 'com.google.protobuf', module: 'protobuf-java'
	    exclude group: 'com.google.api-client', module: 'google-api-client-appengine'
	    exclude module: 'httpclient'
	    exclude group: 'org.json', module: 'json'
	}
}
```

Or, you can use generic functions, like `WhereT` and `SelectT` to simplify your code
(at a performance penalty):

**More examples** can be found in the [documentation](https://github.com/GoogleCloudPlatform/google-cloud-java/tree/master/google-cloud-translate).
