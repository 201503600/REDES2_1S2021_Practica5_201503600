#### Universidad San Carlos de Guatemala
#### Facultad de Ingenieria
#### Escuela de Ciencias y Sistemas
#### Redes de Computadoras 2
#### Ing. Pedro Pablo Hernandez Ramirez
#### Aux. Wilson Guerra

# Manual Técnico - Práctica 5

### Alumno

|Carnet   |Nombre                         |
|:---:|:---:|
|201503600|Edgar Daniel Cil Peñate        |

# :bookmark_tabs: Índice

- [Definición del problema](#:x:-Definición-del-problema)
- [Solución](#:white_check_mark:-Solución)
  - [Arquitectura](#Arquitectura)
  - [Configuracion VPC](#VPC)
  - [Configuracion Subnet](#Subnets)
  - [Configuracion Tablas de Ruteo](#Routes-Tables)
  - [Configuracion Gateway](#Internet-Gateway)
  - [Configuracion EC2](#EC2)
  - [Configuracion Balanceador de Carga](#Load-Balancer)
  - [Configuracion Security Group Privada](#Security-Group)
  - [Conexion EC2](#Conexion-EC2)

# :x: Definición del problema
La empresa Guatemalan Hands, se dedica a la fabricación y venta de artesanía y
textiles a nivel nacional, con el objetivo de expandir la cultura guatemalteca, desean
iniciar a exportar sus productos en todo el continente americano. Actualmente
cuentan con 3 sucursales en centros comerciales, sin embargo debido a la
pandemia estos lugares fueron cerrados, esta empresa quiere trasladar sus
operaciones de ventas a un modelo online, mediante una página de comercio
online, sin embargo están indecisos sobre qué tecnología es la correcta para poder
completar este requerimiento.
Dentro de una reunión, con el coordinador del área de ventas, producción, recursos
humanos e informática, se le ha indicado a usted quien es coordinador del
departamento de Informática, que debe presentar una propuesta de página web la
cual cumpla con el objetivo de expandir la cultura guatemalteca a todo el continente
americano, haciendo énfasis en que esta pagina solamente debe de ser ilustrativa
puesto que a partir de esta tomarán decisiones posteriores para su implementación
definitiva, pero que todas las funcionalidades de redes y seguridad deben estar.

------
## :white_check_mark: Solución
------
## **Arquitectura**
Para la solucion implementada se utilizo la siguiente arquitectura con Amazon VPC, en la cual se definen 2 subredes diferentes, en la cual solamente una de ellas tiene acceso a internet y con ayuda de un balanceador de carga se podra visualizar la pagina. Las subredes tienen las siguientes direcciones de red:

|Red   |Direccion de Red|
|:---:|:---:|
|Public Network|13.0.3.0/24 |
|Private Network|13.0.6.0/24|

Para exponer la pagina web se utilizo un servidor nginx. Solamente la primer instancia (ubicada en la red publica) tendra conexion a la instancia que se encuentra en la red privada.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/tree/main/capturas/Arquitectura.png)

## Configuraciones realizadas

### **VPC**
Para poder implementar la red en AWS se utilizó el servicio de Amazon VPC. En el panel de AWS se hace la búsqueda de `VPC` y se selecciona el servicio. En la nueva ventana mostrada dar click al boton `Create VPC`. En el formulario a llenar basta con ingresar un nombre para la red e ingresar el rango de direcciones permitidas, tal como se muestra en la siguiente imagen.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_vpc.png)

Por ultimo de click en `Create VPC` y esta sera creada. Una vez creada la VPC se visualizara la siguiente ventana

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/vpc_creada.png)

### **Internet-Gateway**
Para que alguna subred pueda ser accesida desde internet, se debe configurar un gateway. 

> Nota: Solo puede crearse una gateway por VPC

Para crear una gateway, en el panel lateral izquierdo seleccionar `Internet Gateways` y aparecera la siguiente ventana, en la cual debe darse click al boton en naranja que se encuentra en la esquina superior derecha.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet1.png)

Una vez hecho esto, se debe asignar un nombre al gateway que se esta creando.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet2.png)

Luego dar click al boton `Create internet gateway`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet3.png)

Una vez creada, debe asociarse este gateway a una VPC, para ello, dar click al boton que se muestra en la barra superior.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet4.png)

En la vista nueva que se muestra, dar click a la opcion Actions > Attach to VPC.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet5.png)

En la vista que aparece ir a la pestaña `Routes` y dar click al boton `Edit routes`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet6.png)

En la ventana se muestra la tabla de ruteo asignada a la VPC, se para que esta tenga acceso a internet dar click al boton `Add route`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet7.png)

En el destino asignar `0.0.0.0/0` lo cual indica que se podra comunicar a cualquier direccion ip con cualquier mascara de subred. En target seleccionar la opcion `Internet Gateway`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet8.png)

Posterior a ello, seleccionar la gateway creada anteriormente y dar click en `Save`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_internet9.png)

