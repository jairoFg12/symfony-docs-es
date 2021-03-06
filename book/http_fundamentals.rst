.. index::
   single: Symfony2 Fundamentals

Las Especificaciones de HTTP y los Fundamentos de Symfony2
==========================================================

Para no adentrarnos mucho en Symfony2, iniciaremos discutiendo el  Protocolo de Transferencia de Hipertexto
(HTTP) - el formato de un simple mensaje de actualizacion que es usado por todos los clientes (ejemplo los 
navegadores web) y los servidores cuando se comunican unos con otros. Esto es importante porque, como 
descubiremos mas adelante, Symfony2 has sido diseado enteramente para usar HTTP - no lo reinventamos.
El producto final es un framework que, a diferencia de muchos otros, es una abstracion de los principios
funtamentales de la World Wide Web. Ya sea que nos demos cuenta o no, el Protocolo de Hipertexto (HTTP) es
algo que usamos a diario. Con Symfony2, aprenderemos a aprovecharlo y  dominarlo.

.. index::
   single: HTTP; Request-response paradigm

El Cliente envia la Solicitud 
-----------------------------

La Web esta contruida alrededor de la idea de que toda comunicacion empieza cuando un cliente realiza una
"solicitud" a el servidor. La solicitud es un simple mensaje de texto creado por el cliente en un formato
especial conocido como HTTP. A pesar de que hay diferentes tipos de clientes- navegadores web, servicios 
web, lectores RSS, etc - cada uno de ellos envia una solicitud con el mismo formato basico. Por ejemplo:

.. code-block:: text

    GET /index.html HTTP/1.1
    Host: www.example.com
    Accept: text/html
    User-Agent: Mozilla/5.0 (Linux; X11)

La primera linea de una solicitud HTTP es la mas importante (de hecho es la unica obligatoria). Esta 
contiene dos cosas/elementos la URI y el metodo HTTP. La URI (URL si esta combina con la cabezera del
host) que identifica como unica la localizacion del recurso mientras el metodo HTTP define lo que deseas
"hacer" con el recurso. En el ejemplo anterior, la localizacion del recurso es ``/index.html`` y el metodo
es GET. En otras palabras, los clientes desean recibir el recurso identificado por ``/index.html``.

Los metodos son los *verbos* de la solicitud HTTP y definen las posibles alternativas que  tienes para 
interactuar con el recurso:

* *GET*  Recupera un recurso del servidor;
* *POST* Crea un recurso en el servidor;
* *PUT*  Actualiza un recurso en el servidor;
* *DELETE* Eimina el recurso del servidor.

Con esto es mente, te podrias imaginar que una solicitud HTTP para eliminar una entrada especifica de un blog
luciria de la siguiente manera:

.. code-block:: text

    DELETE /blog/15 HTTP/1.1

.. note::

    En la actualidad hay nueve metodos HTTP definidos en la especificaciones de HTTP, pero muchos de ellos no
    son usados o soportados. En la realidad, algunos navegadores web modernos no soportan los metodos ``PUT`` 
    y ``DELETE``. Un metodo adicional que es comunmente soportado es el metodo  ``HEAD``, que pide para la 
    respuesta una solicitud identica al metodo GET, pero sin el cuerpo de respuesta.

En conjunto a esta primera linea, una solicitud HTTP generalmente contiene otras lineas de informacion conocidas
como cabeceras de la solicitud HTTP. Estas cabeceras pueden suministrar informacion adicional como el ``Host`` 
solicitado, el formato de respuesta que acepta el cliente (``Accept``) y la aplicacion que el cliente esta usando
para realizar la solicitud (``User-Agent``). Muchas otras cabeceras pueden existir y puedes encontrarlas en Wikipedia
en el articulo `List of HTTP header fields`.

El Servidor devuelve una Respuesta
----------------------------------

Luego de que el servidor ha leido la solicitud HTTP del cliente, este conoce exactamente cual recurso el cliente
ha identificado (la URI)  y lo que el cliente desea hacer hacer con el mismo (metodo HTTP). En el caso, de una
solicitud GET, el servidor prepara el recurso y lo devuelve como una respuesta HTTP:

