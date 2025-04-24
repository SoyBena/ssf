# 🛜 U08AC01 - Conexiones remotas seguras SSH y RDP 
En el mundo del **desarrollo** —y especialmente en el mundo del Desarrollo Web— es de vital importancia saber conectarse de forma remota a los servidores en los que alojareoms nuestros proyectos, tanto para subir los archivos de nuestro proyecto como para hacer aquellos cambios que sean necesarios. Al principio el concepto de **conexión remota** puede ser algo abstracto de imaginar. Es tan simple como acceder desde tu ordenador o portátil a otro equipo, pudiendo operar en este último tal y como si estuvieras trabajando en la oficina desde la comodidad de tu sofá.

¿Suena bonito, verdad? Pero, como todo en el mundo de la tecnología, tiene sus **complicaciones** y problemas que nos pueden llegar a hacer la tarea de conectarnos remotamente a un servidor (o a otro equipo) algo tediosa. Trabajar con conexiones remotas es como montar en bici: una vez lo has hecho y te acostumbras sale solo. La única forma de aprender a montar en bici es intentándolo, cayéndote y volviéndolo a intentar, ¡así que vamos al lío!

> [!NOTE]
> Encontrarás a lo largo de todo el documento elementos señalados por el icono ⚠️ para indicar problemas frecuentes precedidos de sus soluciones, señaladas por el icono ✅



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

## 🔧 Antes de empezar
Antes de poder conectarnos a un equipo remoto tenemos que asegurarnos de que el equipo de destino está **preparado** para recibir conexiones remotas.
- ### 🟠 Linux
    Para establecer conexiones remotas hacia un dispositivo Linux utilizaremos el protocolo `SSH`, que nos permite acceder remotamente a otro equipo y de forma remota. Primero deberemos de comprobar si tenemos el servicio en ejecución:

    ```bash
    sudo systemctl status ssh
    ```
    ⚠️ Bajo circunstancias normales obtendremos un mensaje por consola similar al siguiente: `Unit ssh.service could not be found.` indicando que no se ha encontrado el servicio. 
    ✅ ¡Pero no pasa nada! Todo tiene solución. Primero nos cercioraremos de que contamos con el paquete `openssh-server` . Para ello, primero actualizaremos la lista de repositorios de nuestro gestor de paquetes —`apt` en este caso— y después lo descargaremos.
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
    
- ### 🪟 Windows
    Aunque también es posible concectarse entre equipos Windows con SSH, el sistema operativo cuenta con su propio protocolo de conexión remota: **Remote Desktop Protocol (RDP)**. Este protocolo nos permite **conectarnos remotamente** desde un equipo Windows (como puede ser el equipo de tu casa) a otros dispositivos Windows (como puede ser el equipo de tu oficina, o un equipo con Windows Server)

    Para habilitar el RDP tenemos que ir a `Configuración > Sistema > Escritorio Remoto` y activarlo
    ![Activado de RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/habilitar-escritorio-remoto.png?raw=true)
    
    ⚠️ ¡Pero eso no es todo! En Windows, además de habilitar la conexión de escritorio remoto hay que especificar **qué usuarios** van a ser accesibles remotamente. Si intentamos acceder con un usuario que **no está habilitado** para conexión por RDP experimentarás el siguiente error:
    
    ![Error RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/error-conexion-usuario-no-identificado.png?raw=true)
    
    ✅ En la misma pestaña de configuración del paso anterior, abajo del todo podemos encontrar la para seleccionar los usuarios **accesibles** a través del Escritorio Remoto.

    ![Usuarios RDP](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%202/configuracion-usuarios-rdp.png?raw=true)
    

---
## 🔌 ¡Nos conectamos!
Si has llegado a este punto estás listo para acceder de forma remota a otros equipos, pero... ¿Cómo lo hago?
- ### 🔐 SSH 
    Como hemos dicho antes, `SSH` es un **protocolo** que nos permite conectarnos de forma remota y segura a otros equipos. Lo interesante de esto es que **no está vinculado a un sistema operativo**, por lo que podemos conectarnos desde un dispositivo Windows a un Linux y viceversa, así como entre dos dispositios con el mismo sistema operativo.
    En este supuesto vamos a hacer dos conexiones:
    - ### Entre dos equipos Linux
        > Para este paso es importante haber seguido los pasos del la sección de Preparativos (al principio)
        
        Una vez tenemos todo listo, ingresamos en el terminal desde nuestro equipo de cliente. Para conectarnos hacemos uso del comando `ssh`, que sigue la sintaxis `ssh usuario@ip`.
        ![Conexion SSH](https://github.com/SoyBena/ssf/blob/main/U08AC01%20/img/PARTE%201/conexion-cliente-linux-server-linux.png?raw=true)
        Vemos muchas cosas en esta imagen, vamos a despiezarlo:
        | Comando                          | Explicación       |
        |----------------------------------|-------------------|
        | `ssh astro.linux@192.168.221.52` | Tratamos de establecer la conexión remota|
        | *Are you sure you want to continue connecting?* | Este es el mensaje a través del cual se nos pregunta si confiamos en el certificado (fingerprint) de la conexión remota. Una vez aceptado no nos volverá a preguntar en futuras conexiones. Después de aceptarlo **nos pide la contraseña** |
        |`$`| Promt. Una vez vemos esto hemos ingresado correctamente al usuario.|
        | `whoami` | Comprobamos con qué usuario estamos para verificar que nos hemos conectado satisfactoriamente |
        ⚠️ Pero... Como siempre, pueden haber problemas.  Pueden haber varios problemas en relación a la IP o al usuario (porque hayamos puesto mal la ip, porque el equipo de destino no esté preparado para recibir conexiones, porque el usuario introducido no exista...) Aquí vemos algunos ejemplos:
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
        | Prompts                          | Explicación       |
        |----------------------------------|-------------------|
        | `login as:` | Campo para introducir el usuario con el que nos queremos loggear |
        |`nebula.sys@192.168.221.52's passwords`| Campo para introducir la contraseña|
        Y tras hacerlo, estamos dentro.
    - ### SCP 🗂️
        Conectarnos de forma remota está bien, pero... ¿Cómo paso los archivos de mi proyecto al servidor?

    


        


        
        
    


    
