La Vista
========

Después de ver la primera parte del tutorial, has decidido que vale la pena 
otros 10 minutos con Symfony2. Excelente. En esta segunda parte, aprenderás 
mas sobre el motor de plantillas de Symfony2, `Twig`_. Twig es un flexible, 
rápido y seguro motor de plantillas para PHP que hace a tus plantillas más 
legibles y concisas, haciéndolas también más amigables para los diseñadores web.

.. note::

    En la lugar de Tiwig, también puedes usar :doc:`PHP </cookbook/templating/PHP>` 
    para tus plantillas. Ambos motores son soportados por Symfony2 y tienen el 
    mismo nivel de soporte.

.. index::
   single: Twig
   single: View; Twig

Twig, un vistazo rápido
----------------------

.. tip::

    Si quieres aprender a usar Twig, recomendamos ampliamente leer su 
    `documentación`_ oficial. Esta sección es solo una vista general de los conceptos 
    principales.

Una plantilla Twig es un archivo de texto que puede generar cualquier formato 
basado en texto (HTML, XML, CSV, LaTeX, ...). Existen dos tipos de delimitadores:

* ``{{ ... }}``: Imprime una variable o el resultado de una expresión;

* ``{% ... %}``: Controla la logica de la plantilla; es usado para ejecutar ciclos o sentencias condicionales.

Abajo puedes encontrar una plantilla bien simple para mostrar las características básicas:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <title>My Webpage</title>
        </head>
        <body>
            <h1>{{ page_title }}</h1>

            <ul id="navigation">
                {% for item in navigation %}
                    <li><a href="{{ item.href }}">{{ item.caption }}</a></li>
                {% endfor %}
            </ul>
        </body>
    </html>

Las variables pasadas a la plantilla pueden ser cadenas, matrices o incluso 
objetos. Twig abstrae las diferencias entre ellos y permite acceder a "atributos" 
de una variable por medio del operador punto (``.``):