### **Subnets**
Seguidamente deben crearse las subredes (publica y privada). Para poder crear una nueva subred debe ir al panel izquierdo (Panel de VPC) y seleccionar `Subnets`

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/menu_vpc.png)

En la ventana que se muestra apareceran todas subredes creadas. Debe dar click en el boton `Create` que se muestra en la esquina superior derecha.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet1.png)

Luego debe seleccionarse la VPC a la que pertenecera la subred

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet2.png)

Una vez seleccionada la VPC se mostrara el siguiente menu en el cual debe asignarse un nombre a la subred al igual que asignar una direccion de red junto con su mascara de subred, esta direccion debe estar dentro del rango permitido de la VPC.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet3.png)

Si se desea crear otra subred en la parte inferior debe dar click al boton `Add new subnet`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet4.png)

Al igual que anteriormente, se configura la nueva subred 

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet5.png)

Al tener configuradas todas las subredes dar click al boton `Create subnets` y aparecera la siguiente ventana.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet6.png)

Para poder darle acceso a internet a una de las subredes creadas, debe seleccionar la subnet y seleccionar Actions > Modify auto-assign IP settings

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet7.png)

En la nueva ventana mostrada dejar habilitada la opcion `Enable auto-assign public IPv4 address` y dar click en `Save`

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_subnet8.png)

### **Routes-Tables**
Una vez creadas las subredes que se necesiten, deben asignarse las tablas de ruteo, las cuales sirven para que la subred sepa a donde conectarse. Para ello, en el menu lateral izquierdo seleccione `Route Tables`. Se mostrara la siguiente pagina y se debe dar click en el boton `Create route table`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table1.png)

Posteriormente, debe asignarse un nombre a la tabla de ruteo y seleccionar la VPC a la cual pertenecera la misma.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table2.png)

Una vez creadas las tablas de ruteo necesarias apareceran en la vista principal.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table3.png)

Luego se debe asignar la tabla de ruteo a la subred a la cual pertenecera. Para hacer esto se debe seleccionar la tabla de ruteo y se visualizara la vista inferior que se muestra en la siguiente imagen, dar click a la pestaña `Subnet Associations`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table4.png)

Una vez seleccionada dar click al boton `Edit subnet associations`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table5.png)

Aparecera la siguiente vista y en ella se deben seleccionar la subred a la que pertenecera esta tabla de ruteo. Por ultimo dar click en el boton `Save`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_route_table6.png)


### **EC2**

Para crear una instancia de EC2, ir al servicio EC2 de Amazon y en el panel lateral izquierdo seleccionar `Instancias` y se mostrara la siguiente vista, en ella dar click al boton `Lanzar instancias`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_1.png)

Una vez hecho esto, seleccionar la imagen que desea utilizar, en este caso se utilizo la imagen predefinida de Ubuntu Server 20.04 LTS, dar click en el boton `Seleccionar`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_2.png)

En el siguiente paso debe seleccionar el tipo de instancia que desea utilizar, para este caso se dejo el valor predefinido y seleccionar siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_3.png)

En el tercer paso, se debe indicar la cantidad de instancias que se desean crear. Tambien debe seleccionarse la VPC a la que perteneceran las instancias, asi como la subred en la cual se asignaran. Dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_4.png)

En el cuarto paso, dejar los valores predefinidos y dar click a siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_5.png)

Nuevamente dar click a siguiente

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_6.png)

En el sexto paso se debe configurar el security group, el cual permitira el acceso a la instancia a traves de ciertos puertos. Para este caso, los puertos necesarios son el 22 (Para conexion SSH) y el puerto 80 (Puerto en el cual se expondra el servidor web). En la columna origen seleccionar personalizado e ingresar `0.0.0.0/0` para indicar que se aceptara peticiones desde cualquier direccion ip con cualquier mascara de subred, dar click a siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_7.png)

Una vez realizados todos los pasos, se revisa toda la configuracion realizada y dar click en `Lanzar`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_8.png)

Antes de que la instancia sea lanzada aparecera un cuadro de dialogo en el cual se debe indicar si ya se cuenta con una llave para la conexion por medio del protocolo SSH. Si no se cuenta con una, seleccionar `Crear un nuevo par de claves`

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_9.png)

Luego ingresar el nombre que tendra el par de claves y dar click en la opcion `Descargar par de claves`. Ya que si no se descarga no se podra conectar a la maquina virtual. Dar click en `Lanzar instancias`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_10.png)

Una vez lanzadas, se puede asignar un nombre a cada una de las instancias en la columna Name, dando click en el icono de editar, ingresar el nombre que se quiera.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_11.png)

En la siguiente imagen se muestra como quedarian las instancias creadas.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_ec2_12.png)

### **Load-Balancer**
Posterior a crear las instancias, se configuro un balanceador de carga de aplicaciones, para que si alguno de los servidores falla, el cliente aun pueda seguir visualizando la pagina sin ningun problema. Para esta configuracion, en el panel lateral izquierdo del servicio EC2 seleccionar `Balanceador de Carga`, en la vista que aparece dar click al boton `Crear balanceador de carga`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_1.png)

