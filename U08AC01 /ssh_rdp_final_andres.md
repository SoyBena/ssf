
# 🛜 U08AC01 - Conexiones remotas seguras SSH y RDP 






> [!NOTE]
> Se recomienda visualizar desde [GitHub](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/ssh_rdp_final_andres.md) para ver las anotaciones de forma más visual.







En el mundo del **desarrollo** —y especialmente en el mundo del Desarrollo Web— es de vital importancia saber conectarse de forma remota a los servidores en los que alojareoms nuestros proyectos, tanto para subir los archivos de nuestro proyecto como para hacer aquellos cambios que sean necesarios. Al principio el concepto de **conexión remota** puede ser algo abstracto de imaginar. Es tan simple como acceder desde tu ordenador o portátil a otro equipo, pudiendo operar en este último tal y como si estuvieras trabajando en la oficina desde la comodidad de tu sofá.

¿Suena bonito, verdad? Pero, como todo en el mundo de la tecnología, tiene sus **complicaciones** y problemas que nos pueden llegar a hacer la tarea de conectarnos remotamente a un servidor (o a otro equipo) algo tediosa. Trabajar con conexiones remotas es como montar en bici: una vez lo has hecho y te acostumbras sale solo. La única forma de aprender a montar en bici es intentándolo, cayéndote y volviéndolo a intentar, ¡así que vamos al lío!

> [!TIP]
> Para la especificacíon de **problemas frecuentes** y **soluciones** obligatorias en la práctica encontrarás a lo largo de todo el documento elementos señalados por el icono ⚠️ (o anotaciones de WARNING) para indicar problemas frecuentes precedidos de sus soluciones, señaladas por el icono 💡 (o anotaciones TIP)



## ⚙️ Preparativos
Para esta práctica vamos a precisar de la creación de determinados **usuarios** en los equipos que actuarán como **servidores**. Además, asignaremos las IPs de nuestros equipos manualmente. Trabajaremos con **cuatro equipos**:
| Sistema Operativo        | Tipo       | Ip                      | Usuarios                             | 
|--------------------------|------------|-------------------------|--------------------------------------|
| Linux                    | Servidor   | 192.168.221.52          | astro.linux, nebula.sys, orbit.admin |
| Linux                    | Cliente    | 192.168.221.202         | —                                    |
| Windows                  | Servidor   | 192.168.221.102         | gamer.rdp, blocked.bot               |
| Windows                  | Cliente    | 192.168.221.152         | —                                    |


Una vez hayámos asignado las IP la ejecución de los comandos `ifconfig` en Linux e `ipconfig` en Windows debería de mostrar lo siguiente:

| Equipo             | Resultado                                                      |
|:--------------------:|:----------------------------------------------------------------:|
| Servidor Linux     | ![IP Linux Server](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/ip-linux-server.png?raw=true) |
| Cliente Linux| ![IP Linux cliente](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/ip-linux-cliente.png?raw=true)
| Servidor Windows | ![IP Windows Server](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/ip-windows-server.png?raw=true) |
| Cliente Windows | ![IP Windows Cliente](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/ip-windows-cliente.png?raw=true) |


# 🔧 Antes de empezar
Antes de poder conectarnos a un equipo remoto tenemos que asegurarnos de que el equipo de destino está **preparado** para recibir conexiones remotas.
## 🟠 Linux
Para establecer conexiones remotas hacia un dispositivo Linux utilizaremos el protocolo `SSH`, que nos permite acceder remotamente a otro equipo y de forma remota. Primero deberemos de comprobar si tenemos el servicio en ejecución:

```bash
sudo systemctl status ssh
```
    
> [!WARNING]
> Bajo circunstancias normales obtendremos un mensaje por consola similar al siguiente: `Unit ssh.service could not be found.` indicando que no se ha encontrado el servicio. 

> [!TIP]
> ¡Pero no pasa nada! Todo tiene solución. Primero nos cercioraremos de que contamos con el paquete `openssh-server` . Para ello, primero actualizaremos la lista de repositorios de nuestro gestor de paquetes —`apt` en este caso— y después lo descargaremos.

```bash
sudo apt update &&
sudo apt install openssh-server
```
Una vez hayamos descargado el paquete podremos **iniciar el servicio** de SSH que nos permitirá recibir conexiones remotas desde otros equipos:
```bash
sudo systemctl enable --now ssh
```
Si todo ha salido de forma correcta, al volver a comprobar el estado del servicio deberíamos de obtener la siguiente salida:

