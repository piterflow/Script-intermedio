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

###### ENUNCIADO

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

La función **apacheActivo()** comprueba el estado del servidor con `systemctl status apache2`. En caso de estar **inactive**, nos redirirá al error.

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

###### ENUNCIADO

Realiza un script llamado usuariosBloqueados.sh, que nos muestre un menú:

1.- Usuarios Bloqueados.

2.- Bloquear un usuario.

3.- Desbloquear usuario.

4.- Cerrar sesión usuario.

5.- Salir

##### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![Cabecera.png](https://i.postimg.cc/SsqvQrcV/Cabecera.png)](https://postimg.cc/4KL5BpLh)

Para que el script se pueda ejecutar, debe ser ejecutado por el usuario **/root**. Si no se es el usuario administrador, aparecerá un mensaje de denegación.

> Función  **tuSerRoot()**

[![Tuser-Root.png](https://i.postimg.cc/prcGZM41/Tuser-Root.png)](https://postimg.cc/FkcVr89j)

Vemos que no ha sido ejecutado por el /root y nos muestra un  mensaje por pantalla.

> Usuarios Bloquedados

Función `mostrarusuariosBloqueados()`. Esta función muestra los usuarios bloqueados del sistema. Extrae y guarda en un archivo los usuarios bloqueados, filtra y guarda en otro archivo los usuarios del sistema con UID entre 1000 y 2000, compara ambos archivos mostrando los usuarios que están bloqueados y elimina los archivos temporales.
  
[![Usuarios-Bloqueados.png](https://i.postimg.cc/Z5pH9NbQ/Usuarios-Bloqueados.png)](https://postimg.cc/v48fkc67)

> Bloquear un usuario

La función **bloquearUsuario()** lo que hace es solicitar el nombre de usuario con el 'read -p' , verificando que éste existe. Sino fuera el caso, saldría un mensaje de Error. El usuario existente se bloqueará con el comando `usermod`.

[![Bloquear-Usuario.png](https://i.postimg.cc/5yzgwQ5j/Bloquear-Usuario.png)](https://postimg.cc/m1b74D5G)

> Desbloquear usuario

En estructura, esta función es exactamente igual que la anterior, lo único que cambia es la opcíon del comando `usermod`, que en este caso es "U".

[![Desbloquear-Usuario.png](https://i.postimg.cc/HsC4qx44/Desbloquear-Usuario.png)](https://postimg.cc/8st6WTYj)

> Cerrar sesión usuario

Como en las funciones anteriores introduciremos el nombre de un usuario, que si no existiese nos devolvería al menú, sino continuaría para obtener el tiempo que lleva inactivo. Si la inactividad hubiera superado los 30 minutos, preguntará si se debe cerrar la sesión. Si elige la opción "s", la sesión se cerrará con `pkill`.

[![Tiempo-Inactividad.png](https://i.postimg.cc/SRbCbT8N/Tiempo-Inactividad.png)](https://postimg.cc/jwvLzhZG)

> Bloque principal

Después de limpiar la pantalla y verificar que el usuario sea root, se elegirá una opción del menú desplegado. Dependiendo de la opción seleccionada, se llamará a la función correspondiente. El ciclo while asegura que el menú se muestre repetidamente hasta que se seleccione la opción de salir.

[![Menu.png](https://i.postimg.cc/B6HPpTyW/Menu.png)](https://postimg.cc/phVdXFRk)

##### RESULTADO

Para realizar este script, lo primero que hemos hecho es abrir otro usuario para poder comprobar el tiempo de inactividad, que está en 30 minutos. Para ello utilizaremos el comando `who -u`.

[![Usuarios-Activos.png](https://i.postimg.cc/9FJRLFxf/Usuarios-Activos.png)](https://postimg.cc/Whq13jcR)

Una vez realizada esta acción, hemos procedido a realizar el menú del script. Primero hemos seleccionado la opción 1, la de conocer los usuarios bloqueados y tal como muestra la imágen de abajo, en ese momento estaban bloqueados 2 usuarios: Heidi y Andrés2

[![Bloqueado-pc.png](https://i.postimg.cc/q7G93SJW/Bloqueado-pc.png)](https://postimg.cc/7CfsvBTN)

Posteriormente, con la elección de la opción 2, hemos bloqueado al un usuario: Andrés1 y lo verificamos volviendo a utilizar la opción 1 del menú, la de `usuarios bloqueados`,

[![Usuarios-Bloqueados.png](https://i.postimg.cc/gknmPzzj/Usuarios-Bloqueados.png)](https://postimg.cc/hfWHLq7R)
[![Usuarios-Bloqueados2.png](https://i.postimg.cc/76K8pcjS/Usuarios-Bloqueados2.png)](https://postimg.cc/nj9PmdFL)

Ahora desbloquearemos a un usuario (opción 3). El mismo que hemos creado. Un mensaje nos confirma la acción.

[![Desbloquear-Usuario.png](https://i.postimg.cc/kGNj0t2z/Desbloquear-Usuario.png)](https://postimg.cc/JHncJ0c5)

La opción de `cerrar usuario` lleva unas cuantas acciones que se certificarán con distintas imágenes. 
Lo primero que vamos a hacer es confirmar que tenemos un segundo usuario abierto.

[![Usuario-Abierto.png](https://i.postimg.cc/G2GzDsxt/Usuario-Abierto.png)](https://postimg.cc/TL2rM1Sv)

Si elegimos la opción 4 antes de que pasen los 30 minutos establecidos en el script, nos devolverá al menú escribiéndonos un mensaje en el que se dice que el usuario está activo.

[![Usuario-Activo.png](https://i.postimg.cc/KzSDm8h2/Usuario-Activo.png)](https://postimg.cc/QBmTbsM4)

Esperamos que transcurra la media hora y procedemos a elegir de nuevo la opción 4 del menú. En este caso nos preguntará si estamos seguro de querer cerrar la sesión, y le diremos que sí. 

[![Certificar-Cierre-Usuario.png](https://i.postimg.cc/vHq7czwX/Certificar-Cierre-Usuario.png)](https://postimg.cc/nCm9WqzQ)

Por último haremos un `last Pedro`, que nos señalará que el usuario Pedro ha terminado (killed) su sesión.

[![Killed-Usuario.png](https://i.postimg.cc/ncyzRj4p/Killed-Usuario.png)](https://postimg.cc/F7xN1KCB)


> * Código del script

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 2.0
# Fecha: 25/05/2024
# Descripción: Este script permite realizar tareas de administración de usuarios tales como: Ver los usuarios bloqueados, bloquear un usuario, desbloquear un usuario o cerrar su sesión.


## FUNCIONES

# Función para comprobar que el script sea ejecutado por root
tuSerRoot()
{
    if [ $(id -u) != 0 ]; then
      echo "Este script solo puede ser ejecutado por el root."
      exit 1
    fi
}


# Función para mostrar los usuarios bloqueados
mostrarUsuariosBloqueados() {
  # Obtener sólmente el nombre de todos los usuarios bloqueados del sistema
  passwd -S -a | grep " L " | cut -d " " -f1 > allUsersBlocked.txt
  # Obtener usuarios con UID entre 1000 y 2000 y lo guardamos en un archivo temporal
  awk -F ':' '$3 >= 1000 && $3 <= 2000 { print $1 }' /etc/passwd > allSystemUsers.txt
  # Comparamos y vemos de todos los usuarios que hemos creado están bloqueados
  usuariosBloqueados=$(grep -Fxf allSystemUsers.txt allUsersBlocked.txt)
  

  # Si hay usuarios bloqueados, mostrarlos
  if [ -n "$usuariosBloqueados" ]; then
    clear
    echo "Usuarios bloqueados:"
    for usuario in $usuariosBloqueados; do
      echo "- $usuario"
    done
  else
    clear
    echo "No hay usuarios bloqueados."
  fi


  rm allSystemUsers.txt allUsersBlocked.txt
}

# Función para bloquear un usuario
bloquearUsuario() {
  echo ""
  read -p "Introduce el nombre del usuario: " usuario
  if [ -z "$usuario" ]; then
    echo "Error: No se ha introducido un nombre de usuario válido."
    exit 1
  fi
  sudo usermod -L $usuario
  clear
  echo "Usuario '"$usuario"' bloqueado correctamente"
}

# Función para desbloquear un usuario
desbloquearUsuario() {
  echo ""
  read -p "Introduce el nombre del usuario: " usuario
  if [ -z "$usuario" ]; then
    echo "Error: No se ha introducido un nombre de usuario válido."
    exit 1
  fi
  sudo usermod -U $usuario
  clear
  echo "Usuario '$usuario' desbloqueado correctamente"
}

# Función para cerrar la sesión de un usuario inactivo
cerrarSesionUsuario() {
  echo ""
  # Solicitar nombre de usuario
  read -p "Introduzca el nombre de usuario a comprobar: " usuario

  # Comprobar si el usuario existe
  if [ -z "$usuario" ]; then
    echo "Error: No se ha introducido un nombre de usuario válido."
    exit 1
  fi

  # Obtener el tiempo de inactividad del usuario
  tiempoInactivo=$(who -u $usuario | awk '{ print $7 }')
  # Convertir el tiempo de inactividad a segundos
  tiempoInactivoSegundos=$(echo "$tiempoInactivo" | cut -d':' -f1,2 | awk '{total = $1 * 3600 + $2 * 60; print total}')

  # Comprobar si el tiempo de inactividad es superior a 30 minutos
  if [ $tiempoInactivoSegundos -gt 0 ]; then
    clear
    read -p "El usuario '$usuario' lleva más de 30 minutos inactivo. ¿Desea cerrar su sesión? (s/n): " cerrarSesion

    if [ "$cerrarSesion" = "s" ]; then
      pkill -KILL -u $usuario
      echo ""
      echo "Sesión del usuario '$usuario' cerrada."
    elif [ "$cerrarSesion" = "n" ]; then
      echo ""
      echo "La sesión del usuario '$usuario' no se ha cerrado."
    else
      echo ""
      echo "Opción no válida"
      exit 1
    fi
  else
    clear
    echo "El usuario '$usuario' está activo."
  fi
}


## BLOQUE PRINCIPAL

# Mostrar menú y ejecutar la opción seleccionada
clear
tuSerRoot
while [ true ]; do
  echo "Menú principal:"
  echo "==============="
  echo ""
  echo "1. Usuarios Bloqueados"
  echo "2. Bloquear un usuario"
  echo "3. Desbloquear usuario"
  echo "4. Cerrar sesión usuario"
  echo "5. Salir"
  echo ""
  read -p "Selecciona una opción: " opcion

  case $opcion in
    1) mostrarUsuariosBloqueados ;;
    2) bloquearUsuario ;;
    3) desbloquearUsuario ;;
    4) cerrarSesionUsuario ;;
    5) exit ;;
    *) clear && echo "Opción no válida";;
  esac
  echo ""
done

```
---

#### Ejercicio3.

###### ENUNCIADO

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

> Función  menú

[![menu.png](https://i.postimg.cc/DZgh27vj/menu.png)](https://postimg.cc/vgckhF6f)

> Bloque  principal

Llamamos a la función **tuSerRoot()** para que nos compruebe si somos o no usuario administrador. Luego, nos limpia la pantalla con un `clear` y ejecuta el menú.

[![menu.png](https://i.postimg.cc/DZgh27vj/menu.png)](https://postimg.cc/vgckhF6f)


##### RESULTADO

Comprobamos el correcto funcionamiento del script.

> Ejecutamos el script y éste nos muestra el menú.

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

###### ENUNCIADO

Crea en un script llamado crearUsuarios.sh que permita crear usuarios de forma automática. Indicaciones: 
1.- Al script se le pasan dos parámetros:

a) El primer parámetro representa el nombre de un usuario genérico.

b) El segundo parámetro representa el número de usuarios que  queremos que se creen.

##### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![encabezado.png](https://i.postimg.cc/VLDk1BG9/encabezado.png)](https://postimg.cc/MvfSmR8X)

Para que el script pueda ejecutarse, debe ser ejecutado por el usuario /root . Si no se es el usuario administrador, aparecerá un mensaje de denegación.

> Función comprobar root

[![tu-Ser-Root.png](https://i.postimg.cc/mr86dk8S/tu-Ser-Root.png)](https://postimg.cc/6740qWs7)

Con esta función mostramos en pantalla la sintaxis de cómo se deben introducir los datos.

> Función  mostar ayuda

[![mostrar-Ayuda.png](https://i.postimg.cc/bw48N6Qn/mostrar-Ayuda.png)](https://postimg.cc/hX8N21Z4)

Comprobamos los argumentos que se han introducido.

> Función  comprobar argumento

[![comprobar-Argumento.png](https://i.postimg.cc/nV3yhRxL/comprobar-Argumento.png)](https://postimg.cc/s1Z6nJvF)

Las siguientes variables lo que nos hace es que se guarden los parámetros 1 y 2 en un archivo con la fecha del día en que se ejecuta.

Cada vez que se crea un usuario nuevo, éste entrará a formar parte del archivo del día en el que se crea.

[![variables.png](https://i.postimg.cc/SQLvnZp9/variables.png)](https://postimg.cc/V56RTWvs)

La función validarNumero se encarga de limitar la creación de usuarios cuando se le pasa un parámetro para crear X cantidad de ellos, asegurándose de que no se creen más de 9.

> Función  validar número

[![validar-Numero.png](https://i.postimg.cc/G2Fqsp6R/validar-Numero.png)](https://postimg.cc/34wgs3Kf)

Cuando se crea un usuario, se le asigna una contraseña inicial que es igual a su nombre.

Al iniciar sesión por primera vez, el sistema obliga al usuario a cambiar su contraseña inicial por una nueva.

> Bloque principal

[![bloque-Principal.png](https://i.postimg.cc/HW3tGFg6/bloque-Principal.png)](https://postimg.cc/jCwfPgCf)

> Problema

En este script nos surgió un problema en la **función de borrarUsuario()**, y es que no se borrarban los usuarios. Después de mirar, volver a mirar y cambiar en varias ocaisones esa parte del script, nos dimos cuenta que sólo nos faltaba un guión para especificar un campo del usuario, sin el cuál no podía ejecutarse el comando.


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

###### ENUNCIADO

Partimos de la base que tenemos varios usuarios: usuario1, usuario2, usuario3. 

Al usuario1, se le ha establecido una cuota de disco: 40k y 100K (soft y hard respectivamente).

Realiza un script llamado cuotasUsuarios.sh, que nos copie la cuota del usuario1 a todos los usuarios cuyo uid >1000 y uid<2000.

###### DESARROLLO

Explicación de todas las partes del script.

En la siguiente imagen, vemos la cabecera que nos indica el tipo de archivo, los autores, la versión y la descripción de lo que hace el script.

[![encabezado.png](https://i.postimg.cc/RCXVcL73/encabezado.png)](https://postimg.cc/3W0Thpb7)

Para que el script pueda ejecutarse, debe ser ejecutado por el usuario /root . Si no se es el usuario administrador, aparecerá un mensaje de denegación.

[![tu-Ser-Root.png](https://i.postimg.cc/PqVnCSsh/tu-Ser-Root.png)](https://postimg.cc/JyX2gczd)

La función 'establecerCuotas' toma tres argumentos: el nombre de usuario y los límites soft y hard de la cuota de disco. Utilizando el comando 'edquota' para implantar estas cuotas para el usuario especificado.

[![establecer-Cuotas.png](https://i.postimg.cc/bwY511Rv/establecer-Cuotas.png)](https://postimg.cc/Ln7vpYwd) 

En la función principal observamos que se definen las cuotas para un usuario específico ('usuario1'), con un límite soft de 40 KB y un límite hard de 100 KB. Posteriormente, a través del comando cat, obtendremos una lista de usuarios cuyo nombre es "usuario", a los cuales se les establecerán las cuotas anteriormente indicadas. Corroborándose todo con el mensaje de que han sido establecidas.

[![principal.png](https://i.postimg.cc/XJ51nLns/principal.png)](https://postimg.cc/hzS1r9zm)

> Problema

A la hora de ejedcuar el script, nos encontramos con un problema: se establecían las cuotas a todos los usuarios con el UID entre 1000 y 2000 del root, no sólo a los llamados usuarios (que es lo que nos pedía el enunciado). Esto era debido a que la orden que pusimos en un principio en el script para obtener esta lista fue la siguiente:
local usuarios=$(awk -F '($3 >= 1000 && $3 < 2000) {print $1}' /etc/passwd), y no funcionaba.
Ante esto, la tuvimos que cambiar especificando, esta vez, el nombre de los usuarios que queríamos, buscando en el campo correspondiente del archivo passwd (el primero). Quedando tal y como se observa en el código.

###### RESULTADO

Primero se estableceremos las cuotas al usuario usuario1 con el comando edquota -u 1 en usuariol root. Observaremos este cambio con repquota -u | grep alumno1.

[![Ejecucion1.png](https://i.postimg.cc/HLnzshNp/Ejecucion1.png)](https://postimg.cc/5HcLsn1G)
[![Ejecucion2.png](https://i.postimg.cc/HLbwx121/Ejecucion2.png)](https://postimg.cc/GBh87NVX)

Seguidamente se establecerán las cuaotas a los siguientes dos usuarios con el mismo nombre. 

[![Ejecucion3.png](https://i.postimg.cc/5038B5TP/Ejecucion3.png)](https://postimg.cc/cgvKZ3sY)
[![Ejecucion4.png](https://i.postimg.cc/zfYh4P1t/Ejecucion4.png)](https://postimg.cc/75Xb29h0)

Finalmente, nos dará el resultado  de una lista con los usuarios que coinciden con los tres parámetros solicitados: nombre de "usuario", soft 40 KB y hard 100 KB. 

[![Resultado.png](https://i.postimg.cc/WpYks4vJ/Resultado.png)](https://postimg.cc/0rmQfPr8)

> * Código del script.
```bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 12/05/2024
# Descripción: Este script establece las cuotas a los usuario de nombre usuario
clear

##Parámetros/Variables

##Funciones

#Entrar como usuario
tuSerRoot()
{
	if [ `id -u` != 0 ]; then
        	echo "Este script solo puede ser ejecutado por el root."
        	exit 1
    	fi
}

#Establecemos las cuotas a los usuarios
establecerCuotas()
{
usuario="$1"
limiteSoft="$2"
limiteHard="$3"
	# Establecer la cuota utilizando edquota con los límites directamente
	edquota -u "$usuario" "${limiteSoft}" "${limiteHard}"
}
# Función principal del script
principal() {
    # Definir las cuotas del usuario1 
     usuario1="usuario1"
     limiteSoft=40
     limiteHard=100

    # Obtener la lista de usuarios con uid entre 1000 y 2000
     usuarios=$(cat /etc/passwd | awk -F: '{print $1}' | grep "usuario*")

    # Iterar sobre cada usuario encontrado
    for usuario in $usuarios
    do
        # Establecer la cuota para cada usuario
        establecerCuotas "$usuario" "$limiteSoft" "$limiteHard"

        # Mostrar un mensaje indicando que se estableció la cuota
        echo "Cuota establecida para $usuario: soft = $limiteSoft KB, hard = $limiteHard KB"
    done

    # Mostrar mensaje al finalizar
    echo "Proceso completado."
}

# Llamar a la función principal del script

principal

```

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
