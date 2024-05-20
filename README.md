<!--Encabezado (Header)-->

# PROYECTO SCRIPT INTERMEDIO

<!---PORTADA->
<!--PARA GENERAR ENLACE A UNA IMAGEN-->
![imagen de portada](https://media.dev.to/cdn-cgi/image/width=1600,height=900,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F5hhrcbgvutmdmducqkek.png "BASH LOGO")

<!--INDICE-->

## INDICE
1. CONTENIDO

    1.[Ejercicio1](#Ejercicio1)

    2.[Ejercicio2](#Ejercicio2)

    3.[Ejercicio3](#Ejercicio3)

    4.[Ejercicio4](#Ejercicio4)

    5.[Ejercicio5](#Ejercicio5)

2. BIBIOGRAFÍA

3. CONCLUCIONES
---
<!--CONTENIDO-->

### CONTENIDO

#### Ejercicio1.
Programación en crontab de ejecutarse cada 6 horas todos los días.
También que se ejecute a los 5 minutos cuando se reinicie el ordenador si éste se apaga.

> *Entramos con **crontab-e** y en la última línea insertamos las tareas que nos piden los ejercicios.*

[![Captura1.png](https://i.postimg.cc/4xmb77ZD/Captura1.png)](https://postimg.cc/0z1JhNzn "/tmp/contrab")

Se observan dos ejemplos en los que se explica el orden de los datos: minuto, hora, dia mes, mes, dia semana,  de cuándo se ejecutarán, por lo que no hay ninguna dificultad en configurar lo que se nos pide:
    
1. Ejecutará una tarea cada 6 horas todos los días.

2. Si el ordenador está apagado se ejecutará a los 5 minutos de iniciarse.

> *Me encontré con un problema a la hora de entrar en el crontab en el root. Estaba configurado con el editor vim, en el cual no se podía escribir nada, además de quedarse pillado. Por lo que tenía que cerrar el terminal y volver a abrirlo para continuar con las tareas. Gracias al profesor, Jose Luis Boa, se soluciona el problema con la orden `“export EDITOR=nano”`, pudiendo terminar el ejercicio sin ningún problema.*


* ##### Copia y pega el siguiente script y comprueba que funciona en tur ordenador.
  
```bash
clear

##Parámetros/Variables

##Funciones
# Función para comprobar si Apache2 está activo
tuSerRoot()
{
    if [ `id -u` != 0 ]; then
        echo "Este script solo puede ser ejecutado por el root."
        exit 1
    fi
}

apacheActivo()
{
  systemctl status apache2 | grep "active (running)" > /dev/null
  return $?
}

# Función para registrar error y reiniciar Apache2
reiniciarApache()
{
  fechaActual=$(date +%d-%m-%Y_%H:%M)
  echo "Error-Apache: $fechaActual" >> /home/blackmirror/Desktop/tuto/scripts/administracionIntermedia/proyecto/ApacheError.tmp
  systemctl restart apache2
}


##Bloque principal
tuSerRoot
# Comprobar cada minuto si Apache2 está activo
while [ true ]; 
do
  if ( ! apacheActivo ); then
    reiniciarApache
  fi
  sleep 60
done

```
---

#### Ejercicio2.

*La realización de este ejercicio no hemos tenido mucha dificultad*

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 18/04/2024
# Descripción: Este script nos permite administrar usuarios (ver usuarios bloqueados, bloquear usuario, desbloquear usuario o cerrar la sesión de un usuario).
clear

##Parámetros/Variables


##Funciones
tuSerRoot()
{
    if [ `id -u` != 0 ]; then
        echo "Este script solo puede ser ejecutado por el root."
        exit 1
    fi
}

# Función para mostrar usuarios bloqueados
mostrarUsuariosBloqueados()
{
    echo ""
    awk -F: '$3 == "locked" && $1 > 1000 && $1 < 2000 { print $1 }' /etc/passwd
}

# Función para bloquear un usuario
bloquearUsuario()
{
    echo ""
    read -p "Introduce el nombre del usuario: " usuario
    usermod -L $usuario
}

# Función para desbloquear un usuario
desbloquearUsuario()
{
    echo ""
    read -p "Introduce el nombre del usuario: " usuario
    usermod -U $usuario
}

# Función para cerrar la sesión de un usuario inactivo
cerrarSesionUsuario()
{
    echo ""
    read -p "Introduce el nombre del usuario: " usuario
    tiempoInactivo=$(who -u $usuario | awk '{print $2}')

    if [ $tiempoInactivo -gt 1800 ]; then
        pkill -KILL -u $usuario
        echo "\nSesión cerrada para $usuario"
    else
        echo "\nEl usuario $usuario no lleva más de 30 minutos inactivo."
    fi
}


##Bloque principal
tuSerRoot
# Mostrar menú y ejecutar la opción seleccionada
while [ true ]; do
    echo "Menú principal"
    echo "==============="
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
        5) exit 0;;
        *) clear && echo "Opción no válida\n";;
    esac
done
```

---

#### Ejercicio3.
Para deshabilitar las cuenta de usuario existente con fecha de expiración y borrar los usuarios almacenados en **/root/usuarios.csv**. tendremos que crear un script que deshabilita y borra los usuarios de dicho archivo en la fecha señalada, y será  el que configuraremos en el crontab-.

Lo hemos tenido que hacer así, pues si lo hacíamos directamente en el script,  la orden sólo servía para deshabilitar y borrar un usuario, no el conjunto de ellos.

[![Captura2.png](https://i.postimg.cc/tCH4nLbp/Captura2.png)](https://postimg.cc/DmBTtpmM)

> *A la hora de insertar esta tarea en el crontab, volvió a darme problemas el editor. Volvía a estar como predefinido el vim. Esta vez, insertamos una línea en el crontab `@reboot export EDITOR=nano`, para que no pudiera volver a cambiarse*.

* ##### Copia y pega el siguiente script y comprueba que funciona en tur ordenador.

```bash
#!/bin/bash
# Authors: David R. , Ivana S. , Andrés R.
#!/bin/bash
# Authors: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 15/05/2024
# Descripción: Este script deshabilitará y borrara usuarios en una fecha determinada
clear
# Deshabilitar todas las cuentas de usuario existentes con fecha de expiración
deshabilitarUsuarios() 
{
    	for user in $(cut -d: -f1 /etc/passwd); do
        	sudo chage -E $(date -d "30 Jun 2024" +%Y-%m-%d) $usuario
     
    	done
}

# Borrar usuarios almacenados en /root/usuarios.csv
borrarUsuarios() 
{
	# Leer línea por línea del archivo usuarios.csv
    	while IFS=: read -r nombreUsuario _ _ _ _; do
 		echo "Eliminando usuarios: $nombreUsuario"
    		
        	sudo deluser --remove-home "$nombreUsuario"
    	done < ./usuarios.csv
}

# Llamar a las funciones para deshabilitar y borrar usuariosds
deshabilitarUsuarios
borrarUsuarios

```
---
#### Ejercicio4.

*La realización de este ejercicio no hemos tenido mucha dificultad*

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 18/04/2024
# Descripción: Este script crea usuarios masivamente indicando un nombre genérico y la cantidad total de usuarios que se quieren crear.
clear

##Parámetros/Variables
fechaActual=$(date +%d_%m_%Y-%H:%M)
i=1
nombre="$1"
cantTotal="$2"
cantTotal=$(($cantTotal+1))

##Funciones
tuSerRoot()
{
    if [ `id -u` != 0 ]; then
        echo "Este script solo puede ser ejecutado por el root."
        exit 1
    fi
}
mostrarAyuda() {
    echo ""
    echo "Número de argumentos incorrecto."
    echo "Uso: sh $0 <nombre genérico> <numero de usuarios a crear>"
    echo "Ejemplo: sh $0 usuario 3; usuario1, usuario2, usuario3"
    echo ""
    exit 1
}
comprobarArgumentos()
{
    if [ "$#" -ne "2" ]; then
        mostrarAyuda
	fi
}

##Bloque principal
tuSerRoot
comprobarArgumentos $@

until [ $i = $cantTotal ]; do
    #useradd -f -m -s /bin/bash -f 0 $(mkpasswd -m sha-512 $1$i) $1$i > /dev/null 2>&1
    echo "Creando usuario '$1$i'..."
    echo $1$i >> usuarios.tmp
    usuarios=$(cat usuarios.tmp | grep -v / | sort | paste -sd ':')
    i=$(($i+1))
done

rm usuarios.tmp
echo "\n"
echo "Usuarios añadidos\n------------------\n$usuarios" > usuariosCreados-$fechaActual.tmp
cat usuariosCreados-$fechaActual.tmp
```

---


#### Ejercicio5.
Primero establecemos las cuotas al usuario Alumno 1 con el comando `edquota -u alumno1` en root.

[![Captura3.png](https://i.postimg.cc/P5CXghzb/Captura3.png)](https://postimg.cc/tsHG6Kc7)

Con `repquota -u / | grep alumno1` observamos el cambio

[![Captura4.png](https://i.postimg.cc/mDBGgGM5/Captura4.png)](https://postimg.cc/v1qK0k0r)

Ejecutamos.

[![Captura5.png](https://i.postimg.cc/QCCwFNM0/Captura5.png)](https://postimg.cc/BPWMy0HK)

[![Captura6.png](https://i.postimg.cc/pTFk49pN/Captura6.png)](https://postimg.cc/9RcGqQd1)

[![Captura7.png](https://i.postimg.cc/RVNqBcvn/Captura7.png)](https://postimg.cc/hfRK8QzS)

Nos encontramos con el problema que a la hora de  ejecutarse este script, nos daba las cuotas de todos los usuarios con uid entre 1000 y 2000 del root, fuera cual fuera su nombre. Esto era debido a la que la orden que pusimos en un principio en el script para obtener esta lista fue la de `local usuarios=$(awk -F: ‘($3 >= 1000 && $3 < 2000) {print $1}’ /etc/passwd)`.

[![Captura8.png](https://i.postimg.cc/Zq7K2RXL/Captura8.png)](https://postimg.cc/DWGTb2vW)

Así que la cambiamos para especificar el nombre de los usuarios que queríamos por esta otra, que busca en el campo primero del el archivo passwd  todo el que tenga el nombre de usuario.

`local usuarios=$(cat /etc/passwd) | awk -F: ‘{print $1}’ | grep “usuario *` tal y como se observa en la captura de abajo. 

[![Captura9.png](https://i.postimg.cc/RVB9PKNB/Captura9.png)](https://postimg.cc/BtMdb8TY)

Para visualizar todas las tareas programadas haremos un `crontab -l`.

[![Captura1.png](https://i.postimg.cc/4xmb77ZD/Captura1.png)](https://postimg.cc/0z1JhNzn)

* ##### Copia y pega el siguiente script y comprueba que funciona en tur ordenador.

```bash
#!/bin/bash
# Author: David R. , Ivana S. , Andrés R.
# Versión: 1.0
# Fecha: 12/05/2024
# Descripción: Este script comprueba cada minuto si Apache está activo
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
    local usuarios=$(cat /etc/passwd | awk -F: '{print $1}' | grep "usuario*")

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

> **A raiz de esté proyecto, hemos aprendido varias herramientas, y una de ellas es está misma plataforma *GITHUB*, con la que hemos podido compartir nuestros proyectos y conocimiento. Por otra parte, después de realiar el trabajo de script nivel intermedio/abanzado hemos ido desarrollando mayor soltura en la automatización de tareas en */bin/bash*, por lo que, apartir de ahora si quisieramos realizar ciertas tareas en nuestros ordenadores no tendremos que preocuparnos de hacerlas nosotros mismos, podemos programarlas.**