![Estado SSH](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/habilitado-ssh-server.png?raw=true)
    
## 🪟 Windows
Aunque también es posible concectarse entre equipos Windows con SSH, el sistema operativo cuenta con su propio protocolo de conexión remota: **Remote Desktop Protocol (RDP)**. Este protocolo nos permite **conectarnos remotamente** desde un equipo Windows (como puede ser el equipo de tu casa) a otros dispositivos Windows (como puede ser el equipo de tu oficina, o un equipo con Windows Server)

Para habilitar el RDP tenemos que ir a `Configuración > Sistema > Escritorio Remoto` y activarlo
![Activado de RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/habilitar-escritorio-remoto.png?raw=true)

> [!WARNING]
> ¡Pero eso no es todo! En Windows, además de habilitar la conexión de escritorio remoto hay que especificar **qué usuarios** van a ser accesibles remotamente. Si intentamos acceder con un usuario que **no está habilitado** para conexión por RDP experimentarás el siguiente error:

![Error RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/error-conexion-usuario-no-identificado.png?raw=true)

> [!TIP]
> En la misma pestaña de configuración del paso anterior, abajo del todo podemos encontrar la para seleccionar los usuarios **accesibles** a través del Escritorio Remoto.

![Usuarios RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/configuracion-usuarios-rdp.png?raw=true)


# 🔌 ¡Nos conectamos!
Si has llegado a este punto estás listo para acceder de forma remota a otros equipos, pero... ¿Cómo lo hago?

> [!IMPORTANT]
> Para poder conectarse de forma correcta en los apartados de este putno es importante haber seguido los pasos anteriormente descritos en el apartado "Antes de empezar

## 🔐 SSH 
Como hemos dicho antes, `SSH` es un **protocolo** que nos permite conectarnos de forma remota y segura a otros equipos. Lo interesante de esto es que **no está vinculado a un sistema operativo**, por lo que podemos conectarnos desde un dispositivo Windows a un Linux y viceversa, así como entre dos dispositios con el mismo sistema operativo.
En este supuesto vamos a hacer dos conexiones:
- ### Entre dos equipos Linux        
    Una vez tenemos todo listo, ingresamos en el terminal desde nuestro equipo de cliente. Para conectarnos hacemos uso del comando `ssh`, que sigue la sintaxis `ssh usuario@ip`.

  ![Conexion SSH](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/conexion-cliente-linux-server-linux.png?raw=true)
  
    Vemos muchas cosas en esta imagen, vamos a despiezarlo:
    | Comando                          | Explicación                              |
    |----------------------------------|------------------------------------------|
    | `ssh astro.linux@192.168.221.52` | Tratamos de establecer la conexión remota|
    | *Are you sure you want to continue connecting?* | Este es el mensaje a través del cual se nos pregunta si confiamos en el certificado (fingerprint) de la conexión remota. Una vez aceptado no nos volverá a preguntar en futuras conexiones. Después de aceptarlo **nos pide la contraseña** |
    |`$`| Promt. Una vez vemos esto hemos ingresado correctamente al usuario.|
    | `whoami` | Comprobamos con qué usuario estamos para verificar que nos hemos conectado satisfactoriamente |

> [!WARNING]  
>Pero... Como siempre, pueden haber problemas: en relación a la IP o al usuario (porque hayamos puesto mal la ip, porque el equipo de destino no esté preparado para recibir conexiones, porque el usuario introducido no exista...) Aquí vemos algunos ejemplos:

