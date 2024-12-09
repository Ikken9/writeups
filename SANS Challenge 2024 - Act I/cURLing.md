# cURLing

Comenzando con el nuevo Acto I, se presenta el primer desafío llamado *cURLing*, para superarlo es necesario utilizar la herramienta **cURL**, para enviar requests a un servidor remoto.

## **1. Primer desafío**
En primer lugar me puse a investigar que es lo que tenia al alcance, al parecer **cURL** estaba instalado. Mire que había en el directorio actual con el comando `ls -a`, pude ver que habían varios archivos, entre ellos HARD-MODE.txt y HELP, los cuales decidí no abrir y centrarme en lo que pedía el desafío en las letras celestes.

![[curling1.png]]

El primer desafío dentro de la maquina era conectarse a un servidor remoto y obtener la pagina web en el host  "curlingfun" mediante el puerto 8080. El puerto 8080 generalmente es usado por servidores HTTP. Conectarse es bastante sencillo, basta con ejecutar `curl -v http://curlingfun:8080`. La flag `-v` indica que el output debe ser verboso.

![[assets/curling2.png]]


## **2. Segundo desafío**
El siguiente desafío pide que obtengamos la pagina web protegida por TLS ubicada en `https://curlingfun:9090`. Al principio pensé que seria buena idea crear un certificado SSL/TLS self-signed. Para ello verifique si `openssl` estaba instalado en la maquina, y efectivamente lo estaba.

![[curling4.png]]

Un certificado self-signed puede ser creado con `openssl` creando una llave RSA privada para luego a partir de ella, crear el certificado:

1. `openssl genrsa -out rootCA.key 2048` 
   
   ![[curling5.png]]
2. `openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem` 
   
   ![[curling6.png]]

El problema de todo esto, es que **cURL** no confía en el certificado self-signed, fallando en comprobar la legitimidad del server y por lo tanto no pudiendo establecer una conexión con el servidor.

![[curling7.png]]

Fue entonces que recordé que una conexión insegura puede ser establecida con el servidor y que no implica el uso de certificados SSL/TLS, para ello basta con agregar la 
flag `-k` al momento de utilizar **cURL**. Basta con ejecutar `curl -v -k https://curlingfun:9090`:

![[curling8.png]]

![[curling9.png]]


## **3. Tercer desafío**
El tercer desafío requería realizar un POST request hacia `https://curlingfun:9090` con un parámetro "skip" seteado con el valor "alabaster", para ello **cURL** dispone de una flag  `--data` para enviar parámetros, entonces el comando resulta en `curl -v -k https:curlingfun:9090 --data "skip=alabaster"`:

![[assets/curling10.png]]

![[curling11.png]]

## **4. Cuarto desafío**
Ahora se pide el envío de una solicitud a la misma dirección pero esta vez conteniendo una cookie "end" con un valor igual a "3", ante mi desconocimiento sobre como hacer esto, consulte utilizando la pagina de **cURL** que dispone la herramienta **man**: 

![[curling12.png]]
 
 Entonces, esto puede lograrse tal y como se ve en la imagen, con la flag `--cookie` y los datos en un formato `"NAME=VALUE"`, asi que basta con 
 ejecutar `curl -v -k https://curlingfun:9090 --cookie "end=3"`:
 
 ![[curling13.png]]

![[curling14.png]]

La request es exitosa.


## **5. Quinto desafío**
En este desafío, se requiere obtener los headers que resultan de enviar una request a `https://curlingfun:9090`, nuevamente consulte el manual de **cURL** utilizando **man**:

![[curling16.png]]

Como se puede ver, los headers pueden ser volcados en un archivo de salida, así que probe ejecutando `curl -v -k https://curlingfun:9090 --dump-header headers.txt`

![[curling17.png]]

![[curling18.png]]

![[curling19.png]]

## **6. Sexto desafío**
En lugar de obtener los headers de la request, se pide enviar una request con un header llamado "Stone" con el valor "Granite", para ello nuevamente consulte el manual de **cURL**:

![[curling15.png]]

Tal y como se puede ver, un header custom puede adjuntarse en la request con el formato `"HEADER: VALUE"`, entonces ejecute `curl -v -k https://curlingfun:9090 --header "Stone: Granite"` 

![[curling20.png]]

![[curling21.png]]


## **7. Séptimo Desafío**
Ahora se nos pide que obtengamos una respuesta proveniente de `https://curlingfun:9090/../../etc/hacks`, el problema es que cURL hace un manejo para los caracteres especiales evitando que URLs como las del ejemplo puedan enviarse sin alguna flag en especial. La flag para evitar este tipo de procesamiento es `--path-as-is`, entonces el comando a ejecutar es `curl -v -k --path-as-is 'https://curlingfun:9090/../../etc/hacks'`:

![[curling22.png]]

![[assets/curling23.png]]

Aquí es donde terminaba el desafío.


## **(\*) Hard Mode**
Hice un `cat` del archivo `HARD-MODE.txt` del principio para ver el contenido:

![[curling24.png]]

Reutilizando lo que use para resolver los anteriores desafíos, ejecute `curl -v -k https://curlingfun:9090/ --data "skip=bow" --cookie "end=10" --header "Hack: 12ft"`

![[curling26.png]]

El comando fue ejecutado exitosamente, ahora se pide que `curl -v -k --path-as-is 'https://curlingfun:9090/../../etc/button'`

![[curling27.png]]

Por ultimo, se pide acceder a la pagina que resulta de la redirección desde `https://curlingfun:9090/GoodSportsmanship`, para ello ejecute `curl -v -k -L https://curlingfun:9090/GoodSportsmanship`, la flag `-L` habilita la redirección.

![[curling30.png]]










 
 




