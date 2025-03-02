<h1><b>Pyscho</b></h1>
<hr>

![Captura](https://github.com/user-attachments/assets/4290d8de-0c8d-42b3-9b46-cae794e60379)

<h2>Reconocimiento</h2>
<hr>
Realizamos un escaneo con nmap<br>

![nmap](https://github.com/user-attachments/assets/3d6ed8c0-60c4-47d1-876d-efe438932484)

Despues se suele realizar otro escaneo para ver la version y mas informacion que podamos sacar con los script pero para esta maquina no es necesario. Viendo el puerto 80 y 22 abierto procedemos a ingresar a la pagina web <br>

![pagina](https://github.com/user-attachments/assets/71ee3486-a379-4012-a71f-914940acd462)

Viendo la pagina ninguno de los botones funciona, y al final de esta pordemos observar:<br>

![image](https://github.com/user-attachments/assets/1d22b092-2a49-4f5a-b251-980d29c98a27)

Podemos observar que hay un ERROR al final de la pagina, esto podia indicar que algo se esta cargando mal, pero es toda la informacion que nos dan, para entender mejor como se este originando posiblemente esta falla necesitaremos encontrar la tecnologia en la que esta funcionando la pagina web:<br>

![image](https://github.com/user-attachments/assets/c5a59054-3ca3-4709-9e5c-9977be824c16)

![image](https://github.com/user-attachments/assets/bc2f061c-4341-4f48-a271-fff0b18eecd7)

whatweb no nos da mucha informacion pero con gobuster vemos index.php.

Entonces,que tenemos hasta el momento?
<ul>
  <li>La pagina esta cargando mal algo</li>
  <li>Esta usando php</li>
</ul>

Con esto podemos llegar a la conclusion que la pagina esta usando un include() en php en cual podemos derivarlo a un <a href="https://hacktricks.boitatech.com.br/pentesting-web/file-inclusion#file-inclusion" >Local File Inclusion </a>

No conocemos el parametro dentro de index.php, por lo tanto podemos proceder a fuzzear con la herramienta <a href="https://github.com/xmendez/wfuzz">wfuzz</a><br>

![image](https://github.com/user-attachments/assets/3cf9e007-1f79-43d1-b579-1cbcbe41ed8c)

Como podemos ver <b>secret</b> es el parametro que esta haciendo el include, primero veremos que usuarios hay en el sistema en /etc/cat

```bash
curl http://ip de la maquina/index.php?secret=/etc/passwd
```
![image](https://github.com/user-attachments/assets/f3820ae8-8ccb-46ee-aba3-6617ffb65217)

Podemos ver unas cuantas accounts, recordemos que la maquina cuenta con un servicio ssh, por lo tanto podemos realizar fuerza bruta a estas cuentas con hydra o buscar por las llaves. <br>

```bash
/home/luisillo/.ssh/id_rsa
/home/vaxei/.ssh/id_rsa
```
<br>
Probamos con las 2 y encotramos la llave para vaxei<br>
```bash
curl http://ip de la maquina/index.php?secret=/home/vaxei/.ssh/id_rsa
```
<br>
![image](https://github.com/user-attachments/assets/18b82ab6-82a0-4771-9ffb-0d3a714c0ae1)
<br>

Ahora entonces teniendo el ssh podriamos logearnos al menos que nos pidan un frase para ingresar.

>[!tip] nombre que quieras
>El archivo id_rsa debe tener permisos 600