![Simulacion de errores](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/simulacion-errores.png?raw=true)
| Comando                          | Explicación       |
|----------------------------------|-------------------|
| `ssh noexiste@192.168.221.52` | Al tratar de conectar con un usuario que no existe ocurre algo curioso: no nos dice que el usuario no existe, sino que nos dice que no tenemos permiso. Esto es una medida de seguridad. |
| `ssh noexiste@192.168.225.52` | En este caso tan concreto hemos modificado la IP para intentar acceder a una ip fuera de la subred. Al intentar buscarla y no encontrarla... Se queda *"pescando"* hasta que cortamos la ejecución. |
- ### Desde Windows hasta Linux
Nos podemos concectar a través del PowerShell de Windows y no habría mayor problema, pero en este caso vamos a hacer uso de [PuTTY](https://www.putty.org/), un software que entre otras cosas nos permite conectarnos a través del protocolo `SSH`.
Al abrir el programa encontraremos (entre otros) un campo para introducir la ip del servidor al que nos queremos conectar. Tras eso, le damos a `Open`.

![Fingerprint PuTTY](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/conexion-putty-fingerprint.png?raw=true)

Tras hacerlo, volvemos a ver el famoso *Fingerprint.* Le damos a Aceptar para continuar con nuestra conexión:

![Terminal de PuTTY](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/conexion-putty-server-linux.png?raw=true)
| Prompts                          | Explicación                                                  |
|----------------------------------|-------------------|
| `login as:` | Campo para introducir el usuario con el que nos queremos loggear |
|`nebula.sys@192.168.221.52's passwords`| Campo para introducir la contraseña|

Y tras hacerlo, estamos dentro.

- ### SCP 🗂️
Conectarnos de forma remota está bien, pero... ¿Cómo paso los archivos de mi proyecto al servidor? El SCP (Secure Copy Protocol) viene para solucionarlos la vida.

Este protocolo nos permite transferir ficheros entre distintos equipos, pero... ¿Cómo se conectan ambos equipos? ¿Qué crees que puede ser? ¿Se te viene algo a la cabeza? ¿No? Este protocolo emplea de forma interna el protocolo `SSH` para establecer la conexión entre los equipos. ¿Qué cosas, no?

> [!WARNING]
> ¡Cuidado! Hemos dicho que usa `SSH` internamente, por lo que los requisitos de uso son los mismos. Intentar usar este protocolo para enviar ficheros entre dos equipos puede darnos problemas si no se ha **preparado el equipo** para recibir conexiones a través de `SSH` (tal y como hicimos al principio)

Tras asegurar que el equipo remoto está preparado para recibir conexiones `SSH` vamos a comenzar nuestra transmisión. El modo de empleo del comando `scp` es:


Para **descargar** un fichero del servidor:

```bash
scp <usuario@ip:ruta_remota> <ruta_local>
```

En este caso, descargamos el fichero desde la ruta remota `/home/orbit.admin/FicheroHaciaCliente.txt` en el directorio actual `./`

![scp-servidor-cliente](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/descarga-de-servidor-desde-cliente.png?raw=true)



Y, para **subir** un fichero al servidor.
```bash
scp <ruta_local> <usuario@ip:ruta_remota>
``` 
![scp-cliente-servidor](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/envio-cliente-servidor.png?raw=true)


Como podemos ver a continuación, en la priemra ejecución del comando `ls` vemos que no hay ficheros. Sin embargo, al volverlo a lanzar vemos que se ha completado la transferencia satisfactoriamente:

![resultado-scp-cliente-servodor](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/resultado-envio-cliente-servidor.png?raw=true)

> [!WARNING]
> Además de los errores que ya nos podemos esperar al usar `SSH` hay ciertos errores que podemos esperar. Algunos son algo complejos, como aquellos casos donde el usuario remoto no tenga permisos en la ruta a la que se quiera acceder.
>
> Sin embargo, hay algunos que pueden ser muy comunes, y además, fáciles de resolver, pero que la inexperiencia puede hacer que nos resulten muy difíciles de ubicar.
---
> [!WARNING]
> La ruta de **destino** tiene que existir previo a la ejecución del comando. Si no existe, podemos encontrarnos con un error. (`No such file or directory`)

> [!TIP]
> Basta con crear la ruta en el destino haciendo uso de `mkdir` (en nuestro equipo o remotamente como hemos visto antes.)
---
> [!WARNING]
> Es muy común que, en lugar de enviar un fichero individual queramos hacer un envio de un directorio y de sus contenidos, pero al intentarlo podemos experimentar algún error u obtener un resultado diferente al esperado.

> [!TIP]
> Esto ocurre porque (como es común en Linux) debemos de especificar que queremos enviar el directorio **y sus contenidos**, agregando el parámetro `-r` (de recursivo) al comando.
---
- ### 💻 RDP
    Ya hemos visto como manejarnos con conexiones remotas a través de la terminal de comandos, usando `SSH` para conexiones y `SCP` para transferencia segura de ficheros. No es casualidad que en ambos casos hayamos utilizado *Linux*. Y es que, aunque desde *Windwos* también podemos usar los métodos anteriores, este sistema operativo cuenta con lo que conocemos como **Remote Desktop Protrocol**: un protocolo de conexión remota a través de interfaz gráfica que nos permitirá cocnectarnos entre Dispositivos Windows.

    Para abrir la herramienta de conexión basta con buscar "Escritorio Remoto" en el menú de inicio. Una cvez dentro, deberemos de indicar la **Dirección IP** del equipo remoto y el **usuario** del equipo remoto con el que nos queremos conectar. Tras darle a conecctar deberemos de ver algo así:

    ![Conexion RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/inicio-sesion-rdp.png?raw=true)

    En este punto deberemos de introducir las credenciales del usuario cno el que queremos iniciar sesión. Tras hacerlo visualizaremos una ventana emergente en la cual podremos ver el famoso **Fingerprint** una verificación de la seguridad y certificado de la conexión con el equipo remoto.

    ![Fingerprint RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/fingerprint-win.png?raw=true)

    En este punto podemos decidir aceptarlo o rechazarlo:
    - Si lo **rechazamos** la ventana emergente se cerrará y no se efectuará la conexión.
    - Si lo **aceptamos** la ventana emergente se cerrará y continuaremos adelante con la conexión. Además, podemos marcar la casilla *No volver a preguntarme sobre conexiones a este equipo* para guardar la elección y no necesitar aceptarlo cada vez que nos conectemos.

> [!WARNING]
> ¡Ojo! Al conectar mediante este método es probable que, si hay un usuario con la sesión iniciada en el equipo remoto este usuario deba aceptar la conexión. Además, si el usuario con el que estamos tratando conectar tiene la sesión iniciada, su sesión se cerrará al nosotros conectarnos. 
> 
> Además, si alguien en el otro extremo iniciase sesión con ese usuario **mientras** estemos conectados, nos echará de la sesión.

Si todo ha salido bien nos habremos conectado al equipo:

![Conexion RDP 1](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/conexion.png?raw=true)

![Conexion RDP 2](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/escritorio-antes.png?raw=true)


Una vez aquí podemos trabajar en el equipo como si estuvieramos físicamente en él, además, desde la misma interfaz gráfica. Para comprobarlo, vamos a crear un fichero en le escritorio.

![Creamos un fichero en el escritorio](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/escritorio-fichero-creado-remoto.png?raw=true)

En principio parece que se ha creado correctamente. Aun así, vamos a acceder directamente al equipo remoto (esta vez, físicamente) para comprobar si efectivamente el fichero se ha creado:

![Prueba de persistencia](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/escritorio-despues.png?raw=true)

¡Efectivamente! Se ha creado nuestro fichero en la máquina remota. ¿Qué maravilla, no? Llegados a este puntdo ya sabemos conectarnos a través de este protocolo a otro equipo, pero... ¿No es un poco engorroso tener que estar introduciendo todo el rato la IP y el usuario?

Lo es, y por eso Windows nos permite crear un archivo con extensión `.rdp`, que almacena los datos de la conexión que queramos para agilizar este proceso. Basta con darle a guardar en la pantalla principal del RDP, obteniendo como resultado un fichero como este:
![Fichero RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/fichero-conexion-rdp.png?raw=true)

> [!TIP]
> Además de guardar las credenciales de inicio de sesión, este protocolo nos permite cambiar distintas configuraciones en relación a la conexión que tienen que ver con los recursos del sistema. Podemos configurar una calidad de imagen reducida (para reducir el ancho de banda neesario), cambiar configuraciones del audio, etc.

# 💭 Reflexión final
Tras realizar esta práctica hemos aprendido a manejarnos con conexiones remotas. Se dice rápido, pero lo cierto es que sin conocimientos ni experiencia previa puede ser algo bastante complicado de entender y de llevar a cabo.

Durante el desarrollo de esta actividad, además de haber refrescado cuestiones de gestion de usuarios tanto en Windows como en Linux he podido aprender cómo configurar ambos sistemas operativos para recibir conexiones remotas (con `RDP` en Windows y con `SSH` en Linux), destacando:
    
-  `SSH` también se puede usar entre equipos Windows. Además, me ha resultado curioso que, al conectarme a través de `SSH` desde Windows la terminal adopta la apariencia del a terminal remota, así como sus atajos de teclado.
-  Linux tiene medidas de seguridad adicionales para prevenir posibles intentos de acceso no autorizado, como especificar que **la contraseña es incorrecta** o que **no hay permisos** al intentar conectar con un usuario **inexistente.**
-  En `RDP` tenemos un funcionamiento a través de una *Whitelist:* a diferencia de en Linux, aquí debemos de especificar **qué usuarios son accesibles remotamente.**