.. code-block:: jinja

    {# array('name' => 'Fabien') #}
    {{ name }}

    {# array('user' => array('name' => 'Fabien')) #}
    {{ user.name }}

    {# force array lookup #}
    {{ user['name'] }}

    {# array('user' => new User('Fabien')) #}
    {{ user.name }}
    {{ user.getName }}

    {# force method name lookup #}
    {{ user.name() }}
    {{ user.getName() }}

    {# pass arguments to a method #}
    {{ user.date('Y-m-d') }}

.. note::

    Es importante notar que las llaves no forman parte de la variable sino de 
    la impresión de la misma. Si necesitas acceder a variables dentro de 
    etiquetas, no uses las llaves.

Decorar plantillas
--------------------

Por lo general, las plantillas de un proyecto comparten elementos comunes, como 
los conocidas cabeceras y pies de página. Con Symfony2 nos gusta pensar en 
forma diferente sobre este problema: una plantilla puede ser decorada por otra. Esto 
funciona de la misma manera que las clases PHP. La herencia de plantillas permite 
construir una plantilla base denominada "layout" que contiene todos los elementos 
comunes del sitio y define bloques para que los hijos los puedan sobrescribir.

La plantilla ``index.html.twig`` hereda de ``layout.html.twig`` gracias a la etiqueta ``extends``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

La notación ``HelloBundle::layout.html.twig`` suena familiar cierto? Es la misma 
notación utilizada para referenciar a plantillas regulares. Los ``::`` significan que 
el elemento controlador esta vacío, por lo que el archivo correspondiente se 
encuentra almacenado dentro de ``views/``.

Demos un vistazo a la plantilla simplificada ``layout.html.twig``:

.. code-block:: jinja

    {% extends "::base.html.twig" %}

    {% block body %}
        <h1>Hello Application</h1>

        {% block content %}{% endblock %}
    {% endblock %}

La etiqueta {% block %} define dos bloques(body y content) para que los hijos 
lo puedan rellenar. Todo lo que hace una etiqueta de tipo bloque es decirle al motor 
de plantillas que un hijo puede sobreescribir esa porción. La plantilla 
hello.html.twig sobreescribe el bloque con nombre --content--. El otro bloque está 
definido en una plantilla base ---defined in a base layout as the layout is itself decorated by another one---. ---Cuando en el nombre lógico, la parte correspondiente al bundle esta vacía--- (::base.html.twig), las vistas son buscadas dentro de la carpeta app/Resources/. Esta carpeta almacena vistas globales para el proyecto completo.
The ``{% block %}`` tags define two blocks (``body`` and ``content``) that
child templates can fill in. All the block tag does is to tell the template
engine that a child template may override those portions of the template. The
``index.html.twig`` template overrides the ``content`` block. The other one is
defined in a base layout as the layout is itself decorated by another one.
When the bundle part of the template name is empty (``::base.html.twig``),
views are looked for in the ``app/views/`` directory. This directory store
global views for your entire project:

.. code-block:: jinja

    {# app/views/base.html.twig #}
    <!DOCTYPE html>
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
            <title>{% block title %}Hello Application{% endblock %}</title>
        </head>
        <body>
            {% block body '' %}
        </body>
    </html>

Usar Etiquetas, Filtros y Funciones
----------------------------

Una de las mejores funcionalidades de Twig es la extensibilidad para trabajar con 
etiquetas, filtros y funciones. ---Symfony2 contiene varios propios para facilitar 
el trabajo del diseñador---.
One of the best feature of Twig is its extensibility via tags, filters, and
functions; Symfony2 comes bundled with many built-in ones to ease the web
designer work.

Incluir otros templates
~~~~~~~~~~~~~~~~~~~~~~~~~

La mejor forma de compartir un fragmento de código entre varias plantillas 
diferentes es definir una plantilla que pueda ser incluída dentro de otra.

Crea una plantilla con el nombre ``hello.html.twig``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/hello.html.twig #}
    Hello {{ name }}

Y cambia la plantilla ``index.html.twig`` para que la incluya:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {# override the body block from index.html.twig #}
    {% block body %}
        {% include "HelloBundle:Hello:hello.html.twig" %}
    {% endblock %}

Incluir otros controladores
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Y si la necesidad es incluir el resultado de otro controlador dentro de la 
plantilla? Esto es muy útil cuando trabajamos con Ajax o cuando el template 
incluido necesita algunas variables que no existen en la plantilla principal.

Si creas una acción con nombre ``fancy``, y quieres utilizarlo dentro de la 
plantilla ``index``, puedes usar la etiqueta ``render``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% render "HelloBundle:Hello:fancy" with { 'name': name, 'color': 'green' } %}

La cadena ``HelloBundle:Hello:fancy`` hace referencia a la acción ``fancy``
del controlador ---Demo--- ---and the argument is used as simulated request path values---:
Here, the ``HelloBundle:Hello:fancy`` string refers to the ``fancy`` action of
the ``Hello`` controller, and the argument is used as simulated request path
values::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    class HelloController extends Controller
    {
        public function fancyAction($name, $color)
        {
            // create some object, based on the $color variable
            $object = ...;

            return $this->render('HelloBundle:Hello:fancy.html.twig', array('name' => $name, 'object' => $object));
        }

        // ...
    }

Crear vínculos entre páginas
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cuando hablamos de aplicaciones web, la creación de vínculos entre páginas 
es una necesidad. En lugar de escribir en duro las URLs dentro de la plantilla, 
la función ``path`` sabe como generar URLs de acuerdo a la configuración de 
ruteo, haciendo que todas las URLs puedan ser fácilmente actualizadas modificando 
la configuración:

.. code-block:: jinja

    <a href="{{ path('hello', { 'name': 'Thomas' }) }}">Greet Thomas!</a>

La función ``path`` toma el nombre de la ruta y un array de parámetros 
como argumentos. El nombre de la ruta es la clave principal por la que las 
rutas son referenciadas y los parámetros son los valores de los ---placeholders--- definidos 
en la ruta:
The ``path`` function takes the route name and an array of parameters as
arguments. The route name is the main key under which routes are referenced
and the parameters are the values of the placeholders defined in the route
pattern:

.. code-block:: yaml

    # src/Sensio/HelloBundle/Resources/config/routing.yml
    hello: # The route name
        pattern:  /hello/{name}
        defaults: { _controller: HelloBundle:Hello:index }

.. tip::

    La función ``url`` genera URLs *absolutas* {{ url('hello', { 'name': 'Thomas' }) }}

Incluir Recursos: imágenes, JavaScripts, y hojas de estilo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

¿Qué sería Internet sin imágenes, JavaScripts, y hojas de estilo? Symfony2 
provee la función ``asset`` para lidiar con esto fácilmente:

.. code-block:: jinja

    <link href="{{ asset('css/blog.css') }}" rel="stylesheet" type="text/css" />

    <img src="{{ asset('images/logo.png') }}" />

El principal propósito de la función ``asset`` es hacer la aplicación más 
portable. Gracias a esta función puedes mover el directorio raíz de tu aplicación 
a cualquier lugar dentro del directorio raíz de tu servidor web sin tener que 
cambiar nada en el código de tu plantilla.

Escapar salidas
---------------

Twig está configurado para escapar automáticamente todas las salidas por 
omisión. Lee la `documentation`_ de Twig para aprender más sobre como escapar 
salidas y la extensión ---Escaper---
Twig is configured to automatically escapes all output by default. Read Twig
`documentation`_ to learn more about output escaping and the Escaper
extension.

Pensamientos finales
--------------

Twig es simple pero poderoso. Gracias a las ---layout---(plantillas?), bloques, 
---templates---(plantillas?) y la inclusión de acciones, es muy sencillo organizar 
tus plantillas de una forma lógica y extensible.
Twig is simple yet powerful. Thanks to layouts, blocks, templates and action
inclusions, it is very easy to organize your templates in a logical and
extensible way.

Haz trabajado con Symfony2 por nada más que 20 minutos y ya puedes crear 
varias cosas interensates. Este es el poder de Symfony2. Aprender la parte 
básica es sencilla y pronto te darás cuenta que la simplicidad esta escondida bajo 
una arquitectura muy flexible.
You have only been working with Symfony2 for about 20 minutes, and you can
already do pretty amazing stuff with it. That's the power of Symfony2. Learning
the basics is easy, and you will soon learn that this simplicity is hidden
under a very flexible architecture.

---Pero no nos adelantemos---. Primero necesitas aprender más sobre los 
controladores y es exactamente de esto de lo que hablamos en la próxima parte 
de este tutorial. Preparado para otros 10 minutos con Symfony2?
But I get ahead of myself. First, you need to learn more about the controller
and that's exactly the topic of the next part of this tutorial. Ready for
another 10 minutes with Symfony2?

.. _Twig:          http://www.twig-project.org/
.. _documentation: http://www.twig-project.org/documentation
