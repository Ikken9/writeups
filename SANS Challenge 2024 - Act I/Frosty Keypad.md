# Frosty Keypad

En este desafío es necesario acceder al ***Shredder McShreddin 9000***. El elfo asociado con la misión, llamado Morcel Nougat, menciona que existe un libro que puede ayudar a recuperar el documento. Encontré el libro cerca:

![[book.png]]

El enlace vinculado a *Read the Book...* redirige a [https://frost-y-book.com/](https://frost-y-book.com/), en donde se encuentra el libro.

En el keypad existe una nota pegada:

![[keypad_note.png]]

Aquí, los primeros números de cada grupo de números coincidían con la cantidad de páginas que contiene el libro. Además, el número de grupos de números coincide con la cantidad de números que admite el Keypad. Buscando en las páginas del libro que coinciden con el primer número, las palabras que coinciden con el segundo número y las letras que coinciden con el tercer número, obtuve:

- 2:6:1 = S
- 4:19:3 = A
- 6:1:1 = N
- 3:10:4 = T
- 14:8:3 = A

En las pistas se menciona el uso de una linterna ultravioleta para alumbrar el teclado y así reducir al menos la cantidad de números que pueden ser utilizados en la clave, observando las marcas que la luz revela sobre los botones. Tras horas de buscar la linterna, pude encontrarla detrás de una caja cerca del **Shredder McShreddin 9000**:

![[flashlight.png]]

Al utilizar la linterna, las teclas usadas se revelan:

![[fl1.png]]
![[fl2.png]]
![[fl3.png]]
![[fl4.png]]

Son entonces los números \[2, 6, 7, 8\]. Esto concuerda con lo que está escrito en una parte del código:

![[keys.png]]

Como los caracteres que admite son 5, quiere decir que hay un número que se repite y que puede coincidir con las letras que componen la palabra "SANTA". Entonces, eso me llevó a pensar que el número asociado con la letra A es el que se repite.

Asigné a cada letra de la palabra "SANTA" un número basado en el orden en que aparecen en el alfabeto. Es decir, a la letra que aparece primero en el alfabeto de las que componen la palabra "SANTA" le asigné el número más chico de los posibles. Repetí el proceso con todas las letras. Como resultado, obtuve: \[7, 2, 6, 8, 2\]. Coloqué eso en el teclado, presioné Enter y, al primer intento, lo conseguí:

![[password.png]]

Luego, el elfo menciona el cifrado Ottendorf, que en efecto es el mismo que se utiliza para obtener la contraseña a partir del libro:

![[ottendorf.png]]

> [!NOTE] Ottendorf Cipher
> Un cifrado Ottendorf, también conocido como cifrado de libro, es un método de encriptación que utiliza un libro u otro texto como clave. El cifrado identifica letras en el texto usando una serie de tres números:
>
> - **Primer número**: Identifica la línea en el texto.
> - **Segundo número**: Identifica la palabra en esa línea.
> - **Tercer número**: Identifica la letra en esa palabra.

También menciona que existe otro código, pero que requerirá algo más de esfuerzo para poder crackearlo:

![[one_more_code.png]]

![[shredded_pieces.png]]

Hacer clic en el enlace inicia una descarga de un comprimido .zip llamado "shreds.zip".

El contenido del comprimido son muchos archivos con extensión .jpg:

![[files.png]]

![[file.png]]

También pude notar que los nombres de los archivos parecen tener un orden según el nombre y utilizan los mismos caracteres que base16 o HEX. Parecían ser UUIDs. El output también indica que las imágenes tienen una dimensión de 1 x 1000 píxeles, 1 píxel de ancho y 1000 píxeles de alto.

Existe una pista que contiene un enlace hacia un [Gist con un script en Python](https://gist.github.com/arnydo/5dc85343eca9b8eb98a0f157b9d4d719) que permite unir las piezas de la imagen:

![[cutting_edge.png]]

Como resultado obtuve:

![[assembled_image.png]]

En la nota se puede ver **"Baud: 115200"**, **"Parity: Even"**, **"Data: 7 Bits"**, **"Stop Bits: 1 Bit"** y **"Flow Control: RTS"**.
