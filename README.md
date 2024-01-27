![Ephect](assets/images/salamandra.png)

## What is Ephect ?

**E**phect is a **P**rogramming **H**ybrid **E**nvironment with **C**omponent-based **T**emplates. 

It's a comprehensive solution that combines a lightweight PHP framework and a JavaScript library.

Ephect allows you to design backend pages and components a fluent way as well as web components.

It integrates a build process based on webpack.

## Featured concepts

### The components

Basically, components are custom HTML tags. All concepts of HTML tags on backend side are applicable to components except the naming convention: a component name must begin with a capital letter. 

The logic of the component is coded in a simple funcion returning a template.

#### Example 1

Here is an open component that can surround children components or just HTML code.

```html
<MyComponent>
    Hello World!
</MyComponent>
```

The code would look like this:

```php
<?php

namespace QuickStart;

function MyComponent($children) {

    return (<<< HTML
    <h1>My component</h1>
    <p>
    {% raw %}{{ children }}{% endraw %}
    </p>
    HTML);
}
```

It will render:

```html
    <h1>My component</h1>
    <p>
    Hello World!
    </p>
```

#### Example 2

A closed component to which we pass arguments:

```html
<AnotherComponent with="Some argument" />
```

The code would look like this:

```php
<?php

namespace QuickStart;

function AnotherComponent($props) {

    return (<<<HTML
    <h2 id="with">
        {% raw %}{{ props->with }}{% endraw %}
    </h2>
    HTML);
}
```

It will render:

```html
    <h2 id="with">
    Some argument
    </h2>
```
### Cascading and reusing components

The components can be declared in cascade and the same component can be reused in each cascading component.

If we combine examples 1 and 2:

```html
<MyComponent>
    <AnotherComponent with="Some argument" />
</MyComponent>
```

It will render:

```html
    <h1>My component</h1>
    <p>
    <h2 id="with">
        Some argument
    </h2>
    </p>
```

## The component Slot

A parent component with named placeholders can be inherited to change the content of its placeholders. The component in charge of managing placeholders is called *Slot*.

Here is a Mother component containing slots to be overriden:

```php
<?php

namespace QuickStart;

function Mother($children)
{

    return (<<< HTML
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <title>
            <Slot name="title">
                Mother!
            </Slot>
            </title>

            <Slot name="stylesheets">
            </Slot>
        </head>

        <body>
            <div class="App" >
                {% raw %}{{ children }}{% endraw %}
            </div>
            <Slot name="javascripts">
            </Slot>
        </body>
    </html>
    HTML);
}
```

And here is a component inheriting from Mother component:

```php
<?php

namespace QuickStart;

function Home()
{
    return (<<< HTML
    <Mother>
        <Slot name="title">Ephect in action !</Slot>
        <Slot name="stylesheets">
            <link rel="stylesheet" href="/css/app.css" />
        </Slot>
        <!-- 
            The children container of the parent component
            is filled with all that is not nested in slots
        -->
        <div class="App-content" >
            <h1>Welcome Home!</h1>
        </div>
        <!-- end of children code -->
        <Slot name="javascripts">
            <script src="/js/ephect.js"></script>
        </Slot>
    </Mother>
    HTML);
}
```

It will render: 

```html
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <title>
                Ephect in action !
            </title>

            <link rel="stylesheet" href="/css/app.css" />
        </head>

        <body>
            <div class="App">
                <div class="App-content">
                    <h1>Welcome Home!</h1>
                </div>
            </div>
            <script src="/js/ephect.js"></script>
        </body>
    </html>
```


### Hooks

**Ephect** introduces *hooks* to manage the data workflow. Additionally to well-known useEffect and useState hooks in the **ReactJS** realm, **Ephect** comes with 3 new basic hooks:

- useProps: to ensure a property can be used,
- useQueryArgument: to filter the value passed as parameter to the query,
- useSlot: to bind variables nested in slots to the parent component.

## CLI tool *egg*

**Ephect** comes with a CLI tool called *egg*. The main feature of **egg** is to pre-compile the code of the whole application at once. This is done in one command: *php egg compile*. The benefits of this approach are the errors management and the increase of velocity of the application.

### Error handling

Every syntax error is handled as a fatal error which stops the compilation process. An explicit message is displayed on the console output to help you fix it quickly.

### Headless page generator

The compiler parses the templates of every component owned by a page comprising the cascading components. Each component is translated from **Ephect** template code to pure functional **PHP** code and cached. The code of each component is light and fast to run. 

