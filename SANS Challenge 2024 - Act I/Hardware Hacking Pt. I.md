# Hardware Hacking Pt. I

En este nuevo desafío se plantea un problema de hardware hacking, en donde se quiere acceder al ***SLH - Access Card Maintenance Tool***, el cual es requerido para abrir el cofre de Santa, que contiene la lista de deseos. Este dispositivo dispone de una interfaz UART y, para hacer uso de ella, es necesario conectar un UART Bridge.

![[hh_1.png]]

El UART Bridge de la imagen está basado en un ficticio controlador NP2103, el cual es una parodia del muy conocido CP2103 de Silicon Labs. 

Tengo experiencia con este tipo de herramientas; conectarlas es un proceso trivial. El esquema de conexión para dos dispositivos mediante UART es:

![[uart.png]]

A esto debe sumarse el cable de alimentación **VCC** necesario para alimentar el dispositivo. Luego de conectar el puente UART, es necesario conectar el USB que conecta el puente con el **SLH**, pero antes cambié el switch que establece la corriente entre 5V y 3V a la posición de 3V. Es una buena práctica cuando uno no sabe el voltaje al que opera el dispositivo en cuestión.

![[hh_2.png]]

Ahora resta encender el dispositivo **SLH** presionando el botón verde **P**:

![[hh_3.png]]

Para configurar la comunicación UART, usé los datos que obtuve de la nota secreta. Estos fueron: 

- **Baud: 115200** 
- **Parity: Even**
- **Data: 7 Bits**
- **Stop Bits: 1 Bit**
- **Flow Control: RTS**

Y coloqué en el puerto: USB.

![[hh_4.png]]

![[hh_5.png]]

El elfo luego dice que quizá podría ser capaz de bypassear por completo el hardware para resolver el desafío:

![[hh_12.png]]

Luego de buscar en el código, encontré esto:

![[hh_9.png]]

Aquí se menciona que los elfos pudieron encontrar una forma de aplicar fuerza bruta utilizando la primera versión de la API (v1). Tal y como se puede ver, existe una constante URL similar a la que se utiliza ahora; para poder enviar la request a la API v1, dicha URL se construye de la misma forma que la que se usa actualmente con la v2. Lo único que cambia es la versión de la API, entonces bastaría con enviar la misma request cambiando "v2" por "v1".

La request en cuestión se ve así:

![[hh_7.png]]

Luego de ejecutarla, el servidor responde con:

![[hh_8.png]]

Así que decidí utilizar el interceptor de BurpSuite para capturar la request y modificar la URL de destino, para que sea enviada a la v1:

![[hh_10.png]]

![[hh_11.png]]

Luego de hacer forwarding a la request para que termine de ejecutarse, el desafío queda completado.