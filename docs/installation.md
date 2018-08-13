# Installation

- [Installation](#installation)
    - [Installing Panda](#installing-panda)
    - [Configuration](#configuration)
- [Web Server Configuration](#web-server-configuration)

## Installing Panda

Panda utilizes [Composer](https://getcomposer.org) to manage its dependencies.
So, before using Panda, make sure you have Composer installed on your machine.

### Panda Components

Panda as a project offers a complete framework including a set of components a website
needs to work fully, like Routing, Storage and Controllers.

Panda code offers:

- Panda Framework
- Web Application Template
- API Template

#### Panda Framework

Panda framework is a set of components that work together to serve a website, built in php.

To install panda framework, using composer, use the following command:

```
composer require panda/framework
```

#### Web Application template

Panda framework can also come in a website-ready template to build a full website with
already configured controllers, routes and configuration files for different environments.

The Web Application template is using [panda-ui](https://github.com/PandaPlatform/ui) as an html render engine,
to generate html output manipulating html in views on php side.

The Web Application template is also using [panda-jar](https://github.com/PandaPlatform/jar) as a Json Async Response
library in order to make better ajax request and handle responses globally.
It works perfectly with [panda-js-http](https://github.com/PandaPlatform/panda-js-http) library to manage event
listeners and jar responses.

To install Panda Web Application Template, use the following command:

```
composer create-project --prefer-dist panda/panda
```

#### API template

If you are building an API, without html renderer and with preference in handling multiple
requests and returning descriptive responses, the API Template is the perfect template for you.

To install Panda API template, use the following command:

```
composer create-project --prefer-dist panda/api
```

## Configuration

### Public Directory

After installing Panda, you should configure your web server's document / web root to be the `public` directory.
The `index.php` in this directory serves as the front controller for all HTTP requests entering your application.

### Configuration Files

All of the configuration files for the Panda framework are stored in the `config` directory.

### Directory Permissions

After installing Panda, you may need to configure some permissions.
Directories within the `storage` directory should be writable by your web server or Panda will not run. 

### Additional Configuration

Panda needs almost no other configuration out of the box. You are free to get started developing!
However, you may wish to review the the [Configuration](configuration.md) doc.
It contains several options that you may wish to change according to your application.

## Web Server Configuration

### Pretty URLs

#### Apache

Panda Web Application template includes a `public/.htaccess` file that is used to provide URLs without the `index.php`
front controller in the path.
Before serving Panda with Apache, be sure to enable the `mod_rewrite` module so the `.htaccess` file will work with the server.