.. code-block:: text

    HTTP/1.1 200 OK
    Date: Fri, 12 Nov 2010 12:43:38 GMT
    Server: Apache/2.2.14 (Ubuntu)
    Connection: Keep-Alive
    Content-Length: 563
    Content-Type: text/html

    <html><body>Hello Symfony2 World!</body></html>

La respuesta HTTP devuelta por el servidor a el ciente no contiene solo
el recurso solicitado (el cotenido HTML en este caso), sino que ademas contiene informacion adicional
relacionada con el recurso. Como la solicitud HTTP, la primera linea de la respuesta
es la mas importante y contiene un codigo relacionado con el estatus de la respuesta HTTP (200 en
este caso). El codigo de estatus es muy importante ya que  informa al cliente el resultado de su 
solicitud. Existen diferentes codigos de estatus HTTP para informar de las solicitudes exitosas, fallidas, 
y ademas de las solicitudes que requieren intervencion por parte del cliente (ejemplo: una redireccion).
Una lista completa de estos codigos de estatus puedes encontrarlo en el articulo de Wikipedia 
`List of HTTP status codes`.

Como la solicitud, un mensaje de respuesta HTTP podria contener puiezas adicionales
de informacion. Estos son conocidos como cabeceras HTTP y se encuentran ubicadas entre la primera linea
(el codigo de estatus) y el contenido de la respuesta.

Una de las cabeceras de respuesta HTTP mas importante es la de ``Content-Type``. El cuerpo del 
recurso puede ser devuelto en multiples y diferentes formatos incluyendo HTML,
XML, or JSON por nombrar algunos. La cabecera ``Content-Type`` le indica al cliente el formato en el
que sera devuelta la respuesta.

Como ya visto, existen otras cabeceras. Muchas de ellas son realmente funcionales y pueden
ser usadas, por ejemplo, para manejar/contruir y poderoso sistema de cache.

HTTP and Client-Server Communication
------------------------------------

This request-response exchange is the fundamental process that drives all
communication on the World Wide Web. And as important and powerful as this
process is, it's inescapably simple. In fact, the rapid client-server communicationwhich asks
    for the response of an identical GET request, but without the response
    body.
mirrors the way in which we send and receive email messages everyday. HTTP
is simply a commonly-understood language for these messages so that a disparate
set of applications and machines can communicate.

But why is a book about Symfony going to such lengths to explain requests,
responses, and the HTTP messaging format? Regardless of the framework you
choose, the type of application you build (web, mobile, JSON API), or the
development philosophy you follow, the end goal of the server is *always*
to understand each request and create and return the appropriate response.
Symfony is architected to match this reality.

.. tip::

    To learn more about the HTTP specification, we highly recommend reading
    the original `HTTP 1.1 RFC`_ or the `HTTP Bis`_, which is an active
    effort to clarify the original specification. A great tool to check
    both the request and response headers while browsing is the `Live HTTP Headers`_
    extension for Firefox.

.. index::
   single: Symfony2 Fundamentals; Requests and responses

Requests and Responses in Symfony
---------------------------------

PHP comes packaged with an array of variables and methods that allow the developer
to understand each request and send a response. For request information,
PHP prepares superglobal variables such as ``$_SERVER`` and ``$_GET``.
Recall that each raw request is simply an HTTP-formatted block of text.which asks
    for the response of an identical GET request, but without the response
    body.
The transformation of the request message into the superglobal variables
is done behind the scenes by PHP and your web server. The end result is that
the request message information is now available in PHP, but as a scattered
collection of different superglobals.

As object-oriented developers, we need a better (object-oriented) way to
access our request information. Symfony provides a ``Request`` class for
just that purpose. The ``Request`` class is simply an object-oriented
representation of an HTTP request message. With it, you have all the
request information at your fingertips::

    use Symfony\Component\HttpFoundation\Request;

    $request = Request::createFromGlobals();

    // the URI being requested ((e.g. /about) minus any query parameters
    $request->getPathInfo();

    // retrieve GET and POST variables respectively
    $request->query->get('foo');
    $request->request->get('bar');

    // retrieves an instance of UploadedFile identified by foo
    $request->files->get('foo');

    $request->getMethod();          // GET, POST, PUT, DELETE, HEAD
    $request->getLanguages();       // an array of accepted languages

