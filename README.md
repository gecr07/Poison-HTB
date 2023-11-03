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






