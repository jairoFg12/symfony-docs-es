.. index::
   single: Emails; Gmail

¿Cómo utilizar Gmail para enviar Emails?
===============================

Durante el desarrollo, utilizar Gmail para enviar correos electrónicos, es más
fácil y práctico que utilizar un servidor SMTP. Con el ``bundle`` Swiftmailer esto
es realmente fácil.

.. tip::

    En vez de usar tu cuenta habitual de Gmail, se recomienda crear una cuenta
    especial para este propósito.

En el archivo de configuracion de desarrollo, asigna el valor de ``transport`` 
como ``gmail`` y escribe las credenciales ``username`` y ``password`` de 
tus cuenta de Google:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        swiftmailer:
            transport: gmail
            username:  tu_usuario_de_gmail
            password:  tu_contrasena_de_gmail

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->

        <!--
        xmlns:swiftmailer="http://symfony.com/schema/dic/swiftmailer"
        http://symfony.com/schema/dic/swiftmailer http://symfony.com/schema/dic/swiftmailer/swiftmailer-1.0.xsd
        -->

        <swiftmailer:config
            transport="gmail"
            username="tu_usuario_de_gmail"
            password="tu_contrasena_de_gmail" />

    .. code-block:: php

        // app/config/config_dev.php
        $container->loadFromExtension('swiftmailer', array(
            'transport' => "gmail",
            'username'  => "tu_usuario_de_gmail",
            'password'  => "tu_contrasena_de_gmail",
        ));

¡Y has finalizado!

.. note::

    El transporte como ``gmail`` es simplemente un atajo que utiliza el 
    transporte ``smtp`` y asigna los valores de ``encryption``, 
    ``auth_mode`` y ``host`` para trabajar con Gmail
