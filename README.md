CapacityTester
==============

¿Ha comprado una unidad flash USB de 128 GB (pen drive, usb stick) por 10 dólares? con el envío "gratuito" desde Asia?
Si preguntas algo, obtienes una respuesta en un mal inglés de un chino que se llama a sí mismo "Jennifer"...
Pensando en los impuestos y en los gastos de envío, empiezas a preguntarte ¿qué tan barata sera esta unidad en particular, si se compara con una de una tienda local?
Afrontémoslo: Probablemente has comprado una **unidad flash falsa**, es decir, una que en realidad tiene mucha menos capacidad de la que dice tener.

Esta herramienta puede probar una unidad USB o tarjeta de memoria para averiguar si es falsa. Por ejemplo, una falsificación puede ser vendida como "unidad USB de 64 GB"
pero sólo tendría una capacidad real de 4 GB, todo más allá de este límite
se perderá. En ese punto, la unidad flash falsa a menudo se vuelve inútil.

Esta herramienta realiza una prueba simple para determinar si la capacidad total
es utilizable o no. Todo lo que hace es llenar el volumen con datos de prueba (archivos)
y luego los verifica para saber si los datos del volumen son correctos.

El dispositivo debe estar completamente vacío (sin archivos) para que la prueba proporcione resultados fiables.

Tenga en cuenta que la prueba se ejecuta sobre un sistema de archivos existente,
que es suficiente para comprobar una unidad flash falsa. También podra usarlo para probar un disco duro viejo (borre todos los archivos o formatee el disco primero), pero esto no es para lo que esta herramienta fue creada.
Para un viejo hdd, seria mejor usar *badblocks* o alguna otra herramienta para probar la unidad en sí.

![CapacityTester GUI](screenshots/CapacityTester_GUI_1.png)

![CapacityTester GUI](screenshots/CapacityTester_GUI_2.png)

![CapacityTester CLI](screenshots/CapacityTester_CLI_1.png)


Prueba
----

Sólo los sistemas de archivos montados aparecen en la lista,
así que tiene que montar la unidad USB antes de poder probarla.
Puede usar un gestor de archivos como Caja para eso.

Aunque la prueba no es destructiva (es decir, no tocará los archivos existentes),
deberías asegurarte de eliminar cualquier archivo existente de la unidad.

Durante la fase de inicialización de la prueba,
se crean archivos temporales de prueba.
Si la unidad es defectuosa y reporta errores,
la prueba debería abortar en esta etapa.
Después de la fase de inicialización, se escribe un patrón de prueba en la unidad,
que luego se lee y se verifica.
Durante la prueba, la unidad estará completamente llena.
Si tienes un disco falso, la prueba fallará cuando los datos de prueba
escritos más allá del límite de capacidad real del dispositivo se empiezen a leer.
La prueba también debería fallar si el sispositivo se ha vuelto defectuoso.

Después de que la prueba haya terminado, los archivos temporales se eliminan automáticamente.
Sin embargo, si la unidad se desmonta o muere durante la prueba,
los archivos tendrán que ser borrados por el usuario.

El programa no detecta ni informa del tipo de falsificación,
sólo informa de un error y de dónde ha ocurrido el error.
Esto puede o no proporcionar una estimación de la capacidad real de la unidad,
pero no se dan garantías sobre la exactitud del resultado.

La prueba del dispositivo funciona con archivos sobre el sistema de archivos de la unidad,
así que no sabe dónde termina cada archivo en el disco.
Depende del sistema de archivos donde se escribe cada archivo de prueba
y cómo puede estar fragmentado.
Esto significa que el desplazamiento informado en caso de error es un desplazamiento de archivo, no una compensación física y podría ser mucho más pequeña que la capacidad real
de una unidad falsa.

Muchos sistemas de archivos complejos no permiten utilizar toda la capacidad
ya que los metadatos y la fragmentación pueden reducir la capacidad disponible
una vez que los archivos se escriben.
Por esa razón, hay un búfer, que debería ser de alrededor de 1 MB.
Algunos sistemas de archivos como FAT32 no necesitan ese buffer (SAFETY_BUFFER = 0).



Construir
-----

Este software requiere Qt 5.

Construir usando qmake:

    $ qmake
    $ make

Limpieza:

    $ make distclean

Construir sin qmake (alternativa):
Asegúrate de que la variable de entorno QTDIR apunta
a tu directorio de construcción de Qt.

    $ export QTDIR=~/Builds/Qt/5.5.1-GCC5.3.1-FEDORA22
    $ cd build
    $ make

Construir en Windows:

    $ SET QTDIR=...
    $ cd build
    $ make PLATFORM=win32-g++

compilar 32 bits :

    $ export QTDIR=... # 32-bit Qt build (-platform linux-g++-32)
    $ cd build
    $ make rebuild-32bit

Limpieza:

    $ cd build
    $ make clean



Llamado
----

Usa las bibliotecas en tu propio directorio de construcción de Qt si no están instaladas.
Si no tienes las bibliotecas Qt 5 instaladas pero tienes un build de Qt personalizado:

    $ LD_LIBRARY_PATH=~/Builds/Qt/5.5.1-GCC4.7.2-DEBIAN7/qtbase/lib \
      bin/CapacityTester

Llama con el directorio de plugins explícitamente establecido (por ejemplo, si falta el plugin xcb):

    $ export QTDIR=~/Builds/Qt/5.5.1-GCC5.3.1-FEDORA22 \
      LD_LIBRARY_PATH=$QTDIR/qtbase/lib \
      QT_PLUGIN_PATH=$QTDIR/qtbase/plugins \
      bin/CapacityTester

Si el programa aún no se inicia porque el plugin xcb no puede ser cargado,
probablemente te falte una o dos bibliotecas.
Revisa las dependencias de $QTDIR/qtbase/plugins/plataformas/libqxcb.so
e instala las bibliotecas que faltan o añádelas manualmente.

línea de comandos (no gui):

    $ export LD_LIBRARY_PATH=~/Builds/Qt/5.5.1-GCC4.7.2-DEBIAN7/qtbase/lib
    $ bin/CapacityTester -platform offscreen
    $ bin/CapacityTester -platform offscreen -list



Autor
------

Philip Seeger (philip@c0xc.net)



Licencia
-------

Por favor, vea el archivo llamado LICENSE.