Como primer paso, debemos seleccionar el tipo de balanceador de carga que se creara, para este caso, crearemos un balanceador de carga de aplicaciones.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_2.png)

El segundo paso es asignar un nombre al load balancer y asignar el puerto en el cual estara expuesto. Para este caso al ser para una pagina web, el puerto que se necesita es el 80 (HTTP).

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_3.png)

En la parte inferior, se debe seleccionar la VPC a la cual se asignara el load balancer, al igual que 2 zonas de disponibilidad como minimo. Dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_4.png)

En el siguiente paso, dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_5.png)

Luego, se debe configurar el security group. Para el balanceador, se creara uno nuevo ya que solamente se necesitara el puerto 80. 

> Nota: Asignar un nombre y los puertos que considere convenientes.

Dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_6.png)

Posteriormente, se debe seleccionar el destino, es decir, a que servidores redirigira el trafico el load balancer. Para ello, ingresar el nombre del nuevo destino y seleccionar como tipo de destino `Instancia`, ya que si se reinicia cualquiera de las instancias creadas anteriormente, no habra problemas de comunicacion con el balanceador debido a que este hara referencia a la instancia como tal y no a la direccion IP que esta tenga.

Seleccionar el puerto por medio del cual tendra comunicacion con los servidores, para este caso sera el puerto 80. Dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_7.png)

Luego, se deben registrar los destinos que tendra asociados el balanceador, para ello, en la tabla inferior seleccionar las instancias a las cuales se redirigira el trafico y dar click en el boton `Agregar a registrado`. Dar click en siguiente.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_8.png)

Como ultimo paso, revisar la configuracion realizada y si todo esta correctamente, dar click en `Crear`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/creacion_balanceador_9.png)

### **Security-Group**
Se debe tener en cuenta que solamente la primer instancia de la subred publica tendra conexion a la instancia de la subred privada, para ello, debemos modificar el security group de la instancia privada.

Nos vamos a la vista de instancias, seleccionamos la instancia correspondiente y en el panel inferior, seleccionar la pestaña `Seguridad`. En esta la vista que aparece dar click al enlace con titulo `Grupos de seguridad`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/sg_privada.png)

En la vista que se muestra, dar click al boton `Editar reglas de entrada`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/sg_privada_2.png)

Aca lo unico que debemos realizar es cambiar la direccion de destino de `0.0.0.0/0` a <direccion_ip_primer_instancia>/<mascara_subred>. Dar click en `Guardar reglas`.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/sg_privada_3.png)

### **Conexion-EC2**

> Instancias subred publica

Para la conexion a las instancias, se utilizo el programa termius por medio del protocolo SSH. Al conectarnos a las instancias, es necesario verificar la conexion a internet, para ello, ingresamos el comando 

```
ping google.com
```

Luego es necesario actualizar los paquetes de ubuntu con el comando 

```
sudo apt update
```

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/conexion_ec2.png)

Luego instalamos el servidor de nginx con el comando 

```
sudo apt install nginx
```

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/conexion_ec2_2.png)

Y con ello ya tendremos nuestro servidor web.

> Nota: Si desea realizarlo puede modificar la vista de la pagina en la ruta `/var/www/html`

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/visualizacion_pagina.png)

Se puede verificar el funcionamiento del balanceador ingresando la url del mismo, como se muestra a continuacion

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/acceso_load_balancer.png)

> Instancia subred privada

Para la conexion a la instancia ubicada en la subred privada, es necesario subir nuestra llave a la primer instancia publica dado que sin esta llave, no se podra realizar la conexion SSH.

Para subir esta llave, en la terminal de nuestra computadora, nos ubicamos en la ruta en la cual se encuentra este archivo e ingresamos el siguiente comando

```
scp -i <archivo_llave> <archivo_a_subir> <user>@<ip_publica>:<ruta_almacenamiento>
```

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/copy_key.png)

Para verificar que se subio correctamente el archivo, en la instancia se ejecuta el comando `ls` y el mismo debe de aparecer.

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/list_key.png)

Una vez verificado, se deben otorgar permisos 400 al archivo de conexion con el comando 

```
sudo chmod 400 <nombre_archivo>
```

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/conexion_ec2_privada_1.png)

Luego con la cadena de conexion a la instancia privada nos conectamos desde la primer instancia y se obtiene lo siguiente.

> Notar que la direccion ip de la terminal cambia de `13.0.3.215` a `13.0.6.229`

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/conexion_ec2_privada_1.png)

### Anexos
Para confirmar que solamente la primer instancia tiene conexion a la instancia privada, se copio el archivo de conexion a la segunda instancia publica y se ejecutaron los mismos comandos en la misma. 

![](https://github.com/201503600/REDES2_1S2021_Practica5_201503600/blob/main/capturas/conexion_ec2_privada_2.png)

Como se puede visualizar en la anterior imagen, debido a la configuracion realizada por el security group, no se puede conectar.
