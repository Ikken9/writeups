# Frosty Keypad

En este desafío es necesario acceder al ***Shredder McShreddin 9000***, el elfo asociado con la mision llamado Morcel Nougat menciona que existe un libro que puede ayudar a recuperar el documento. Encontré el libro cerca:

![[book.png]]

El enlace vinculado a *Read the Book...* redirige a https://frost-y-book.com/, en donde se encuentra el libro.

En el keypad existe una nota pegada:

![[keypad_note.png]]

Aquí los primeros números de cada grupo de números coincidía con la cantidad de paginas que contiene el libro. Ademas, el numero de grupos de números coincide con la cantidad de números que admite el Keypad. Buscando en las paginas del libro que coinciden con el primer numero, las palabras que coinciden con el segundo numero y las letras que coinciden con el tercer numero, obtuve:

2:6:1 = S
4:19:3 = A
6:1:1 = N
3:10:4 = T
14:8:3 = A

En las pistas se menciona el uso de una linterna ultravioleta para alumbrar al teclado y así reducir al menos la cantidad de números que pueden ser utilizados en la clave, observando las marcas que la luz revela sobre los botones. Tras horas de buscar la linterna, pude encontrarla detrás de una caja cerca del **Shredder McShreddin 9000**:

![[flashlight.png]]

Al utilizar la linterna, las teclas usadas se revelan:

![[fl1.png]]
![[fl2.png]]
![[fl3.png]]
![[fl4.png]]


Son entonces los números \[2, 6, 7, 8\]. Esto concuerda con lo que esta escrito en una parte del código:

![[keys.png]]

Como los caracteres que admite son 5, quiere decir que hay un numero que se repite y que puede coincidir con las letras que componen la palabra "SANTA", entonces eso me llevo a pensar que el numero asociado con la letra A es el que se repite.
 
Asigne a cada letra de la palabra SANTA un numero basado en el orden en que aparecen en el alfabeto, es decir, la letra que aparece primero en el alfabeto de las que componen a la palabra "SANTA", le asigne el numero mas chico de los posibles. Repeti el proceso con todas las letras, como resultado obtuve: \[7 2 6 8 2\], coloque eso en el teclado, presione Enter y al primer intento lo consegui

![[password.png]]

Luego el elfo menciona el cifrado Ottendorf, que en efecto es el mismo que se utiliza para obtener la contraseña a partir del libro.

![[ottendorf.png]]

> [!NOTE] Ottendorf Cipher
> An Ottendorf cipher, also known as a book cipher, is a method of encryption that uses a book or other text as the key. The cipher identifies letters in the text using a series of three numbers:
>
>- **First number**: Identifies the line in the text
>- **Second number**: Identifies the word in that line
>- **Third number**: Identifies the letter in that word

También menciona que existe otro código, pero que requerirá algo mas de esfuerzo para poder crackearlo:

![[one_more_code.png]]

![[shredded_pieces.png]]

Hacer click en el link inicia una descarga de un comprimido .zip llamado "shreds.zip"

El contenido del comprimido son muchos archivos con extension .jpg:

![[files.png]]

![[file.png]]

También pude notar que los nombres los archivos parecen tener un orden según el nombre, y utilizan los mismos caracteres que base16 o HEX, parecían ser UUIDs. El output también indica que las imágenes tienen una dimension de 1 x 1000 pixeles, 1 pixel de ancho y 1000 pixeles de alto. 

Existe una pista que contiene un link hacia un [Gist con un script en Python](https://gist.github.com/arnydo/5dc85343eca9b8eb98a0f157b9d4d719) que permite unir las piezas de la imagen:

![[cutting_edge.png]]

Como resultado obtuve:

![[assembled_image.png]]

En la nota se puede ver **"Baud: 115200"**, "**Parity: Even**", "**Data: 7 Bits**", "**Stop Bits: 1 Bit**" y "**Flow Control: RTS**".