The ``getPathInfo()`` method is especially important as it returns the URI
being requested relative to your application. For example, suppose an
application is being executed from the ``foo`` subdirectory of a server. In
that case::

    // http://example.com/foo/index.php/bar
    $request->getPathInfo();  // returns "bar"

Symfony also provides a ``Response`` class, which is simply a PHP abstraction
of the raw HTTP response message. This allows your application to use an
object-oriented interface to construct response that needs to be returned
to the client::which asks
    for the response of an identical GET request, but without the response
    body.

    use Symfony\Component\HttpFoundation\Response;
    $response = new Response();

    $response->setContent('<html><body><h1>Hello world!</h1></body></html>');
    $response->setStatusCode(200);
    $response->headers->set('Content-Type', 'text/html');

    // echos the headers followed by the content
    $response->send();

At this point, if Symfony did nothing else, you would already have a
framework for accessing request information and an object-oriented
interface for creating the response. Symfony provides you with a rich toolset,
without obscuring the reality that *the end goal of any web application is
to process an HTTP request and return the appropriate HTTP response based on
the application-specific business logic*. Even as we discuss the many features
in Symfony, this goal will remain fundamental and transparent.

.. tip::

    The ``Request`` and ``Response`` classes are part of a standalone component
    included with Symfony called ``HttpFoundation``. This component can be
    used entirely independent of Symfony and also provides classes for handling
    sessions and file uploads.

The Journey from the Request to the Response
--------------------------------------------

We know now that the end goal of any application is to use the HTTP
request to create and return the appropriate HTTP response. Symfony provides
``Request`` and ``Response`` classes that allow this to be done through
an object-oriented interface. So far, we're only leveraging a small
piece of Symfony. But we already have the tools to write a simple application!
Let's dive in:

.. code-block:: php

    $request = Request::createFromGlobals();
    $path = $request->getPathInfo(); // the URL being requested
    $method = $request->getMethod();

    if (in_array($path, array('', '/') && $method == 'GET') {
        $response = new Response('Welcome to the homepage.');
    } elseif ($path == '/about' && $method == 'GET') {
        $response = new Response('About us');
    } else {
        $response = new Response('Page not found.', 404);
    }
    $response->send();

In this simple example, the application correctly processes the request and
returns an appropriate response. From a very technical standpoint, our
application does exactly what it should.

An Application without a Framework
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

But what if the application needs to grow? Imagine this same application if it
were now forced to handle hundreds or even thousands of different pages! In
order to keep things maintainable (i.e. not all in one file), we'd need to do
some reorganization. For starters, we might move the work of creating the
``Response`` into a set of different functions. These functions are commonly
known as *controllers* and allow us to further organize our code::

    if (in_array($path, array('', '/')) && $method == 'GET') {
        $response = main_controller($request);
    } elseif ($path == '/about' && $method == 'GET') {
        $response = about_controller($request);
    } else {
        $response = error404_controller($request);
    }

    function main_controller(Request $request)
    {
        return new Response('Welcome to the homepage.');
    }

    function about_controller(Request $request)
    {
        return new Response('About us');
    }

    function error404_controller(Request $request)
    {
        return new Response('Page not found.', 404);
    }

Next, our growing application still contains a long ``if`` ``elseif`` block
that routes the creation of the ``Response`` object to a different controller
(i.e. PHP method). We might consider building a configuration-based routing
system that maps each request to a specific controller based on the URI and
HTTP method of the request.

Obvious or not, the application is beginning to spin out of control. Recall
that the goal of any application is to apply the custom application logic and
information from the request to create an appropriate response. In our
application, these proposed changes are **not** to the business logic. Instead,
the necessary refactoring means inventing a system of controllers and a custom
routing system. As we continue development, we'll inevitably spend some time
developing our application and some time developing and enhancing the framework
around it.

We need a better solution - one where the developer spends his/her time developing
the application logic for creating ``Response`` objects instead of on so many
low-level details.

The Symfony framework does just this by allowing you to focus on your most
valuable deliverables without sacrificing the power and organization of a
framework. Of course, a popular framework like Symfony comes with a long
list of "bonuses" such as free maintenance, documentation, standardization,
and a community-driven group of open source bundles (i.e. plugins) available
for use.

.. index::
   single: Symfony2 Fundamentals; The Kernel
   single: Kernel; Introduction

Introducing the Symfony Kernel
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Symfony is based around a ``Kernel`` object whose single responsibility is to facilitate
the journey from the ``Request`` object to the final ``Response`` object.
The ``Kernel`` is what handles each request and actually executes your application
code.

The "application code" executed by the ``Kernel`` is called a "controller",
a special term for what's actually a basic PHP callable (most commonly,
an object method). The controller is where your application code lives -
it's where you create the final ``Response`` object. The ``Kernel`` works by
determining and then calling a "Controller" for each request:

