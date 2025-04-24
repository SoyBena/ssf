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

## 🔧 Paso 1: Antes de empezar
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

    Para habilitar
    
