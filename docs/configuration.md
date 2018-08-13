# Configuration

- [Introduction](#introduction)
- [Foundation](#foundation)
    - [Configure the Environment](#configure-the-environment)
- [Environment Specific Configuration](#environment-specific-configuration)
- [Reading The Configuration](#reading-the-configuration)

## Introduction

All of the configuration files for the Panda framework are stored in the `config` directory, unless it's specified otherwise.

## Foundation

The most basic logic and functionality of Panda framework is in the class `Panda\Foundation\Application`.

### Container / DI

Panda framework is using [php-di/php-di](https://github.com/PHP-DI/PHP-DI) as the main dependency injection container.

The `Application` class works with php-di to provide all the classes each constructor needs.

### Initialization

To initialize the Application, you are going to need 3 parameters:

#### Base Path.

Contains the working directory of the application

#### Configuration Path (optional). 

Contains the config directory, relative to base path. If none is provided, `config` is used by default.

#### Environment (optional)

Contains the application environment as context to running the code. If no environment is injected to the above constructor, 
the environment is being retrieved from the system's `APPLICATION_ENV` variable.
If no environment is configured to this variable, `default` is used.

Example:

```php
$app = new Panda\Foundation\Application(
    $basePath = realpath(__DIR__ . '/../'),
    'config/',
    null
);
```

### Configure the Environment

It is often helpful to have different configuration values based on the environment where the application is running.

To allow the Panda framework to work in different environment, you have to define it on the Foundation class' constructor:

```php
$app = new Panda\Foundation\Application(
    $basePath = realpath(__DIR__ . '/../'), 
    null, 
    'development'
);
```

## Environment Specific Configuration

One basic example is the keys you are using for services or databases. You might connect to a different database
when working on your project locally than on production.

Panda accepts different environment configuration by allowing multiple config files per environment.

### Default configuration

In file `config-default.json`, there is all the default configuration for the entire Panda application.

### Environment-specific configuration

If you would like to setup configuration values only for your development environment, you should build a file called
`config-development.json` which will contain all your development values.

Panda environment-specific configuration extends the default configuration, so you may define only the values
that are different from the default values. This way, you can build a default configuration set and extend only
the values that are different.
For example, you can set different service keys or logger or localization configuration.

## Reading The Configuration

### Registry

Panda Configuration is using the [Registry](https://github.com/PandaPlatform/registry) package.

Registry is a package that allows to load shared and non-shared values into memory for as long the script is running.

### Shared Configuration

Panda Configuration extends Registry as a behavior and loads configuration values as a shared registry on startup.

You can use registry and find the entire configuration under the key `config`:

```php
use Panda\Registry\SharedRegistry;

// Create a shared registry instance
$registry = new SharedRegistry();

// Read shared configuration
$config = $registry->get('config');
```

The following example will provide the same result:

```php
use Panda\Config\SharedConfiguration;

// Create a shared configuration instance
$configuration = new SharedConfiguration();

// Read configuration
$config = $configuration->get();
```

On startup, `SharedConfiguration` reads the entire environment-specific configuration statically into memory and makes it
accessible for all Controllers and services, regardless of having an instance or not.

### Encapsulated values

Configuration is working the same way Registry is with encapsulated values and dot-syntax.

If you have set configuration values from an array, you can use `parent1.parent2.parent3.value` to retrieve a value
that has a depth of 3, in the logic of arrays, instead of using `['parent1']['parent2']['parent3']['value']`:

```php
use Panda\Config\SharedConfiguration;

// Create a shared configuration instance
$configuration = new SharedConfiguration();

// Read the encapsulated value
$value = $configuration->get('parent1.parent2.parent3.value');
``` 
