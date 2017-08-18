# Backend Ui Renderer

- [Introduction](#introduction)
- [DOM](#dom)
    - [DOM Handlers](#dom-handlers)
    - [DOM Factories](#dom-factories)
    - [Extending DOM](#extending-dom)
    - [DOMPrototype](#dom-prototype)
- [HTML](#html)
    - [HTML Handlers](#html-handlers)
    - [HTML Factories](#html-factories)
    - [HTML Basics](#html-basics)
    - [HTML Forms](#html-forms)

## Introduction
Panda Ui component is a backend ui handler/renderer engine that enables generating html (including xml) content in a more structured way.

This component is extending the normal DOM structure that is offered by the language itself, including some of the following classes:
- [`DOMNode`](http://php.net/manual/en/class.domnode.php)
- [`DOMDocument`](http://php.net/manual/en/class.domdocument.php)
- [`DOMElement`](http://php.net/manual/en/class.domelement.php)

This is the main reason why this component is so powerful, providing extra, faster and more clever functionality using existing components.

## DOM

### DOM Handlers

We built Ui component with the ability to be extensible and configurable.
For this reason we have inserted handlers and factories which can be replaced, just by using the interfaces provided.

In order to be able to manipulate DOM elements in any way, including elements that we create but also elements that we can retrieve from a Document (using xpath), we had to create an independent structure of handlers that can manipulate DOM without actually being part of it.

Building a DOM tree can be a painful process using only native functions, that's why we created DOMHandler, to replace a multi-line functionality with a single-line function call.

`DOMHandler` is responsible for applying simple DOM manipulations to xml documents, generic enough for all the document types. Let's see some examples here:

Adding an attribute:

```php
// Normal DOMElement way
// The same way for get and remove
// Where $element is a DOMElement object
$element->setAttribute($name = 'id', $value = 'id_value');


// Using DOMHandler
$handler = new DOMHandler();

// Set an attribute
$handler->attr($element, $name = 'id', $value = 'id_value', $validate = false);

// Get an attribute
$handler->attr($element, $name = 'id');

// Remove an attribute
$handler->attr($element, $name = 'id', $value = null);
```

The previous example might not say much, but moving on to more functionality, we have the following functions available:
* `attr(DOMElement &$element, $name, $value = '', $validate = false);`
* `attrs(DOMElement &$element, $value = []);`
* `appendAttr(DOMElement &$element, $name, $value);`
* `data(DOMElement &$element, $name, $value = []);`
* `nodeValue(DOMElement &$element, $value = null);`
* `append(DOMElement &$parent, &$child);`
* `prepend(DOMElement &$parent, &$child);`
* `remove(DOMElement &$element);`
* `replace(DOMElement &$old, &$new);`
* `evaluate(DOMDocument $document, $query, $context = null);`

### DOM Factories

After describing the structure with DOMPrototype and DOMItem, we are here to introduce the DOM Factories.
DOM Factories are used to create DOM items with simple calls without the need to create extra objects like the DOMDocument or the DOMHandler.
These factories are not replacing the functionality of a container, but they just provide an interface for creating all the elements needed from a single class.

The DOM Factory class is the smallest form of factory which provides one public function for building a simple DOMElement, the buildElement() function. The DOM Factory expects a DOMPrototype to be set with the setDOMDocument() function so that it can create all the elements needed.

Mainly the factories are accessible through the DOMPrototype, where we provide it as a dependency in the constructor. The Document is responsible for initializing the factory and connecting it to the current document.

```php
// Create a handler instance
$handler = new DOMHandler();

// Create a new factory instance
$factory = new DOMFactory();

// Create a document and provide the handler and factory
$document = new DOMPrototype($handler, $factory);


// Get the factory and build an element
$document->getDOMFactory()->buildElement($name = 'div', $value = 'value');

// Document uses the above function with a 'facade' function called create:
$document->create($name = 'div', $value = 'value');
```

### Extending DOM

Two are the base classes of the entire component, which extend php objects:

DOMItem:
```php
class DOMItem extends DOMElement
{
}
```

DOMPrototype:
```php
class DOMPrototype extends DOMDocument
{
}
```

Both of the objects are using two basic interfaces for handling the entire functionality and are standing there like observers for the building.
Those are the `DOMHandler` and `DOMFactory` interfaces.

#### DOMPrototype

The DOMPrototype object is the base object for XML Documents (and HTML Documents). It provides some basic functionality and the rest is being handled by a DOMHandler and a DOMFactory.

The DOMPrototype object has the following functionalities:
* `create($name = 'div', $value = '')`
* `append($element);`
* `evaluate($query, $context = null);`
* `find($id, $nodeName = '*');`
* `getXML($format = false);`

The above functions support the basic Document object. However you can perform more tasks using the DOMHandler and create more elements using the DOMFactory object.

```php
// Create the document
// Using a container here would make the call a lot easier
$DOMHandler = new DOMHandler();
$DOMFactory = new DOMFactory();
$document = new DOMPrototype($DOMHandler, $DOMFactory);

// Create the root element and append it to the document
$root = $document->create($name = 'root', $value = '');
$document->append($root);

// Create an element and append it to the root
$element = $document->create($name = 'child', $value = 'This is a root child.');
$root->append($element);
```

#### DOMItem

The basic root element for creating DOM elements is the DOMItem basic object. DOMItem extends the given php functionality of a DOMElement and provides a better way to manipulate the item writing less code. The object supports the following functionality:
* `attr($name, $value = '', $validate = false);`
* `attrs($value = []);`
* `appendAttr($name, $value);`
* `nodeValue($value = null);`
* `append(&$element);`
* `appendTo(&$element);`
* `prepend(&$element);`
* `prependTo(&$element);`
* `remove();`
* `replace($element);`

The DOMItem constructor accepts a DOMPrototype (document) so that it can be associated with it and it can be handled by a client (otherwise it will be a read-only object). The Document itself requires a DOMHandler. The DOMItem uses this DOMHandler to manipulate itself using all the previous functions. Basically all these functions are being handled by the DOMHandler and not the DOMItem itself.

```php
// Create the document to associate the DOMItem with
$document = new DOMPrototype(new DOMHandler(), new DOMFactory());

// Create the item
$item = new DOMItem($document, $name = 'div', $value = '');

// Update the item
$item->attr('name', 'item_name');
$item->attr('title', 'item_title');

// Append item to document
$document->append($item);
```

> Every DOMItem that is being created is being appended to the given document so that we can edit it.

## HTML

### HTML Handlers

HTMLHandler extends DOMHandler functionality with HTML specific functions. It provides easy-to-use one-line functions for handling HTML specific attributes so that we can make HTML rendering and manipulation easier. Examples:

```php
$handler = new HTMLHandler();

// Using HTMLHandler, we can simply add a class to the element
// This function will not replicate the class if already exists
$handler->addClass($element, $class = 'new_class');

// Or add a style (append this to the style attribute)
$handler->style($element, 'color', 'blue');
$handler->style($element, 'font-size', '23px');
```

One of the most important functionalities (and useful) is select(). This function acts like a jQuery selector function, where we can provide a css selector and return the matched items. This function returns a DOMNodeList object and those objects can be manipulated using the handler itself:
```php
$handler = new HTMLHandler();

// We want to find the document title and change the class and the value
$title = $handler->select($element->ownerDocument, $selector = '.web-document .title', $context = null)->item(0);

// Add a new class
$handler->addClass($title, $class = 'blue');

// Set the title
$handler->nodeValue($title, 'This is the document title');
```

HTMLHandler provides a list of HTML-specific functionalities:
* `addClass(DOMElement &$element, $class)`
* `removeClass(DOMElement &$element, $class)`
* `hasClass(DOMElement $element, $class)`
* `style(DOMElement &$element, $name, $val = '')`
* `innerHTML(DOMElement &$element, $value = null, $faultTolerant = true, $convertEncoding = true)`
* `outerHTML(DOMElement $element)`
* `select(DOMDocument $document, $selector, $context = null)`

### HTML Factories

Extending DOM Factory, we have created HTML Factory for HTML-specific functionality and building. The HTML Factory provides an interface for building html elements which would need more than 2 or 3 lines to be built. This factory is being used the same way as the previous factory, in HTMLDocument object. Supported functionality:

* `buildHtmlElement($name = '', $value = '', $id = '', $class = '');`
* `buildWebLink($href = '', $target = '_self', $content = '', $id = '', $class = '');`
* `buildMeta($name = '', $content = '', $httpEquiv = '', $charset = '');`
* `buildLink($rel, $href);`
* `buildScript($src, $async = false);`

```php
// Create a handler instance
$handler = new HTMLHandler();

// Create a new factory instance
$factory = new HTMLFactory();

// Create a document and provide the handler and factory
$document = new HTMLDocument($handler, $factory);


// Get the factory and build an element
$document->getHTMLFactory()->buildElement($name = 'div', $value = 'value', $id = 'id', $class = 'class');

// Document uses the above function with a 'facade' function called create:
$document->create($name = 'div', $value = 'value', $id = 'id', $class = 'class');
```

DOMDocument and HTMLDocument both accept a DOMFactory and an HTMLFactory accordingly so that they can build their elements. The factories are being injected in the constructor, which means that they can be replaced by any DOMFactoryInterface or HTMLFactoryInterface.

### HTML Basics

#### HTMLDocument

Extending basic DOMPrototype, we have created the HTMLDocument object that can be used specifically for HTML pages. It's a base class for easily creating HTML pages, also having access to the HTMLFactory class which provides a lot of functions for creating different html elements.

#### HTMLElement

Now that we have seen the DOMItem and the basic DOM functionality, which extends the native DOMElement functionality, we are introducing the HTMLElement. HTMLElements extend the base DOMItem and offer a new HTML-specific functionality to allow us to create HTML pages, elements and snippets.

The HTML element provides the following functionality as extra and HTML-specific:
* `data($name, $value = []);`
* `addClass($class);`
* `removeClass($class);`
* `hasClass($class);`
* `style($name, $val = '');`
* `innerHTML($value = null, $faultTolerant = true, $convertEncoding = true);`
* `outerHTML();`
* `select($selector, $context = null);`

All the previous functions are using the HTMLHandler that is given in the HTMLDocument that the HTMLElement accepts at the constructor.

```php
// Create an HTMLDocument
$document = new HTMLDocument(new HTMLHandler(), new HTMLFactory());

// Create an element
$element = new HTMLElement($document, $tag = 'div', $value = '', $id = 'el_id', $class = 'el_class');

// It's easy to add a remove classes
$element->addClass('class_2');
$element->removeClass('el_class');

// We also can add data attribute using json encoding
$data = ['name1' => 'val1', 'name2' => 'val2'];
$element->data('test', $data);

// The previous data() call will generate the following attribute:
// data-test='{"name1":"val1","name2":"val2"}'

// Finally append the element to the document
// As we discussed before, we don't have to do this if we want to append the element directly to the document
$document->append($element);
```

### HTML Forms

#### Platform Generic Form Element

Form builder/factory allows the creation of items with all the appropriate attributes in single-line calls. formItem is an internal object that is being used by the form builder in order to have less, more flexible and smarter code.

#### PHP Examples

```php
// Single-line creator of a form element
	
// Create an HTMLDocument
$document = new HTMLDocument(new HTMLHandler(), new HTMLFactory());

// Create a form element
$fe = new FormElement($document, $itemName = 'select', $name = 'gender', $value = '', $id = '', $class = '', $itemValue = '');

// Append item to anything
$container->append($fe);
```

#### Platform Generic Form Input
Form builder/factory allows the creation of inputs with all the appropriate attributes in single-line calls. FormInput is an internal object that is being used by the form builder in order to have less, more flexible and smarter code.

The FormInput extends the FormElement which minimizes the code even more, allowing the platform to be faster and more efficient. Example:

```php
// Single-line creator of a form input

// Create an HTMLDocument
$document = new HTMLDocument(new HTMLHandler(), new HTMLFactory());

// Create a form input
$fi = new FormInput($document, $type = 'text', $name = 'name', $id = '', $class = '', $value = '', $required = false);

// Append input to form inner container
$container->append($fi);
```

As seen above, we can create input items given the type and the name. Of course, in a form we can use more than inputs (select, textarea etc.) and this is why there is the FormElement object.
