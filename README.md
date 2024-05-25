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
      
   * ENUNCIADO

   * DESARROLLO
       
   * RESULTADO

    2.[ Ejercicio2](#Ejercicio2)

   * ENUNCIADO

   * DESARROLLO
       
   * RESULTADO


    3.[ Ejercicio3](#Ejercicio3)

   * ENUNCIADO

   * DESARROLLO
       
   * RESULTADO


    4.[ Ejercicio4](#Ejercicio4)

   * ENUNCIADO

   * DESARROLLO
       
   * RESULTADO


    5.[ Ejercicio5](#Ejercicio5)

   * ENUNCIADO

   * DESARROLLO
       
   * RESULTADO


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

##### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![Cabecera.png](https://i.postimg.cc/0y4Qnvns/Cabecera.png)](https://postimg.cc/Xrw4NTtH)

Para que el script se pueda ejecutar, debe ser ejecutado por el usuario **/root**. Si no se es el usuario administrador, aparecerá un mensaje de denegación.

> Función  **tuSerRoot()**

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


> Bloque  princial

En el bloque principal, llamamos a la función `TuSerRoot()`, que comprueba si el usuario es superusuario o no. Para que aparezca en pantalla el mensaje *Apache corriendo correctamente*, en caso de que esté *inactive* (Apache inactivo. Reiniciando Apache) y una vez reiniciado (Apache reiniciado correctamente). Utilizamos la función `comprobarApache &` (se ejecuta en segundo plano).

[![Bloque-Principal.png](https://i.postimg.cc/2S47WHrr/Bloque-Principal.png)](https://postimg.cc/gx2hFHxT)

> CONTRAB

El archivo donde se configuran las tareas programadas en Linux es **crontab**. Para que el script se ejecute cada 6 horas, se debe indicar en el archivo `/etc/crontab`.

[![crontab.png](https://i.postimg.cc/k4kmkMZQ/crontab.png)](https://postimg.cc/sQ9qWz3x)

> ANACRONTAB

Para que el script se ejecute una vez encendido, 5 minutos después, tenemos que poner las instrucciones en el **anacrontab**.

[![anacrontab.png](https://i.postimg.cc/1X5G1Pqz/anacrontab.png)](https://postimg.cc/0MFM7TZg)

##### RESULTADO
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

##### DESARROLLO

> Usuarios Bloquedados

* Funsión `usuariosBloqueados()`.

> Bloquear un usuario

> Desbloquear usuario

> Cerrar sesión usuario

> Salir

##### RESULTADO

---


#### Ejercicio3.

Realiza un script llamado crearBorrarUsuarios.sh, que nos muestre un menú:

1.- Crear Usuarios.

2.- Borrar Usuarios.

3.- Salir 

##### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![Encabezado.png](https://i.postimg.cc/JhS80ymh/Encabezado.png)](https://postimg.cc/HJ0RPkrG)

Para que el script se pueda ejecutar, debe ser ejecutado por el usuario **/root**. Si no se es el usuario administrador, aparecerá un mensaje de denegación.

> Función comprobar usuario /root

[![tu-Ser-Root.png](https://i.postimg.cc/pTNNxgYh/tu-Ser-Root.png)](https://postimg.cc/p9BcJcSR)

Mediante un `while read` sacamos los campos *nombreusuario, contraseña, nombre, apellido y correo electrónico*, obtenidos del archivo **usuario.csv** ubicado en el directorio /root.

> Función  para crear usuarios

[![crear-Usuarios.png](https://i.postimg.cc/WzWXpZLW/crear-Usuarios.png)](https://postimg.cc/zHhn0VfK)

Mediante un `while read` y un separador de dos puntos `(IFS=':')` borramos el usuario y su directorio /home.

> Función para borrar usuarios

[![borrar-Usuarios.png](https://i.postimg.cc/L6mqBsgX/borrar-Usuarios.png)](https://postimg.cc/dDStJwBP)

Con esta función menú mostramos en pantalla las opciones que da el script, y con el case, introducimos uno de los 3 números que nos da el menú y nos realiza la opción que hemos elegido.

> Función  menu

[![menu.png](https://i.postimg.cc/DZgh27vj/menu.png)](https://postimg.cc/vgckhF6f)

> Bloque  principal

Llamamos a la función **tuSerRoot()** para que nos compruebe si somos o no usuario administrador. Luego, nos limpia la pantalla con un `clear` y ejecuta el menú.

[![menu.png](https://i.postimg.cc/DZgh27vj/menu.png)](https://postimg.cc/vgckhF6f)


##### RESULTADO

Compronamos el correcto funcionamiento del script.

> Ejecutamos el script y este nos muestra el menú.

[![Ejecucion1.jpg](https://i.postimg.cc/D0PPjYcb/Ejecucion1.jpg)](https://postimg.cc/H8jMjzRp)


Creamos usuario

> Opción 1 creación de usuarios

[![Ejercicio2.jpg](https://i.postimg.cc/3r0q3Ktt/Ejercicio2.jpg)](https://postimg.cc/s1ymcRdW)


Borramos los usuarios.

> Opción 2, borrar usuario

[![ejecucion4.jpg](https://i.postimg.cc/rFr2ZG6F/ejecucion4.jpg)](https://postimg.cc/Ln2Wn16r)


Salimos del script.

> Opción 3 nos saca del script

[![Ejecucion3.jpg](https://i.postimg.cc/6qWv36dy/Ejecucion3.jpg)](https://postimg.cc/MMLT3S3S)

Comprobamos que los usuarios **joseXX** se han creado correctamente.

[![comprobacion-Usuario.jpg](https://i.postimg.cc/MGKWLcbP/comprobacion-Usuario.jpg)](https://postimg.cc/RWy56ZYK)

Comprobamos que los usuarios **joseXX** se han eliminado.

[![Comprobacion-Borrado.jpg](https://i.postimg.cc/Y0gkFQM9/Comprobacion-Borrado.jpg)](https://postimg.cc/7Jxp8GC8)


> * Código del script

```bash
#!/bin/bash
# Authors: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 18/04/2024
# Descripción: Este script muestra un menú que permite crear y borrar usuarios del fichero "/root/usuarios.csv"
clear

##Parámetros/Variables


##Funciones
# Función para comprobar que el usuario que ejecuta el script sea root
tuSerRoot()
{
    	if [ `id -u` != 0 ]; then
      		echo "Este script solo puede ser ejecutado por el root."
      	exit 1
    	fi
}

# Crear usuario a partir del archivo usuarios.csv
crearUsuarios() 
 {
	clear
	echo "Creando usuarios..." 
	# Lee el archivo CSV línea por línea
  	 while IFS=':' read -r nombreUsuario contrasena nombre apellido email; do
  	# Crea el usuario con opciones específicas
        useradd -m -p "$(openssl passwd -1 "$contrasena")" -c "$nombre $apellido" -e "2024-06-30" "$nombreUsuario"
        # Establece la contraseña usando chpasswd
    		echo "$nombreUsuario:$contrasena" | chpasswd
    	# Crea un archivo con el correo electrónico del usuario
    		echo "$email" > "/home/$nombreUsuario/email.txt"    			
 	    	echo ""
    	    	echo "Creación de usuarios completada."
   	done < "/root/usuarios.csv"
}

# Función para borrar usuarios
borrarUsuarios() 
{
 	clear
 	echo "Borrando usuarios..."        
        # Borrar el usuario y su directorio home
        while IFS=':' read -r nombreUsuario _; do
      	# Elimina el usuario y redirige posibles errores
    		userdel -r "$nombreUsuario" 2> /dev/null
     	    	echo ""
            	echo "Borrado de usuarios completado." 
        done < "/root/usuarios.csv" 
}

Función menú
menu() 
{
	while  true;
 	do
 		echo ""
  		echo " Script para crear o borrar usuarios"
  		echo "====================================="
  		echo "1.- Crear usuarios."
  		echo "2.- Borrar usuarios."
  		echo "3.- Salir"
  		echo ""
  		read -p "Indique una de las opciones anteriores: " opcion
  		

  	case $opcion in
    		1)
    			crearUsuarios ;;
    		2) 
    			borrarUsuarios ;;
    		3) 	
    			clear
    			echo "Saliendo del script...";
    			echo "";
    			exit ;;
    			
    		*) 
    			clear
    			echo "Opción no válida. Por favor, seleccion 1, 2 o 3.";;
    		
  		esac
	done
}

#Bloque principal
tuSerRoot
clear
menu

```

---

#### Ejercicio4.

Crea en un script llamado crearUsuarios.sh que permita crear usuarios de forma automática. Indicaciones: 
1.- Al script se le pasa dos parámetros:

a) El primer parámetro representa el nombre de un usuario genérico.

b) El segundo parámetro representa el número de usuarios que quiere crearse.

##### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![encabezado.png](https://i.postimg.cc/VLDk1BG9/encabezado.png)](https://postimg.cc/MvfSmR8X)

Con esta función mostramos en pantalla la sintaxis de cómo se deben introducir los datos.

> Función  mostar ayuda

[![mostrar-Ayuda.png](https://i.postimg.cc/bw48N6Qn/mostrar-Ayuda.png)](https://postimg.cc/hX8N21Z4)

Comprobamos los argumentos que se han introducido.

> Función  comprobar argumento

[![comprobar-Argumento.png](https://i.postimg.cc/nV3yhRxL/comprobar-Argumento.png)](https://postimg.cc/s1Z6nJvF)

Las siguientes variables lo que nos hace es que se guardan los parámetros 1 y 2 en un archivo con la fecha del día en que se ejecuta.

Cada vez que se crea un usuario nuevo, se creará un archivo nuevo con la fecha en la que se ejecuta.

[![variables.png](https://i.postimg.cc/SQLvnZp9/variables.png)](https://postimg.cc/V56RTWvs)

La función validarNumero se encarga de limitar la creación de usuarios cuando se le pasa un parámetro para crear X cantidad de ellos, asegurándose de que no se creen más de 9.

> Función  validar número

[validar-Numero.png](https://postimg.cc/34wgs3Kf)

Cuando se crea un usuario, se le asigna una contraseña inicial que es igual a su nombre.

Al iniciar sesión por primera vez, el sistema obliga al usuario a cambiar su contraseña inicial por una nueva.

> Bloque principal

[![bloque-Principal.png](https://i.postimg.cc/HW3tGFg6/bloque-Principal.png)](https://postimg.cc/jCwfPgCf)


###### RESULTADO

Da error cuando no proporcionamos los parámetros y mostramos un ejemplo para ilustrar cómo se hace.

[![Ejecucion1.jpg](https://i.postimg.cc/Jhmyz43Z/Ejecucion1.jpg)](https://postimg.cc/QF4N6Dyd)

> Lo ejecutamos correctamente.

En el día de hoy se han creado estos usuarios, los cuales se han archivado en el archivo usuariosCreados del 25 de mayo de 2024.

[![Ejecucion2.jpg](https://i.postimg.cc/26yS1q1b/Ejecucion2.jpg)](https://postimg.cc/BLW0ync4)

> * Código del script.

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 18/04/2024
# Descripción: Este script crea usuarios masivamente indicando un nombre genérico y la cantidad total de usuarios que se quieren crear.
# Parámetros

clear
# Funciones

tuSerRoot() {
   if [ "$(id -u)" != 0 ]; then
        echo "Este script solo puede ser ejecutado por el root."
        exit 1
   fi
}

mostrarAyuda() {
    echo ""
    echo " Solicitud incorrecta."
    echo ""
    echo " EJEMPLO: sh $0 alumno 3"
    echo "-Esto creará 3 usuarios llamados alumno y enumerados correlativamente (alumno1, alumno2, alumno3)-"
    echo ""
    exit 1
}

comprobarArgumentos() {
    if [ "$#" -ne 2 ]; then
        mostrarAyuda
    fi
}
nombreGenerico=$1
cantTotal=$2
fechaActual=$(date +%Y-%m-%d)
archivoUsuarios="usuariosCreados.$fechaActual.tmp"

validarNumero() 
{
    case $1 in
        ''|*[!0-9]*)
            echo "El segundo argumento debe ser un número."
            mostrarAyuda
            ;;
    esac
}
# Bloque principal
tuSerRoot
comprobarArgumentos "$@"
validarNumero "$cantTotal"

# Inicializar variables
i=1
usuarios="" # Inicializar la variable usuarios

# Crear usuarios
	while [ $i -le $cantTotal ]; do
    	usuario="${nombreGenerico}${i}"
    
    	# Crear el usuario y asignar la misma contraseña que el nombre de usuario
    	useradd -m -s /bin/bash "$usuario"
    		echo "$usuario:$usuario" | chpasswd
    
    # Obligar al usuario a cambiar la contraseña al primer inicio de sesión
    	chage -d 0 "$usuario"
   		echo "Creando usuario '$usuario'..."
   		echo ""
    		echo "$usuario:$usuario" >> "$archivoUsuarios"
    	i=$((i + 1))
	done

# Mostrar y limpiar el archivo temporal
	cat "$archivoUsuarios"
	echo ""
	echo " Usuarios añadidos y contraseñas asignadas."
	echo " Guardados en el archivo $archivoUsuarios"

```

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