Once the pages are prepared in cache, browsing them is as fast as possible. The only bottleneck that you should be aware of is the data resources calls.

## Requirements

The minimal version of PHP needed for Ephect is 8.2.

You may also need to install some PHP extensions, if it's not done yes, like PDO/Sqlite, cUrl, Iconv, etc.

## Install the framework

It's recommended to use the composer create-project command in order to get all the needed stuff.

There's a dedicated project you can find here **[ephect-io/create-app](https://github.com/ephect-io/create-app)** that will help you for a quick start. 

Just do:

    composer create-project ephect-io/create-app myproject

where *myproject* is the name of your project. 

## Install the sample application

Move to *myproject* directory and type:

    php egg make:quickstart

You will see a **app** directory in which you will find the standard structure of an ephect application and a **public** directory in which is stored the index.php. Ephect doesn't really care of the actual project pstructure provided that all componenets are under **app** directory. It means you can organize your application tree as you wish.

## Build the application

### Using the PHP only context

If no issue is popping up on the console then you can generate your application outside the browser.

However, you first need to launch the embedded web server.

If you're under Windows, you need to type this:

    php -S localhost:8888 -t src/public

otherwise, MacOS and Linux accept this syntax:

    php egg build

If you installed the QuickStart application as said previously, you should see something like this:

    Compiling App ... 059ms
    Compiling Home, querying http://localhost:8000/ ... 193ms
    Compiling Hello, querying http://localhost:8000/hello/1/2 ... 027ms
    Compiling World, querying http://localhost:8000/world?name=$1&lname=$2 ... 019ms
    Compiling Matriochka0, querying http://localhost:8000/matriochka/1 ... 105ms
    Compiling Info, querying http://localhost:8000/info ... 009ms
    Compiling Callback, querying http://localhost:8000/callback ... 069ms

You will find the generated application in the directory *cache*.

The build process follows the routes with GET HTTP method, all other methods routes are build on web browser call.

### Using the hybrid context: PHP + JavaScript 

First, ensure NodeJS is installed and running with version 20 as a miminum. LTS/Iron is a good choice.

Once it's done, run:

    npm install
    npm run dev

If you installed the QuickStart application as said previously, you should see something like this:

    > logo@1.0.0 dev
    > sh scripts/dev.sh all

    Building web components...
    asset app.min.js 2.31 KiB [emitted] (name: main)
    runtime modules 274 bytes 1 module
    ./app/JavaScripts/index.js 212 bytes [built] [code generated]
    webpack 5.89.0 compiled successfully in 325 ms
    'app/Assets/css/app.css' -> 'public/css/app.css'
    'app/Assets/css/index.css' -> 'public/css/index.css'
    'app/Assets/css/setup.css' -> 'public/css/setup.css'
    'app/Assets/img/css/app.css' -> 'public/img/css/app.css'
    'app/Assets/img/css/index.css' -> 'public/img/css/index.css'
    'app/Assets/img/css/setup.css' -> 'public/img/css/setup.css'
    'app/Assets/img/salamandra.png' -> 'public/img/salamandra.png'
    'node_modules/human-writes/dist/web/human-writes.min.js' -> 'public/modules/human-writes.min.js'
    Compiling App ... 059ms
    Compiling Home, querying http://localhost:8000/ ... 193ms
    Compiling Hello, querying http://localhost:8000/hello/1/2 ... 027ms
    Compiling World, querying http://localhost:8000/world?name=$1&lname=$2 ... 019ms
    Compiling Matriochka0, querying http://localhost:8000/matriochka/1 ... 105ms
    Compiling Info, querying http://localhost:8000/info ... 009ms
    Compiling Callback, querying http://localhost:8000/callback ... 069ms

## The sample pages 

The available page routes are:

A simple page with 2 children components; the first passes values to the second with useState hook.
- http://localhost:8000/

A sample to show how to manage parameters passed in the URL path.
- http://localhost:8000/hello/arg1/arg2

A sample to show how to manage parameters passed in the query string with a parent component for the style.
- http://localhost:8000/world?name=arg1&lname=arg2

A sample to show how to use the same component in cascade.
- http://localhost:8000/matriochka/arg1

A sample to show PHP info
- http://localhost:8000/info

A sample to show how to make a JavaScript callback with a PHP JSON response.
- http://localhost:8000/callback
