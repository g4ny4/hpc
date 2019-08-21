
# Ejecutar StarPu en un clúster dentro de una red de area local (LAN)

El presente tutorial fue realizado en maquinas con Ubuntu, pero esta pensado para ejecutarce en cualquier ambiente Linux con ligeros cambios en los comandos

---

# Tabla de contenidos

> El tutorial se divide en 5 etapas

- [Instalación y configuración de OpenMPI](#1)
- [Instalación y configuración de StarPu](#2)
- [Configuración de computadoras en red de area local](#3)
- [Ejecución de programas con herramienta OpenMPI en cluster](#4)
- [Ejecución de programas con herramienta StarPu en cluster](#5)
- [Equipo](#team)
- [FAQ](#faq)

---

# 1. Instalación OpenMPI desde la fuente

A continuación se explica como instalar OpenMPI desde un archivo fuente y con herramientas GNU.

> Esta guía se instala y configura la version 4.0.1 de OpenMPI

## Requisitos previos
1. 	Es necesario tener instalado un compilador de C/C++ ANTES de realizar la instalación de OpenMPI.
2. 	Es necesaria la herramienta Make.

## URL's de interes:
OpenMPI (URL fuente)

## Preparación 

Descargar OpenMPI

```shell
$ wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.1.tar.gz
```

Descomprimir el archivo openmpi-x.x.x.tar.gz (Cambiar x.x.x por la versión descargada).
```shell
$ sudo tar -xvf openmpi-4.0.1.tar.gz
```

Acceder a la carpeta que genera la descompresión. (Cambiar x.x.x por la versión descargada)
```shell
cd openmpi-4.0.1
```

## Configuración
Configuramos el archivo de instalación (haciendo uso de los privilegios de superusuario correspondientes a su sistema operativo, no confundir con root), esto tarda entre 5 y 10 minutos (dependiendo del ordenador). Es necesario añadir en el prefijo el directorio de instalación que queremos para OpenMPI. Lo normal sería selecciónar de directorio de instalación "/home/$USER/.openmpi", aunque en esta guía lo instalaremos sobre /opt
```shell
$ sudo ./configure --prefix="/opt/openmpi"
```

## Compilación
Se realiza la compilación, para ello es necesario la herramienta Make
```shell
$ sudo make 
```
## Instalación

```shell
$ sudo make install
```

## Exportar variables

Para que la exportación de variables perdure para las siguientes sesiones y terminales, es necesario colocar las variables de entorno en el archivo .bashrc (para los usuarios de bash). Por defecto será "/home/<usuario>/.bashrc".
```shell
$ echo '# Variables OpenMPI' >> /home/$USER/.bashrc
$ echo 'export PATH="$PATH:/opt/openmpi/bin"' >> /home/$USER/.bashrc ;
$ echo 'export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/opt/openmpi/lib/"' >> /home/$USER/.bashrc
```
```shell
$ source ~/.bashrc
```

## Prueba

Si todo ha ido bien, cuando ejecutemos mpirun nos desplegará la ayuda de uso, al igual que al hacer mpicc.

```shell
$ mpirun --version

mpirun (Open MPI) 4.0.1
Report bugs to http://www.open-mpi.org/community/help/
```



# 2. Instalación StarPu desde la fuente

A continuación se explica como instalar StarPu desde un archivo fuente y con herramientas GNU.

> Esta guía se instala y configura la version 1.3.1 de StarPU

## Requisitos previos
1. 	Es necesario tener instalado un compilador de C/C++ ANTES de realizar la instalación de StarPu.
2. 	Es necesaria la herramienta Make.

## URL's de interes:
StarPu http://starpu.gforge.inria.fr/
Versiones de StarPu http://starpu.gforge.inria.fr/files/
Handbook: http://starpu.gforge.inria.fr/doc/html/index.html

## Dependencias opcionales

La biblioteca de descubrimiento de topología hwloc (http://www.open-mpi.org/software/hwloc) no es obligatoria para usar StarPU, pero se recomienda encarecidamente. Permite la planificación consciente de la topología, lo que mejora el rendimiento. 
libhwloc está disponible en las principales distribuciones gratuitas de sistemas operativos y para la mayoría de los sistemas operativos.

Si libhwloc está instalado en una ubicación estándar, no se requiere ninguna opción, se detectará automáticamente, de lo contrario, se debe usar --with-hwloc=<directory> para especificar su ubicación.

Para instalar libhwloc ejecute el siguiente comando

```shell
$ sudo apt install libhwloc-dev
```
Si libhwloc no está disponible en su sistema, la opción --without-hwloc debe darse explícitamente al llamar al script de configuración.

En la práctica tambien se instaló libtool-bin, aunque no está especificada en el manual de instalación de StarPu. 

```shell
$ sudo apt install libtool-bin
```
## Preparación 

> Recomendación: instalar como root

```shell
$ sudo su -
```

Cambiar de directorio
```shell
$ cd /opt
```
Los archivos fuentes de StarPU se pueden obtener de la página de descarga del sitio web de StarPU (http://starpu.gforge.inria.fr/files/).

```shell
$ wget http://starpu.gforge.inria.fr/files/starpu-1.3.1/starpu-1.3.1.tar.gz
```

Descomprimir
```shell
$ tar -xvf starpu-1.3.1
```

## Configuración

Acceder al directorio generado por la descompresión del archivo descargado

```shell
$ cd starpu-1.3.1
```

Se debe configurar StarPU. Los detalles sobre las opciones que son útiles para configurar se dan en Configuración de compilación. http://starpu.gforge.inria.fr/doc/html/CompilationConfiguration.htm, como por ejemplo, deshabilitar CUDA

```shell
$ ./configure
o
$ ./configure --disable-cuda
```

## Compilación

```shell
$ make
```

Una vez que todo está compilado, es recomendable verificar el resultado. StarPU proporciona un amplio conjunto de pruebas de regresión. La ejecución de las pruebas se realiza llamando a make check. Estas pruebas se ejecutan todas las noches y el resultado del perfil principal está disponible públicamente (http://starpu.gforge.inria.fr/testing/).

```shell
$ make check
```

## Instalación

Para instalar StarPU en la ubicación que se especificó durante la configuración, ejecutar:

```shell
$ make install
```
Una vez terminada la instalación, ya no es necesario continuar como root.

```shell
$ logout
```

## Exportar variables

Para que la exportación de variables perdure para las siguientes sesiones y terminales, es necesario colocar las variables de entorno en el archivo .bashrc (para los usuarios de bash). Por defecto será "/home/<usuario>/.bashrc".

```shell
$ echo '# Variables StarPu' >> /home/$USER/.bashrc
$ echo 'export PATH="$PATH:$STARPU_PATH/bin"' >> /home/$USER/.bashrc ;
$ echo 'export LD_LIBRARY_PATH="$STARPU_PATH/lib:$LD_LIBRARY_PATH"' >> /home/$USER/.bashrc ;
```

Cargar el archivo .bashrc en la terminal actual, esto carga las variables que acabamos de escribir en el archivo .bashrc

```shell
$ source ~/.bashrc
```

## Prueba

```shell
$ starpu_machine_display

$ STARPU_SCHED=dmdas ./examples/cholesky/cholesky_implicit -size$((960*40)) -nblocks 40
```
## OTROS (tal vez no necesarios)

Install fxt-tools deb package:  
$ sudo apt install fxt-tools  
$ sudo apt install ocl-icd-opencl-dev  
$ sudo apt install libtool-bin  

# 3. Configuración de computadoras en red de area local

Para poder ejecutar StarPu y OpenMPI de forma distribuida, es necesario configurar las computadoras en una red de area local.


## Requisitos previos

## URL's de interes:
https://mpitutorial.com/tutorials/running-an-mpi-cluster-within-a-lan/  
http://www.linuxproblem.org/art_9.html


## Configura tu archivo de hosts
Para comunicarse con otras computadoras, es necesario conocer la ip de cada nodo, sin embargo para para facilitar la tarea, vamos a asignar un alias a los distintos nodos de la red con los que deseamos comunicarnos. Esta información se introduce en el archivo '/etc/hosts' del sistema operativo de cada nodo.

Se puede obtener la ip de cada nodo, usar el comando 'ip addr show'. Es necesario hace lo mismo para cada nodo de la red.

```shell
$ ip addr show
```
Ip del nodo 192.168.0.15

 Ahora se debe decidir cual será el nodo servidor o maestro, que es quien ejecutará el programa, los demás nodos, sólo realizarán las instrucciones que les manda ejecutar el nodo maestro.

 Una vez obtenida la lista de todas las IP's de los nodos de la red y que además ya se decidió quien es el nodo maestro, es necesario agregar esta información en cada computadora dentro del archivo 'hosts'.

```shell
$ sudo nano /etc/hosts
```
Agregar al final del archivo la lista de los nodos, indicando cual es el maestro y cuales son los nodos esclavos.

```shell
# información de nodos 
192.168.0.10	master
192.168.0.21	slave1
192.168.0.22	slave2
192.168.0.23	slave3
```

## Crea un nuevo usuario
Aunque se puede operar el clúster con la cuenta de usuario actual, es recomendable crear una nueva cuenta de usuario que sea idéntica en todos los nodos de la red. Es muy importante que el nombre de la cuenta creada en cada nodo sea la misma.

> En esta guía usaremos el nombre de usuario 'hpc'

```shell
$ sudo adduser hpc
```
Siga las instrucciones de consola para crear el nuevo usuario.

> Importante: No utilice el comando useradd para crear un nuevo usuario, ya que este comando no crea un directorio '/home' separado de los demás.

Para realizar configuraciones extra en cada usuario, es necesario agregarlo al grupo de usuarios 'sudoers', sin embargo una vez finalizadas las configuraciones, se recomienda eliminar al usuario del grupo de 'sudoers'

```shell
$ usermod -aG sudo hpc
```
Ahora es momento de cambiar a la cuenta del nuevo usuario para realizar las configuraciones correspondientes

```shell
$ su - hpc
```

## Configurar las variables de ambiente para el usuario creado
Al igual que en el momento de la instalación de OpenMPI, es necesario definir las variables de ambiente.

>La configuracion de variables de ambiente se debe realizar dentro de la sesión del nuevo usuario creado 'hpc' y para cada nodo del cluster.

Para que la exportación de variables perdure para las siguientes sesiones y terminales, es necesario colocar las variables de entorno en el archivo .bashrc (para los usuarios de bash). Por defecto será "/home/<usuario>/.bashrc".
```shell
$ echo '# Variables OpenMPI' >> /home/$USER/.bashrc
$ echo 'export PATH="$PATH:/opt/openmpi/bin"' >> /home/$USER/.bashrc ;
$ echo 'export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/opt/openmpi/lib/"' >> /home/$USER/.bashrc
```

Cargar el archivo .bashrc en la terminal actual, esto carga las variables que acabamos de escribir en el archivo .bashrc

```shell
$ source ~/.bashrc
```

## Configurar SSH
Cada nodo se comunicará con el maestro y el maestro con sus esclavos a traves de SSH y compartirán datos a través de NFS, del cual hablaremos un poco más tarde.

> La configuracion de SSH se debe realizar dentro de la sesión del nuevo usuario creado 'hpc' y para cada nodo del cluster.

Instalar 'openssh-server' en cada nodo.

```shell
$ sudo apt update
$ sudo apt install openssh-server 
```
> La comunicación por omisión de ssh se realiza por el puerto 22, sin embargo si tiene algun problema de puerto, pude definir manualmente el puerto de comunicación de ssh 'sudo ufw allow 22'

Como el servidor ssh ya está instalado, ahora puede iniciar sesión los otros nodos, mediante el comando '$ ssh machineName' (sustituir machineName por master, slave1, ...), el cual le pedirá que ingrese la contraseña del usuario 'hpc'. Sin embargo para poder realizar la ejecución de OpenMPI y StarPu, es necesario permitir un inicio de sesión más fácil, esto se logra con las llaves publicas RSA 

Generamos las claves RSA. 

```shell
$ ssh-keygen -t rsa
```
Dar enter en todas las opciones para generar las llaves RSA.

Ahora se debe agregar la llave pública a los nodos de la red donde deseamos un inicio de sesión sin contraseña.

### Conectar via ssh nodo maestro a esclavos

> Este paso sólo lo realiza el nodo maestro.

El nodo maestro, debe poder acceder a todos los clientes o esclavos. se deben ejecutar los siguientes comandos con la información para cada nodo esclavo.

```shell
$ ssh slave1 mkdir -p .ssh;
$ cat .ssh/id_rsa.pub | ssh slave1 'cat >> .ssh/authorized_keys'
```
```shell
$ ssh slave2 mkdir -p .ssh;
$ cat .ssh/id_rsa.pub | ssh slave2 'cat >> .ssh/authorized_keys'
```
### Conectar via ssh nodos esclavos a maestro

> Este paso lo debe realizar cada nodo esclavo.

Los nodos esclavos, deben poder acceder al nodo maestro. Se deben ejecutar los siguientes comandos en cada nodo esclavo.

```shell
$ ssh master mkdir -p .ssh;
$ cat .ssh/id_rsa.pub | ssh master 'cat >> .ssh/authorized_keys'
```

Ahora, suponiendo que haya agregado correctamente las llaves publicas en los otros nodos, el nodo maestro debe poder iniciar sesión en cualquier nodo esclavo sin solicitar una contraseña.

```shell
$ ssh slave1
```
Y los nodos esclavos deben poder iniciar sesión sin contraseña en el nodo maestro.
```shell
$ ssh master
```
> Nota: Se supone que todas las cuentas creadas fueron realizadas con el mismo nombre de usuario, de lo contrario, la conexión sin contraseña no funcionará.

## configurar servidor NFS

> Los siguientes pasos solo se realizan en el nodo maestro

Para compartir archivos, el nodo maestro creará un servidor NFS. Primero es necesario instalarlo

```shell
$ sudo apt update
$ sudo apt install nfs-kernel-server 
```

Crear la carpeta común para intercambiar archivos

```shell
$ mkdir cloud 
```
Ahora, es necesario exportar el directorio 'cloud', para ello se debe crear una entrada en el archivo '/etc/exports'

```shell
$ echo '/home/hpc/cloud * (rw,sync,no_root_squash,no_subtree_check)' >> /etc/exports
```
El * indica que esta carpeta puede ser compartida con cualquier computadora. Si desea limitar la compartición, es necesario agregar la direccion IP o una mascara IP.

Los permisos definidos:
- rw : Esto es para habilitar las opciónes de lectura y escritura. ro es para solo lectura.
- sincronización : aplica los cambios en el directorio compartido solo después de confirmar los cambios.
- no_subtree_check : esta opción impide la comprobación del subárbol. Cuando un directorio compartido es el subdirectorio de un sistema de archivos más grande, nfs realiza exploraciónes de cada directorio por encima de él, para verificar sus permisos y detalles. Deshabilitar la comprobación de subárbol puede aumentar la confiabilidad de NFS, pero reduce la seguridad.
- no_root_squash : esto permite que la cuenta raíz se conecte a la carpeta.

Después de haber agregado la carpeta a compartir en /etc/exports, es necesario ejecutar

```shell
$ exportfs -a 
```

Este comando se ejecutará cada vez que realice un cambio en '/etc/exports'

Se recomienda reiniciar el servidor NFS

```shell
$ sudo service nfs-kernel-server restart 
```

## configurar los clientes para NFS

> Los siguientes pasos sólo se realizan los nodos esclavos.

Instalar los paquetes requeridos.

```shell
$ sudo apt update
$ sudo apt install nfs-common 
```

Crear un directorio en el nodo esclavo

```shell
$ mkdir cloud 
```
Y ahora, se monta el directorio compartido 'cloud'

```shell
$ sudo mount -t nfs master:/home/hpc/cloud ~/cloud 
```

Para verificar que el directorio haya sido montado correctamente, ejecutar

```shell
$ df -h
```
Debe haber una line al final parecida a 

```shell
Mounted on master:/home/hpc/cloud /home/hpc/cloud 
```

Para hacer que el montaje sea permanente para que no tenga que montar manualmente el directorio compartido cada vez que reinicie el sistema, puede crear una entrada en la tabla de sistemas de archivos '/etc/fstab'.

```shell

$ sudo echo '#MPI CLUSTER SETUP' >> /etc/fstab
$ sudo echo 'master:/home/hpc/cloud /home/hpc/cloud nfs' >> /etc/fstab
```

# 4. Ejecución de programas con herramienta MPI en cluster

> La compilación y ejecución debe ser realizada por el usuario 'hpc'

Para probar la ejecución de MPI de forma distribuida en el cluster que configuramos, usaremos el siguiente ejemplo basico.

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    MPI_Init(NULL, NULL);// Initialize the MPI environment
    int world_size, world_rank;

    MPI_Comm_size(MPI_COMM_WORLD, &world_size);// Get the number of processes
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);// Get the rank of the process

    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_len;
    MPI_Get_processor_name(processor_name, &name_len);// Get the name of the processor

    // Print off a hello world message
    printf("Hello world from processor %s, rank %d out of %d processors\n",
           processor_name, world_rank, world_size);

    MPI_Finalize();// Finalize the MPI environment.
}
```

Se debe compilar con el siguiente comando para obtener un archivo ejecutable.

```shell
$ mpicc -o mpi_hello_world mpi_hello_world.c
```

Para ejecutar el programa anterior en una sola computadora ejecutar

```shell
$ mpirun -np 2 ./mpi_hello_world
```

Donde se -np indica el numero de procesos a ejecutar, en este caso 2.

Ahora para ejecutar en red mover el archivo ejecutable a el directorio compartido cloud y usar

```shell
$ mpirun -np 2 --host master,slave1 ./mpi_hello_world
```

Se debe recibir una salida parecida a la siguiente

```shell
Hello world from processor usuario-ThinkPad-T430, rank 0 out of 2 processors
Hello world from processor usuario-K53E, rank 1 out of 2 processors
```

Donde master y slave1 corresponden a los alias asignados en el archivo /etc/hosts en el [paso 3](#3).

# 5. Ejecución de programas con herramienta StarPu en cluster

> La compilación y ejecución debe ser realizada por el usuario 'hpc'

Para probar la ejecución de StarPu en un solo nodo usaremos el siguiente ejemplo basico que llamaremos 'starpu_hello.c'

```c
#include <stdio.h>

/* Task declaration. */
static void my_task(int x) __attribute__ ((task));
/* Definition of the CPU implementation of ‘my_task’.*/
static void my_task(int x){
    printf ("Hello, world! With x = %d\n", x);
}

int main (){
    /* Initialize StarPU. */
    #pragma starpu initialize
    /* Do an asynchronous call to ‘my_task’. */
    my_task (42);
    /* Wait for the call to complete.*/
    #pragma starpu wait
    /* Terminate. */
    #pragma starpu shutdown
    return 0;
}
```
Para compilar

```shell
$ gcc $(pkg-config --cflags starpu-1.3) starpu_hello.c -o starpu_hello $(pkg-config --libs starpu-1.3)
```

Y para ejecutar usar

```shell
$ ./starpu_hello
```

Para la ejecución de StarPu de forma distribuida en el cluster que configuramos se utilizará el programa 'mpi_cholesky_distributed' el cual se encuentra en la carpeta "$STARPU_PATH/mpi/examples/matrix_decomposition/", se recomienda copiar este directorio al directorio compartido configurado en el [paso 3](#3), llamado 'cloud'

```shell
$ cp -r $STARPU_PATH/mpi/examples/matrix_decomposition/ ~/cloud
```
Para ejecutar moverse a la carpeta ~/cloud/matrix_decomposition/

```shell
$ cd ~/cloud/matrix_decomposition/
```
Ejecutar el programa

```shell
$ STARPU_SCHED=dmdas mpirun -np 2 --host master,slave1 ./mpi_cholesky_distributed -size $((960*40*2)) -nblocks $((40*2))
```
Este comanda significa que se está ejecutando el programa mpi_cholesky_distributed de forma distribuida en 2 nodos del cluster, con alias master y slave, utilizando las herramientas StarPu con MPI.

# 6. Equipo
	
- Chávez Fragoso Gonzalo Adán, gchavez@computacion.cs.cinvestav.mx
- Leyva García Juan Antonio, jleyva@computacion.cs.cinvestav.mx
- Pacheco del Moral Oscar, opacheco@computacion.cs.cinvestav.mx
- Reyes Almanza Jesús Abraham, jreyes@computacion.cs.cinvestav.mx

# 5. FAQ



