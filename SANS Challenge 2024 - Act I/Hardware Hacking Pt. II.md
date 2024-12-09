# Hardware Hacking Pt. II

Para esta parte, se requería acceder a una terminal y una base de datos para otorgar acceso al numero de tarjeta 42.

![[hh2_1.png]]

Para ello debía utilizar la aplicación de **SLH**, el problema es que tiene una contraseña y debía encontrarla primero.

![[hh2_2.png]]

Al clickear en el dispositivo, se abre una terminal con U-Boot, el cual es un popular bootloader utilizado en dispositivos embebidos y soporta multiples arquitecturas.

![[hh2_3.png]]

**SLH** presenta un comando `--help` para mostrar una ayuda:

![[hh2_5.png]]

Los detalles de una tarjeta pueden verse ejecutando `slh --view-card ID`, y para ver la numero 42 basta con ejecutar `slh --view-card 42`:

![[hh2_4.png]]

Si se ejecuta `ls` sobre el directorio actual puede verse el archivo `access_cards` que a su vez puede ser identificado mediante el uso de `file` para comprobar que tipo de archivo es:

![[hh2_6.png]]

Se trata de una base de datos **SQLite 3.x** la cual contiene a todas las tarjetas.

Aparte de eso, existe la siguiente pista:

![[hh2_7.png]]

Dice que a veces la contrasena puede verse loggeada en algun tipo de historial y que puede resultar trivial encontrarla. Lo primero que pense fue el historial de la shell, en este caso es una bash, por defecto se encuentra en el directorio home del usuario, asi que con utlizando `ls -a` se deberia de poder encontrar y acto seguido mostrar su contenido:

![[hh2_8.png]]

En efecto es tal como lo pensé, ademas no solo esta la contraseña sino muchos otros comandos que pueden dar indicios acerca de lo que se ha estado 
haciendo en el sistema.
Para otorgar acceso a la tarjeta 42, ejecute `slh --passcode CandyCaneCrunch77 --set-access 1 --id 42`

![[hh2_9.png]]

El acceso fue otorgado exitosamente.





(42, 'c06018b6-5e80-4395-ab71-ae5124560189', 0, 'ecb9de15a057305e5887502d46d434c9394f5ed7ef1a51d2930ad786b02f6ffd')
