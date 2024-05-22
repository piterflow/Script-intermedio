<!--Encabezado (Header)-->

# PROYECTO SCRIPT INTERMEDIO

<!---PORTADA->
<!--PARA GENERAR ENLACE A UNA IMAGEN-->
![imagen de portada](https://media.dev.to/cdn-cgi/image/width=1600,height=900,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F5hhrcbgvutmdmducqkek.png "BASH LOGO")

> #### Autores: David R. Ivana S. Andés R.

<!--INDICE-->

## INDICE
1. CONTENIDO

   1.[Ejercicio1](#Ejercicio1)
      
   * [ENUNCIADO](#ENUNCIADO)

   * [DESARROLLO](#DESARROLLO)
       
   * [RESULTADO](#RESULTADO)

    2.[ Ejercicio2](#Ejercicio2)

   * [ENUNCIADO](#ENUNCIADO)

   * [DESARROLLO](#DESARROLLO)
       
   * [RESULTADO](#RESULTADO)


    3.[ Ejercicio3](#Ejercicio3)

   * [ENUNCIADO](#ENUNCIADO)

   * [DESARROLLO](#DESARROLLO)
       
   * [RESULTADO](#RESULTADO)


    4.[ Ejercicio4](#Ejercicio4)

   * [ENUNCIADO](#ENUNCIADO)

   * [DESARROLLO](#DESARROLLO)
       
   * [RESULTADO](#RESULTADO)


    5.[ Ejercicio5](#Ejercicio5)

   * [5.1ENUNCIADO](#ENUNCIADO)

   * [DESARROLLO](#DESARROLLO)
       
   * [RESULTADO](#RESULTADO)


2. [BIBIOGRAFÍA](#BIBIOGRAFÍA)

3. [CONCLUSIONES](#CONCLUSIONES)
---
<!--CONTENIDO-->

### CONTENIDO

#### Ejercicio1.

Realiza un script llamado comprobarApache.sh, que compruebe cada minuto si el servicio apache2 está activo (running).

Si está parado, entonces:

1.- Introduce una línea: “Error-Apache: Fecha y hora actual” en /root/ApacheError.tmp, 
donde FechaActual, representa día, mes, año, hora y minuto.

2.- Reinicia el servicio apache2 
Para comprobarlo, para el servicio. Ejecuta el script en segundo plano y observa si lo 
reinicia y crea el archivo.

3.- Además del script, crea una tarea programada, de forma que ese script se ejecute cada 
6 horas, todos los días. Y si el ordenador está apagado, se debe ejecutar la próxima vez 
que se inicie, transcurrido cinco minutos. 

###### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![Cabecera.png](https://i.postimg.cc/0y4Qnvns/Cabecera.png)](https://postimg.cc/Xrw4NTtH)

Para que el script se pueda ejecutar, debe ser ejecutado por el usuario **/root**. Si no se es el usuario administrador, aparecerá un mensaje de denegación.

> función comprobar usuario /root

[![tuser-Root.png](https://i.postimg.cc/GpS597bz/tuser-Root.png)](https://postimg.cc/v1fzNvg6)

Vemos que no ha sido ejecutado por el /root y nos muestra un  mensaje por pantalla.

> Mensaje de *Permiso denegado*

[![denegar-Permiso.png](https://i.postimg.cc/SQwjckZb/denegar-Permiso.png)](https://postimg.cc/4nQJgkkW)


> Función para comprobar si apache2 está activo

La función **apacheActivo()** comprueba el estado del servidor con `systemctl status apache2`. En caso de estar **inactive**, nos redirige el error.

[![apache-Activo.png](https://i.postimg.cc/WzsWHQ8N/apache-Activo.png)](https://postimg.cc/G8SxBg6V)

> Función para reiniciar apache2

En caso de que Apache no esté activo, la función **reiniciarApache()** creará el archivo y redireccionará el error a */root/ApacheError.tmp*. Luego, levantará el servicio con `systemctl restart apache2`.

[![reiniciar-Apache.png](https://i.postimg.cc/7hb7h9Y1/reiniciar-Apache.png)](https://postimg.cc/bScdFQnd)

> Función para comprobar si Apache está activo cada minuto.

La función **comprobarApache()** consiste en un bucle que verifica cada 60 segundos si apache2 está activo o no. En caso de que no lo esté, llama a la función **reiniciarApache** para activarlo de nuevo.

[![bucle.png](https://i.postimg.cc/Cx4jvztq/bucle.png)](https://postimg.cc/jDLw2dRx)

> Bloque principal

En el bloque principal, llamamos a la función `TuSerRoot()`, que comprueba si el usuario es superusuario o no. Para que aparezca en pantalla el mensaje *Apache corriendo correctamente*, en caso de que esté *inactive* (Apache inactivo. Reiniciando Apache) y una vez reiniciado (Apache reiniciado correctamente). Utilizamos la función `comprobarApache &` (se ejecuta en segundo plano).

[![Bloque-Principal.png](https://i.postimg.cc/2S47WHrr/Bloque-Principal.png)](https://postimg.cc/gx2hFHxT)

> CONTRAB

El archivo donde se configuran las tareas programadas en Linux es **crontab**. Para que el script se ejecute cada 6 horas, se debe indicar en el archivo `/etc/crontab`.

[![crontab.png](https://i.postimg.cc/k4kmkMZQ/crontab.png)](https://postimg.cc/sQ9qWz3x)

> ANACRONTAB

Para que el script se ejecute una vez encendido, 5 minutos después, tenemos que poner las instrucciones en el **anacrontab**.

[![anacrontab.png](https://i.postimg.cc/1X5G1Pqz/anacrontab.png)](https://postimg.cc/0MFM7TZg)

###### RESULTADO
Compronamos el correcto funcionamiento del script.

> Paramos el servicio apache2 con `systemctl stop apache2` y ejecutamos `systemctl status apache2` para comprobar que está inactive.

[![stop-Apache.png](https://i.postimg.cc/sXBm07t2/stop-Apache.png)](https://postimg.cc/HcgbrcWG)

> Ejecutamos el script `./comprobarApache.sh` y nos muestra el menjase que nos indica que está corriendo el servicio apache.

[![ejecutar-Apache.png](https://i.postimg.cc/J0Nb9qKR/ejecutar-Apache.png)](https://postimg.cc/n9Ljmq6W)

> * Código del script

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 2.0
# Fecha: 22/04/2024
# Descripción: Este script comprueba cada minuto si Apache está activo

# Función para comprobar que el script se ejecuta como administrador
tuSerRoot()
{
    if [ $(id -u) != 0 ]; then
      echo "Este script solo puede ser ejecutado por el root."
      exit 1
    fi
}

# Función para comprobar si Apache2 está activo
apacheActivo() {
  systemctl status apache2 | grep "active (running)" > /dev/null
  return $?
}

# Función para registrar error y reiniciar Apache2
reiniciarApache() {
  fechaActual=$(date +%Y-%m-%d-%H-%M)
  echo "Error-Apache: $fechaActual" >> /root/ApacheError.tmp
  systemctl restart apache2
}

comprobarApache() {
  # Comprobar cada minuto si Apache2 está activo
    while [ true ]; do
      apacheActivo
      if [ $? -eq 1 ]; then
        reiniciarApache
      fi
      sleep 60
    done
}

##Bloque principal
clear
tuSerRoot

systemctl status apache2 | grep "active (running)" > /dev/null
if [ $? -eq 0 ]; then
  echo "Apache corriendo correctamente."
fi

systemctl status apache2 | grep "inactive (dead)" > /dev/null
if [ $? -eq 0 ]; then
  echo "Apache inactivo. Reiniciando Apache..." && sleep 3
  echo "Apache reiniciado correctamente."
fi

comprobarApache &

```

---

#### Ejercicio2.
Realiza un script llamado usuariosBloqueados.sh, que nos muestre un menú:

1.- Usuarios Bloqueados.

2.- Bloquear un usuario.

3.- Desbloquear usuario.

4.- Cerrar sesión usuario.

5.- Salir

###### DESARROLLO

> Usuarios Bloquedados

* Funsión `usuariosBloqueados()`.

> Bloquear un usuario

> Desbloquear usuario

> Cerrar sesión usuario

> Salir

###### RESULTADO

---

#### Ejercicio3.

Realiza un script llamado crearBorrarUsuarios.sh, que nos muestre un menú:

1.- Crear Usuarios.

2.- Borrar Usuarios.

3.- Salir 

###### DESARROLLO

###### RESULTADO

---

#### Ejercicio4.

Crea en un script llamado crearUsuarios.sh que permita crear usuarios de forma automática. Indicaciones: 
1.- Al script se le pasa dos parámetros:

a) El primer parámetro representa el nombre de un usuario genérico.

b) El segundo parámetro representa el número de usuarios que quiere crearse.

###### DESARROLLO

###### RESULTADO

---


#### Ejercicio5.

Partimos de que tenemos varios usuarios: usuario1, usuario2, usuario3. 

Al usuario1, se le ha establecido una cuota de disco: 40k y 100K (soft y hard respectivamente).

Realiza un script llamado cuotasUsuarios.sh, que nos copie la cuota del usuario1 a todos los usuarios cuyo uid >1000 y uid<2000.

###### DESARROLLO

###### RESULTADO

---
<!--BIBIOGRAFÍA-->

#### BIBIOGRAFÍA
* [www.geekland.eu/](https://geekland.eu/solucion-a-que-un-script-no-se-ejecuta-en-cron-pero-si-en-la-terminal/ "ENLACE")

* [https://help.dreamhost.com/hc/es](https://help.dreamhost.com/hc/es/articles/360038608892-Soluci%C3%B3n-de-problemas-de-Cron-jobs "ENLACE")

* [https://www.linuxtotal.com.mx/](https://www.linuxtotal.com.mx/index.php?cont=info_admon_018 "ENLACE")

* [https://www.redeszone.net/](https://www.redeszone.net/tutoriales/servidores/cron-crontab-linux-programar-tareas/ "ENLACE")

* [chatgpt.com](https://chatgpt.com/?oai-dm=1 "ENLACE")

<!--CONCLUCIÓN-->
#### CONCLUCIONES

> **A raíz de este proyecto, hemos aprendido varias herramientas, y una de ellas es esta misma plataforma GitHub, con la que hemos podido compartir nuestros proyectos y conocimiento. Por otra parte, después de realizar el trabajo de script nivel intermedio/avanzado, hemos ido desarrollando mayor soltura en la automatización de tareas en /bin/bash, por lo que, a partir de ahora, si quisiéramos realizar ciertas tareas en nuestros ordenadores, no tendremos que preocuparnos de hacerlas nosotros mismos; podemos programarlas.**
