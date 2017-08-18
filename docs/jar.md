# Json Asynchronous Response

- [Introduction](#introduction)
- [JSON Response](#json-response)
- [HTML Response](#html-response)

Panda Jar package was created to enable a global format of all asynchronous responses towards the client who is calling an api using AJAX.

Every Jar response is based on an abstract AsyncResponse object which extends Response from the Symfony component, so it can be used safely by all the frameworks that are following this methodology and are already using Symfony Requests and Responses.

## Introduction

The base response object consists of a list of headers and a list of content objects.
The AsyncResponse base object offers an interface for adding headers and content. 

However, it does not generate the response output.
The output can be json (which is also the purpose of this component) but can also be something else, like xml.

## JSON Response

Extending the base functionality, `JSONResponse` object generates json responses.
The entire component is based on this class to generate a proper response using json.

Here is a simple response with no headers and just json content (`JSONResponse::CONTENT_JSON`).
Let's assume it's an API response for account information.

```php
// Creating a json response:
$response = new JSONResponse();

// Getting account information
$accountInfo = ['first_name' => 'John', 'last_name' => 'Smith'];

// Adding content to the response and return to the handler
$response->addResponseContent($accountInfo , $key = 'account', $type = JSONResponse::CONTENT_JSON);

// The current output would be something like this:
// {"headers":{},"content":{"account":{"type":"json","payload":{"first_name":"John","last_name":"Smith"}}}}

// We can add more content to the response just by simply calling the same function.
$response->addResponseContent(['locale' => 'el_GR', 'timezone' => 'Europe/Athens'], $key = 'settings', $type = JSONResponse::CONTENT_JSON);

// The current output would be something like this:
// {"headers":{},"content":{"account":{"type":"json","payload":{"first_name":"John","last_name":"Smith"}},"settings":{"type":"json","payload":{"locale":"el_GR","timezone":"Europe/Athens"}}}}
```

The previous example will be handled by the Javascript handler and will get all the necessary information.

#### Headers

We can also continue and use the existing functionality to add headers to the response. 
Headers are there to be used for response information, which can be different from payload. 
The developer is free to decide what kind of information can go into the headers and what info will go in the content payloads.

Let's add some headers into the response:

```php
// Add a api info response
$response->addResponseHeader(['version' => 'v1'], $key = 'api_info', $merge = true);

// The current output would be something like this:
// {"headers":{"api_info":{"version":"v1"}},"content":{"account":{"type":"json","payload":{"first_name":"John","last_name":"Smith"}},"settings":{"type":"json","payload":{"locale":"el_GR","timezone":"Europe/Athens"}}}}
```

#### Events

Json response has a special payload type called events (`JSONResponse::CONTENT_EVENT`).
Events are actions that could be parsed and triggered to the document or the sender.
This type of payload is a convention so that it is separated from the rest of the responses.

Events can have a value or just the event name.
In this case, the key attribute is just for the response json payload.

Example:

```php
// Creating a json response:
$response = new JSONResponse();

// Add an event payload
$response->addEventContent($name = 'page.reload', $value = '', $key = '');

// The new output would be something like this:
// {"headers":{},"content":{"0":{"type":"event","payload":{"name":"page.reload"}}}

// The above example can be caught using jQuery on the document:
$(document).on('page.reload', function(ev) {
    location.reload();
});


// Add an event with a value
$response->addEventContent($name = 'project.reload', $value = '1', $key = '');

// The new output would be something like this:
// {"headers":{},"content":{"0":{"type":"event","payload":{"name":"project.reload","value":"1"}}}

// The above example can be caught using jQuery on the document, and get the project id
$(document).on('page.reload', function(ev, projectId) {
    console.log(projectId);
});
// It will output:
// 1
```

## HTML Response

Extending the existing Json Response, we've created the HTMLResponse object.
This object parses a DOMElement and generates a json response that includes html content.

`HTMLResponse` object should be used for approaches where the html is being generated on the backend and you want to provide an html-ready content.
It also provides extra attributes to guide the content to the desired container with options to append or replace the contents.

Below is an example of using a DOMElement and send it through JSONResponse:

```php
// Creating an html response:
$response = new HTMLResponse();

// Create a DOMElement, in the simplest form
$element = new DOMElement($name = 'div', $value = 'Element value');

// Adding content to the response and return to the handler
$response->addResponseContent($element, $type = HTMLResponse::CONTENT_HTML, $holder = '.holder_class', $method = HTMLResponse::REPLACE_METHOD, $key = '');

// The generated output of the previous response would be the following:
// {"head":{},"body":{"0":{"type":"html","payload":{"holder":".holder_class","method":"replace","html":"<div>Element value</div>"}}}}
```

In the previous example we can see 2 more attributes, the holder and the method attributes:
* The **holder** attribute is a css selector of the container that will host the provided html.
* The **method** attribute guides the engine to append the content to the container or replace the existing contents.
