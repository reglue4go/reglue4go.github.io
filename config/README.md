# Config

{% include headOScoverage.md %}

> Table Of Contents
>
> -   [Introduction](#introduction)
> -   [Available Methods](#available-methods)
> -   [Method Listing](#method-listing)
> -   [Unit Tests Matrix](#unit-tests-matrix)

## Introduction

The config package provides an expressive agnostic interface for working with configuration loaders. You can use third party loaders like viper.

```go
package main

import (
	"github.com/reglue4go/config"

	"github.com/knadh/koanf"
	"github.com/knadh/koanf/parsers/yaml"
	"github.com/knadh/koanf/providers/file"
)

func main() {
	repo := createRepository()

	httpPort := repo.Get("server.ports.http").ToInt()

	jdbcPort := repo.Get("server.ports.jdbc", 1521).ToInt()

	fmt.Printf("%v\n", httpPort) // 8080
	fmt.Printf("%v\n", jdbcPort) // 1521
}

// you can use viper or any other loader
func createRepository() *config.Repository {
	handler := koanf.New(".") // Use "." as the key path delimiter.
	repository := config.New().
		AddLoader(func(_ *config.Repository) {
			handler.Load(file.Provider(target), yaml.Parser())
			// handler.Load(file.Provider(target), toml.Parser())
			// handler.Load(file.Provider(target), json.Parser())
		}).
		AddRetriever(func(key string) (any, bool) {
			if handler.Exists(key) {
				return handler.Get(key), true
			}
			return nil, false
		}).
		Load()
	return repository
}

```

## Available Methods

| &#8230;                               | &#8230;                 | &#8230;                       |
| :------------------------------------ | :---------------------- | :---------------------------- |
| [AddKeysRetriever](#addkeysretriever) | [AddLoader](#addloader) | [AddRetriever](#addretriever) |
| [Get](#get)                           | [Items](#items)         | [Keys](#keys)                 |
| [Load](#load)                         | [Loaders](#loaders)     | [Retrievers](#retrievers)     |

## Method Listing

#### [AddKeysRetriever](#available-methods)

The AddKeysRetriever method registers a callback for retrieving all configuration keys:

```go
repository.AddKeysRetriever(func() []string {
	return viper.AllKeys()
})
```

#### [AddLoader](#available-methods)

The AddLoader method adds a new configuration loader and returns the Repository instance:

```go
repository.AddLoader(func(_ *config.Repository) {
	viper.AddConfigPath("./config")
	viper.SetConfigFile("./config/app.yaml")
	viper.MergeInConfig()
	viper.WatchConfig()
})

repository.AddLoader(func(repo *config.Repository) {
	repo.Items().
		Put("app.name", "recipes").
		Put("app.url", "https://recipes.io")
})
```

#### [AddRetriever](#available-methods)

The AddRetriever method adds a new configuration retriever and returns the Repository instance:

```go
repository.AddRetriever(func(key string) (any, bool) {
	if viper.IsSet(key) {
		return viper.GetString(key), true
	}
	return nil, false
})
```

#### [Get](#available-methods)

The Get method retrieves the specified configuration as a Fluent value. An optional second value can be provided as fallback in case of a missing key:

```go
value := repository.Get("server.ports.http")

fmt.Printf("%v\n", value.ToInt()) // 8080

fmt.Printf("%v\n", value.ToString()) // "8080"
```

#### [Items](#available-methods)

The Items method returns the underlying Bag(map) of loaded configurations:

```go
bag := repository.Items()

fmt.Printf("%v\n", bag.IsEmpty()) // false
```

#### [Keys](#available-methods)

The Keys method retrieves all loaded configuration keys:

```go
value := repository.Keys()

fmt.Printf("%v\n", value) // ["http_port", "jdbc_port"]
```

#### [Load](#available-methods)

The Load method reads configurations from provided sources like files and databases:

```go
value := repository.Load().Get("app.name", "recipes").String()

fmt.Printf("%v\n", value) // "recipes"
```

#### [Loaders](#available-methods)

The Loaders method optionally adds a new configuration loader and returns the list of registered loaders:

```go
callbacks := repository.Loaders()
```

#### [Retrievers](#available-methods)

The Retrievers method optionally adds a new configuration retriever and returns the list of registered retrievers:

```go
callbacks := repository.Retrievers()
```

{% include footMatrixes.md %}
