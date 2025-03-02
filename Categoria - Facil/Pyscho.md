<h1><b>Pyscho</b></h1>
<hr>

![Captura](https://github.com/user-attachments/assets/4290d8de-0c8d-42b3-9b46-cae794e60379)

<h2>Reconocimiento</h2>
<hr>
Realizamos un escaneo con nmap
<br>

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
<br>
<h2>Foothold</h2>
<hr>

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

Ahora entonces teniendo el ssh podriamos logearnos al menos que nos pidan una frase para ingresar.

>[!] Tip
>
>El archivo id_rsa debe tener permisos 600.
>
>Algunos id_rsa estan cifrados con un passphrase, que en ciertos casos necesitaras para que tu key sea aceptada<br>


![image](https://github.com/user-attachments/assets/919a1435-03f1-475a-a017-f15b279e44d6)

<br>
<h2>Lateral Movement</h2>
<hr>

De las primeras cosas que se suelen hacer en una maquina suele ser

```bash
sudo -l
id
Revisar /var/www/html
ps aux
etc
```

Con sudo -l nos saldra todos los permisos que podemos ejecutar como sudo si es que existe alguno, encontramos:
<br>

![image](https://github.com/user-attachments/assets/68746a92-227e-4048-b633-7394437417ff)

<br>

Ya que podemos ejecutar perl como luisillo, y al ser perl un lenguaje de programacion el proceso de escalado es sencillo con <a href="https://gtfobins.github.io/#">GTFOBins</a>


```bash
sudo -u luisillo perl -e 'exec "/bin/sh";'
```
<br>

![image](https://github.com/user-attachments/assets/cf2a6c26-88a3-4e52-b6f7-0c6ccd0094d2)

<br>
<h2>Escalada de Privilegios</h2>
<hr>

Igual que el anterior probramos sudo -l <br>

![image](https://github.com/user-attachments/assets/6f5712f2-6a00-45c4-8b06-edc0cb77f940)

<br>

Al parecer sudoers nos permite ejecutar el script mas no modificarlo.

```python
import subprocess
import os
import sys
import time

# F
def dummy_function(data):
    result = ""
    for char in data:
        result += char.upper() if char.islower() else char.lower()
    return result

# Código para ejecutar el script
os.system("echo Ojo Aqui")

# Simulación de procesamiento de datos
def data_processing():
    data = "This is some dummy data that needs to be processed."
    processed_data = dummy_function(data)
    print(f"Processed data: {processed_data}")

# Simulación de un cálculo inútil
def perform_useless_calculation():
    result = 0
    for i in range(1000000):
        result += i
    print(f"Useless calculation result: {result}")

def run_command():
    subprocess.run(['echo Hello!'], check=True)

def main():
    # Llamadas a funciones que no afectan el resultado final
    data_processing()
    perform_useless_calculation()
    
    # Comando real que se ejecuta
    run_command()

if __name__ == "__main__":
    main()
```

Viendo el codigo vemos que las librerias se estan insertando de forma insegura, es ahi donde nosotros haremos un <a href="https://deephacking.tech/path-hijacking-y-library-hijacking/">Library Hijacking</a>.<br>

Crearemos un archivo llamado subprocess.py de esta forma:

```python
import os
os.system("/bin/bash -p")
```
<br>
Ejecutamos de esta manera:
<br>

![image](https://github.com/user-attachments/assets/0623128d-d221-4f67-a670-a838b457fcb2)

<br>

y de esta manera finalizamos la maquina.

HackTheBox: billyelcalvo
