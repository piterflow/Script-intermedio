[![bashf.jpg](https://i.postimg.cc/cHwn3gQL/bashf.jpg)](https://postimg.cc/qhBqV7bP)
# Script-intermedio

### Ejercicio 1.
Realiza un script llamado comprobarApache.sh, que compruebe cada minuto si el
servicio apache2 está activo (running).
Si está parado, entonces:

1.- Introduce una línea: “Error-Apache: Fecha y hora actual” en /root/ApacheError.tmp,
donde FechaActual, representa día, mes, año, hora y minuto.

2.- Reinicia el servicio apache2
Para comprobarlo, para el servicio. Ejecuta el script en segundo plano y observa si lo
reinicia y crea el archivo.

3.- Además del script, crea una tarea programada, de forma que ese script se ejecute cada
6 horas, todos los días. Y si el ordenador está apagado, se debe ejecutar la próxima vez
que se inicie, transcurrido cinco minutos.

<hr>

### Ejercicio2.

Realiza un script llamado usuariosBloqueados.sh, que nos muestre un menú:
1.- Usuarios Bloqueados.
2.- Bloquear un usuario.
3.- Desbloquear usuario.
4.- Cerrar sesión usuario
5.- Salir
Cada opción del menú corresponde con una función.

UsuariosBloqueados → nos muestra en pantalla los usuarios (uid>1000 y <2000) que
tengan la cuenta bloqueada.

BloquearUsuario → Nos pregunta el nombre de un usuario y lo bloqueamos.

DesbloquearUsuario → Nos pregunta el nombre de un usuario y lo desbloqueamos.

CerrarSesion → Nos pregunta el nombre de un usuario, y si el usuario lleva más de 30
minutos (1800 seg) sin actividad, se le cierra la sesión.
<br>
<hr>

### Ejercicio 3.

Realiza un script llamado crearBorrarUsuarios.sh, que nos muestre un menú:
<br>
1.- Crear Usuarios.
<br>
2.- Borrar Usuarios.
<br>
3.- Salir
<br>
<br>
CrearUsuarios → Crea de forma masiva usuarios almacenados en el fichero 
/root/usuarios.csv
<br>
<br>
[![usuarios.png](https://i.postimg.cc/T3BjWXZQ/usuarios.png)](https://postimg.cc/18cVLbRN) 
<br>
<br>
Los campos son los siguientes:
- El campo 1 representa el nombre de usuario.

- 
- El campo 2 representa la contraseña.


- El campo 3 representa el nombre.


- El campo 4 representa su primer apellido.


- El campo 5 representa su correo electrónico.


Además, queremos que esas cuentas queden inactivas el 30 de junio de 2024.
BorrarUsuarios → Borra de forma masiva usuarios almacenados en el fichero
/root/usuarios.csv.

<hr>

### Ejercicio 4.


