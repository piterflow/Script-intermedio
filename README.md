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

   * [ENUNCIADO](#ENUNCIADO)

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

###### RESULTADO

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
