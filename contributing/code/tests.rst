Ejecutar Pruebas en Symfony2
============================

Antes de enviar un :doc:`patch <parche>` para su inclusión, necesitas ejecutar
la suite de pruebas de Symfony2 para verificar que no has roto nada.

PHPUnit
-------

Para ejecutar la suite de pruebas de Symfony2, primero debes `instalar`_
PHPUnit 3.5.0 o superior:

.. code-block:: bash

    $ pear channel-discover pear.phpunit.de
    $ pear channel-discover components.ez.no
    $ pear channel-discover pear.symfony-project.com
    $ pear install phpunit/PHPUnit

Dependencias (opcional)
-----------------------

Para ejecutar la suite completa de pruebas, incluyendo pruebas que tengan
dependencias externas, Symfony2 necesita ser capaz de cargarlos en forma
automática. Por defecto, las dependencias son cargadas desde el directorio
`vendor/` del directorio raíz del proyecto (vea `autoload.php.dist`).

La suite de pruebas necesita las siguientes librerías de terceros:

* Doctrine
* Doctrine Migrations
* Swiftmailer
* Twig
* Zend Framework

Para poder instalarlas, ejecuta el script `install_vendors.sh`:

.. code-block:: bash

    $ sh install_vendors.sh

.. note:: 

    Ten en cuenta que el script puede demorarse en terminar.

Después de la instalación, puedes actualizar en cualquier momento las librerías
de terceros con el script `update_vendors.sh`:

.. code-block:: bash

    $ sh update_vendors.sh

Ejecutar
--------

Primero, actualiza las librerías de terceros (ver arriba).

Entonces, ejecuta la suite de pruebas desde el directorio raíz de Symfony2
con el siguiente comando:

.. code-block:: bash

    $ phpunit

La salida debería mostrar un `OK`. Si no, necesitas averigurar que está pasando
y si las pruebas estan rotas debido a las modificaciones que has realizado.

.. tip::

    Ejecuta la suite de pruebas antes de aplicar tus modificaciones, para
    chequear que ellas funcionan bien en tu configuración inicial.

Cobertura de Código
-------------------

Si agregas una nueva característica, tienes que verificar la cobertura de
código usando la opción `coverage-html`:

.. code-block:: bash

    $ phpunit --coverage-html=cov/

Verifica la cobertura de código abriendo en un navegador el archivo
`cov/index.html` generado.

.. tip::

    La cobertura de código funciona únicamente si tienes XDebug habilitado
    y todas las dependencias instaladas.

.. _instalar: http://www.phpunit.de/manual/current/en/installation.html
