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

Procedermos a buscar en el codigo y no parece que encontremos algo, y realizaremos una enumeracion de directorios.
```bash
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://172.17.0.2/ -t 100 -x php,html,git,js
```

