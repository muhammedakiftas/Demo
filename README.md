# Google Cloud Translate | Android [![GoDoc](https://godoc.org/github.com/ahmetb/go-linq?status.svg)](https://godoc.org/github.com/ahmetb/go-linq) [![Build Status](https://travis-ci.org/ahmetb/go-linq.svg?branch=master)](https://travis-ci.org/ahmetb/go-linq) [![Coverage Status](https://coveralls.io/repos/github/ahmetb/go-linq/badge.svg?branch=master)](https://coveralls.io/github/ahmetb/go-linq?branch=master)
A powerful language integrated query (LINQ) library for Go.
* Written in vanilla Go, no dependencies!
* Complete lazy evaluation with iterator pattern
* Safe for concurrent use
* Supports generic functions to make your code cleaner and free of type assertions
* Supports arrays, slices, maps, strings, channels and custom collections

## Usage

Usage is as easy as chaining methods like:

`From(slice)` `.Where(predicate)` `.Select(selector)` `.Union(data)` 

**Example 1: Find all owners of cars manufactured after 2015**

```go
multiDexEnabled true

...

configurations.all {
    resolutionStrategy.force 'com.google.code.findbugs:jsr305:1.3.9'
}

...

packagingOptions {
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/io.netty.versions.properties'
        exclude 'META-INF/INDEX.LIST'
}

...


    compile ('com.google.apis:google-api-services-translate:v2-rev47-1.22.0') {
        exclude group: 'com.google.guava'
        exclude module: 'httpclient' //by artifact name
    }
    compile ('com.google.cloud:google-cloud-translate:0.5.0') {
        exclude group: 'io.grpc', module: 'grpc-all'
        exclude group: 'com.google.protobuf', module: 'protobuf-java'
        exclude group: 'com.google.api-client', module: 'google-api-client-appengine'
        exclude module: 'httpclient' //by artifact name
        exclude group: 'org.json', module: 'json'
    }

```

Or, you can use generic functions, like `WhereT` and `SelectT` to simplify your code
(at a performance penalty):

**Example 2: Find the author who has written the most books**

```go
import . "github.com/ahmetb/go-linq"
	
type Book struct {
	id      int
	title   string
	authors []string
}

author := From(books).SelectMany( // make a flat array of authors
	func(book interface{}) Query {
		return From(book.(Book).authors)
	}).GroupBy( // group by author
	func(author interface{}) interface{} {
		return author // author as key
	}, func(author interface{}) interface{} {
		return author // author as value
	}).OrderByDescending( // sort groups by its length
	func(group interface{}) interface{} {
		return len(group.(Group).Group)
	}).Select( // get authors out of groups
	func(group interface{}) interface{} {
		return group.(Group).Key
	}).First() // take the first author
```

**Example 3: Implement a custom method that leaves only values greater than the specified threshold**

```go
type MyQuery Query

func (q MyQuery) GreaterThan(threshold int) Query {
	return Query{
		Iterate: func() Iterator {
			next := q.Iterate()

			return func() (item interface{}, ok bool) {
				for item, ok = next(); ok; item, ok = next() {
					if item.(int) > threshold {
						return
					}
				}

				return
			}
		},
	}
}

result := MyQuery(Range(1,10)).GreaterThan(5).Results()
```

**More examples** can be found in the [documentation](https://godoc.org/github.com/ahmetb/go-linq).

## Release Notes

~~~
v3.0.0 (2017-01-10)
* Breaking change: ToSlice() now overwrites existing slice starting
  from index 0 and grows/reslices it as needed.
* Generic methods support (thanks @cleitonmarx!)
  - Accepting parametrized functions was originally proposed in #26
  - You can now avoid type assertions and interface{}s
  - Functions with generic methods are named as "MethodNameT" and
    signature for the existing LINQ methods are unchanged.
* Added ForEach(), ForEachIndexed() and AggregateWithSeedBy().

v2.0.0 (2016-09-02)
* IMPORTANT: This release is a BREAKING CHANGE. The old version
  is archived at the 'archive/0.9' branch or the 0.9 tags.
* A COMPLETE REWRITE of go-linq with better performance and memory
  efficiency. (thanks @kalaninja!)
* API has significantly changed. Most notably:
  - linq.T removed in favor of interface{}
  - library methods no longer return errors 
  - PLINQ removed for now (see channels support)
  - support for channels, custom collections and comparables

v0.9-rc4
* GroupBy()

v0.9-rc3.2
* bugfix: All() iterating over values instead of indices

v0.9-rc3.1
* bugfix: modifying result slice affects subsequent query methods

v0.9-rc3
* removed FirstOrNil, LastOrNil, ElementAtOrNil methods 

v0.9-rc2.5
* slice-accepting methods accept slices of any type with reflections

v0.9-rc2
* parallel linq (plinq) implemented
* Queryable separated into Query & ParallelQuery
* fixed early termination for All

v0.9-rc1
* many linq methods are implemented
* methods have error handling support
* type assertion limitations are unresolved
* travis-ci.org build integrated
* open sourced on github, master & dev branches
~~~