.. code-block:: text

    Request -> Kernel::handle() -> Controller (your code) -> Response (returned by controller)

Our original sample application could be refactored into two "controllers",
which, in this example, are PHP methods in some ``myController`` class.
The code needed to determine and execute these controllers is isolated
elsewhere and handled by the ``Kernel``::

    class myController
    {
        public function homepageAction()
        {
            return new Response('Welcome to the homepage.');
        }

        public function aboutAction()
        {
            return new Response('About us');
        }
    }

.. tip::

    Notice that each controller returns a ``Response`` object. This is the
    basic job of your controllers: to apply complex business logic and
    ultimately construct and return the final ``Response``.

But how does the ``Kernel`` know which controller to call for each request?
Though this process is entirely configurable, Symfony2 integrates a ``Router``
that uses a "map" to connect path info from the ``Request`` to a specific
controller.

.. code-block:: text

    Request -> Kernel::handle() -> Controller -> Response
                        |    ^
                        | controller
                        |    |
                        v    |
                        Routing

We'll talk a lot more about :doc:`Controllers </book/controller>` and the
:doc:`Router </book/routing>` in later chapters.

.. tip::

    The ``Kernel`` class is part of a standalone component used by Symfony2
    called ``HttpKernel``. This component provides functionality related to
    Bundles, Security, Caching and more. The ``Router`` is also part of a
    standalone component called ``Routing``.

.. index::
   single: Symfony2 Components

Symfony2 *Components* versus the Symfony2 *Framework*?
------------------------------------------------------

By now, we've seen the most basic components that make up the Symfony2 framework.
In reality, everything we've talked about so far (the ``Request``, ``Response``,
``Kernel`` and ``Router``) lives in three different standalone components
used by Symfony. In fact, each feature in Symfony2 belongs to one of over
twenty independent libraries (called the "Symfony Components")! Even if you
decided to build your own PHP framework (an unwise idea), you could use the
Symfony Components as the building blocks for many layers of functionality.
And if you do use Symfony2, but need to replace a component entirely, you have
the ability to do that. Symfony2 is decoupled and relies on interface-driven
dependency injection. In other words, the developer has complete control.

So then, what *is* the Symfony2 **Framework**? The *Symfony2 Framework* is
a PHP framework that accomplishes two distinct tasks:

#. Provides a selection of components (i.e. the Symfony2 Components) and
   third-party libraries.

#. Provides sensible configuration that nicely ties everything together.

The goal of the framework is to integrate many independent tools in order
to provide a consistent experience for the developer. Even the framework
itself is a Symfony2 bundle that can be configured or replaced entirely.

Basically, Symfony2 provides a powerful set of tools for rapidly developing
web applications without imposing on your application. Normal users can
quickly start development by using a Symfony2 distribution, which provides
a project skeleton with sensible defaults. For more advanced users, the sky
is the limit.

.. _`HTTP 1.1 RFC`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
.. _`HTTP Bis`: http://datatracker.ietf.org/wg/httpbis/
.. _`Live HTTP Headers`: https://addons.mozilla.org/en-US/firefox/addon/3829/
.. _`List of HTTP status codes`: http://en.wikipedia.org/wiki/List_of_HTTP_status_codes
.. _`List of HTTP header fields`: http://en.wikipedia.org/wiki/List_of_HTTP_header_fields
