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


<h2>FootHold</h2>
<hr>


