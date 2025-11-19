# Gestión de Discos Virtuales en Linux

## Creación, Partición, Asignar formato y Montaje (Desmontaje)

### Paso a paso

- En la MV (VirtualBox), ehacemos click en la máquina con la que vamos a trabajar, en este caso será un "Ubuntu Server", luego "Configuración" del VirtualBox y finalmente en "Almacenamiento".

- Nos ubicamos sobre el "Controlador:SATA" y le damos al icono de "Add hard disc", para agregar un nuevo disco virtual (que en la realidad es un disco físico).

- En la siguiente ventana le damos click en la parte superior "Crear" y se despliega una ventana en la que vamos a **asignar** un nombre (**Disco1** por ejemplo) y tamaño de disco que para este ejemplo será de **800Mb** y hacemos click en "Terminar".

- En la parte inferior de esa ventana le damos a "Seleccionar" para que ese nuevo disco se agregue a la MV que usaremos (Ubuntu Server).

- Nuevamente en la ventana de "Almacenamiento" podemos ver que ya tenemos asignado el nuevo disco **"Disco1.vdi" (800Mb)**, hacemos click sobre este para ver sus atributos (a la derecha) y finalmente click en "aceptar".

- Ahora en nuestra MV del Ubuntu Server podemos ver claramente que tenemos un nuevo disco en el **Puerto SATA 1:**, también podemos repetir los pasos para "Crear" más discos (paso 3).

- Llegados a este punto, **arrancamos (iniciar) nuestra MV Ubuntu Server**, ingresamos "login y password" y ya en nuestra ventana de comandos (Terminal) iremos ejecutando los siguientes comandos.

```Bash
sudo dmesg
```

- El comando "sudo" otorga el nivel de "super usuario" y el comando "dmesg" muestra información sobre los discos conectados en el equipo.

```Bash
sudo dmesg | grep "sd"
```

- Cuando agregamos **"| grep "sd"** significa que pasará al comando "grep", indicando que buscamos algo más preciso "sd" que es la información sobre los discos que estén conectados a la máquina.

> **Ejemplo:** tenemos "sda" que es el principal, "sdb" sería el nuevo disco **(Disco1.vdi de 800Mb)** y así "sdc", "sdd", etc., etc., todos los siguientes discos que conectemos.

- En la terminal nos muestra información de "sda" y "sdb" que de momento son los únicos que tenemos en la máquina. Ahora pasaremos a darle una **"partición"** a ese **nuevo disco** usando el comando **"fdisk"**.

```Bash
sudo fdisk -l /dev/sdb
```

- Usando el parámetro **(-l)** nos muestra un listado de la información y volúmen del disco para tenerlo en cuenta al momento de particionar.

```Bash
sudo fdisk /dev/sdb
```

> Sin ese parámetro, ahora sí vamos a ejecutar el "particionado" del disco.

- Vemos que *"se ha realizado la operación en memoria"* y nos pide elegir una "acción", además nos presenta la opción de elegir **"m"** para tener una ayuda y elegir correctamente, precionamos "m".

- Vemos en el menú que nos muestra la opción **"n" (add a new partition - adicionar una nueva partición)**, elegimos y precionamos "n".

- Ahora nos pide el **"tipo de partición"**, es decir, si queremos particionar el total del volumen de la unidad o solo una parte (si queremos tener el disco en 2 o más unidades lógicas), para eso utilizaremos la información que obtuvimos anteriormente del disco y haríamos los cálculos necesarios.

- En ésta ocasión vamos a elegir **"p"** (Primary) que viene a ser una **partición primaria**, es decir, usaremos la totalidad del volumen del disco, **precionamos varias veces enter** para que se cojan las opciones por defecto y debemos elegir la opción **"w"** (write table to disk and exit) para guardar los cambios y salir (**"q"**, para salir sin guardar).

- Hasta este punto hemos conectado el disco, también hemos visto la información necesaria para particionar y realizamos la partición asignando el total del volumen (cuando elegimos **"p"**), ahora vamos a darle el **"formato"** adecuado para poder trabajar en él. Para esto usaremos el comando **"mkfs" (make file system)**.

```Bash
sudo mkfs
```

> Si precionamos la tecla TAB varias veces luego de escribir el comando, veremos sus diferentes opciones de formato, en nuestro caso usaremos el formato "ext4".

- Elegimos la opción **"ext4"** y el comando sería:

```Bash
sudo mkfs.ext4 /dev/sdb1
```

- Este comando inidca que se dará formato a la partición específica "sdb1" (pratición primaria) donde se creará el sistema de archivos.

- Luego de asignar el tipo de formato (sistema de archivos en Linux es **"ext4"**), podemos comprobar utilizando el comando **"blkid"** para que nos muestre más información (mapeo).

```Bash
sudo blkid
```

> Este comando da información sobre los discos y sus particiones.

- Ahora que ya conectamos, particionamos y asignamos un formato (sistema de archivos), vamos a **"montar"** el disco, esto sirve para **poder utilizarlo**. Para eso vamos a crear una carpeta primero usando el comando **"mkdir"**.

```Bash
mkdir nuevodisco
```

> Este comando crea un directorio (carpeta) llamado "nuevodisco" en la que vamos a montar nuestra partición.

```Bash
sudo mount -t auto -v /dev/sdb1 ./nuevodisco
```

- Acabamos de "montar" el disco nuevo en un directorio del sistema utilizando el comando **"mount"**, el parámetro **(-t auto)** indica a "mount" que detecte automáticamente el sistema de archivo usado **(ext4)** y el parámetro **(-v)** activa el modo verbose que muestra la información más detallada.

> **Resumen:** el comando monta la partición **/dev/sbd1** en el directorio **./nuevodisco**, detectando automáticamente el sistema de archivos y mostrando detalles del proceso.

- Ingresamos en el directorio utilizando el comando **"cd"**.

```Bash
cd nuevodisco
```

- Podemos utilizar el comando "pwd" antes para confirmar que nos encontramos dentro de ese directorio **"/home/usuario/nuevodisco"**.

- Ahora vamos a crear un archivo llamado "documento1" utilizando el comando **"touch"** de la siguiente manera.

```Bash
touch documento1
```

- Sin embargo nos muestra un error, nos dice que NO podemos crear el archivo (documento1), que **no tenemos permisos (:Permission denied)** y eso se debe a que debemos tener la condición **"sudo" (super usuario)** para hacerlo.

```Bash
sudo touch documento1
```

> Ahora si nos va a permitir hacerlo.

- Finalmente para comprobar que se ha creado el archivo, utilizamos el comando **"ls -la"** para que nos lo muestre en listado.

```Bash
ls -la
```

> Este comando lista el contenido de un directorio.

- Por último tenemos:

```Bash
id
```

> Este comando nos muestra los usuarios y grupos reales y efectivos del nuevo disco montado.

```Bash
history
```

> Este comando nos muestra un listado del historial de comandos utilizandos desde el inicio de sesión.

- Ya para terminar podemos **"desmonatar"** el disco, es decir, si queremos **no utilizarlo más** ejecutamos el comando **"umont"**, para nuestro ejercicio sería así:

```Bash
sudo umount /dev/sdb1
```

> De esta manera quedaría desmontado.
