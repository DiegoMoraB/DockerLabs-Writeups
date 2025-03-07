<h1>WalkingCMS</h1>
<hr>

![image](https://github.com/user-attachments/assets/5cc1132d-f3cf-4faf-a761-11e365515ce4)

<h2>Reconocimiento</h2>
<hr>
Realizamos un escaneo con nmap

<br>

![image](https://github.com/user-attachments/assets/594a29cc-1674-47fe-ab74-c0edabe559e1)

Encontramos el puerto 80 abierto, lo que significa que existe un servicio http y/u pagina web.
Por la tanto accedemos a este.

![image](https://github.com/user-attachments/assets/65e56ea0-f959-423b-8759-aab8fbcfb42e)

Apache2 Debain Defult Page, es el index html que viene por defecto cuando se usa el servicio Apache2.

Procedermos a buscar en el codigo, no parece que encontremos algo, y realizaremos una enumeracion de directorios.
```bash
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://172.17.0.2/ -t 100 -x php,html,git,js
```

![image](https://github.com/user-attachments/assets/153142ab-a095-445a-927e-d9727a209f4c)

Con exito encontramos una pagina WordPress y accedemos a esta.

>[!] Info
>
>WordPress es un Gestor de Contenido, es una herramienta que nos facilita la creacion de paginas webs, ademas de que incluye muchas herramientas para esto mismo, y suelen tener vulnerabilidades, sobre todo por los plugins.

<br>
<h2>Enumeracion en WordPress</h2>
<hr> 
Existen diversas formas de enumerar WordPress, hay algunos archivos o directorios sensibles que podemos buscar dentro de este, como lo pueden ser:
<li>/wp -admin</li>
<li>/wp-login.php</li>
<li>/wp-content/plugins</li>
<li>/xmlrpc.php</li>
<br>

>[+] Tip
>
>Si recien empiezas en el mundo de ciberseguridad es aconsejable tratar de buscar formas de explotar archivos como <a href="https://nitesculucian.github.io/2019/07/02/exploiting-the-xmlrpc-php-on-all-wordpress-versions/" target="_blank">xmlrpc.php</a> de manera manual.

<br>

En esta ocasion para enumerar WordPress haremos uso de una herramienta, la cual es <b>wpscan</b>.
Nos ayudara a encontrar usuarios, plugins vulnerables, themes vulnerables, etc.

```bash
wpscan --url http://172.17.0.2/wordpress -e vp,u --api-token=XXXXXXXXXXXXXXXXXXXXXXXX
```
Este <a href="https://www.malcare.com/blog/how-to-use-wpscan/">link</a> te ayudara a conseguir tu api token que es completamente gratis
![image](https://github.com/user-attachments/assets/927a0fc2-e15b-4daa-b882-384907bf19a4)

<br>

Con esto nosotros ya podemos realizar fuerza bruta al usuario <b>mario</b>.
```bash
wpscan --url http://172.17.0.2/wordpress -U mario -P /usr/share/wordlists/rockyou.txt --api-token=XXXXXXXXXXXXXXXXXXXXXXXX
```
![image](https://github.com/user-attachments/assets/9fcf560f-5906-43fa-8d4b-783972cb3be7)

Accedemos a wp-admin.
```bash
http://ip/wordpress/wp-admin
```
![image](https://github.com/user-attachments/assets/310c1773-ce90-48a1-913d-4fa6a1c9d0ee)

<h2>FootHold</h2>
<hr>

Ahora buscaremos conseguir una shell para obtener acceso al sistema, recordemos que no encontramos plugins vulnerables con nuestra herramienta wpscan.
Veamos el apartado de Theme Editor.

![image](https://github.com/user-attachments/assets/38fa937f-1136-41d5-9e20-2d485f206167)

En este apartado podemos ver que nos permite modificar el codigo del tema que se encuentra activo.
Entre todos los archivo, el mas interesante parece ser el de <b>index.php</b>, ya que si de alguna manera podemos acceder a ese archivo, ejecutaremos ese codigo en el servidor permitiendonos una reverse shell.

Una vez estando en el archivo index.php copiaremos el codigo de este <a href="https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php">enlace</a>, modificando los valores de IP y PORT con los nuestros.

![image](https://github.com/user-attachments/assets/ccbc9167-d462-4529-866f-c9d5dafa76ac)

![image](https://github.com/user-attachments/assets/65b26f64-98c8-4a6a-9f69-ea3d219c4ae2)

>[!] Recordar
>
>La ip que pondras en la reverse shell debe ser tu ip en la red de docker, si no la conoces puedes poner ifconfig.

De esta manera procederemos le daremos al boton, update file.

Despues de guardar nuestra reverse shell, iremos a la siguiente ruta.

```bash
/wp-content/themes/twentytwentytwo/index.php o 404.php
```
Esta ruta suele ser tipica para los themes en WordPress.

