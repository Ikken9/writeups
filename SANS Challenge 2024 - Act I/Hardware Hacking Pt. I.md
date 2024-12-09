# Hardware Hacking Pt. I

En este nuevo desafío se plantea un problema de hardware hacking, en donde se quiere acceder al ***SLH - Access Card Maintenance Tool*** el cual es requerido para abrir el cofre de Santa, el cual contiene la lista de deseos. Este dispositivo dispone de una interfaz UART y para hacer uso de ella, es necesario conectar un UART Bridge.

![[hh_1.png]]

El UART Bridge de la imagen esta basado en un ficticio controlador NP2103, el cual es una parodia del muy conocido CP2103 de Silicon Labs. 

Tengo experiencia con este tipo de herramientas, conectarlas es un proceso trivial. El esquema de conexión para dos dispositivos mediante UART es:

![[uart.png]]

A esto debe sumarse el cable de alimentación \[VCC\] necesario para alimentar el dispositivo. Luego de conectar el puente UART, es necesario conectar el USB que conecta el puente con el **SLH**, pero antes cambie el switch que establece la corriente entre 5V y 3V a la posición de 3V, es una buena practica cuando uno no sabe el voltaje al que opera el dispositivo en cuestión.

![[hh_2.png]]

Ahora resta encender el dispositivo **SLH** presionando el botón verde **P**:

![[hh_3.png]]

Para configurar la comunicación UART, use los datos que obtuve de la nota secreta. Estos fueron: 

- **Baud: 115200** 
- **Parity: Even**
- **Data: 7 Bits**
- **Stop Bits: 1 Bit**
- **Flow Control: RTS**

Y colocando en el puerto: USB.

![[hh_4.png]]

![[hh_5.png]]




