# Elf Minder 9000

### Objetivo
El objetivo es completar el juego.

### Procedimiento
Primero intenté jugar un poco el juego para ver cómo era. Completé los 12 niveles y al final se desbloquea un nivel 13 que no puede completarse utilizando las herramientas que el juego proporciona.

En segundo lugar, abrí BurpSuite para intentar ver alguna Solicitud/Respuesta relacionada con el juego. Había muchas respuestas, pero me enfoqué en las que parecían útiles:

- https://hhc24-elfminder.holidayhackchallenge.com/game2.js: contiene toda la [[Elf Minder 9000 Code#Game Logic|lógica del juego]].
- https://hhc24-elfminder.holidayhackchallenge.com/levels.js: contiene múltiples arrays de datos desde los cuales se crean todos los [[Elf Minder 9000 Code#Levels|niveles del juego]].
- https://hhc24-elfminder.holidayhackchallenge.com/guide.js: contiene información del juego, como un mapeo de entidades del juego a números, y sirve como una [[Elf Minder 9000 Code#Guide|guía]].

Luego también revisé la solicitud que se envía a https://hhc24-elfminder.holidayhackchallenge.com/game/submit cuando el juego comienza. Allí está incluido el camino que dibujé para ganar el primer nivel. El camino consiste en un array que contiene las coordenadas por las que el jugador pasa para alcanzar la meta.

También me di cuenta de que solo se envían solicitudes cuando el juego comienza y termina, pero no mientras se ejecuta. Esto me permitió modificar lo que ocurría en el medio mientras el elfo se movía por el mapa, reemplazando algunos objetos por otros para lograr llegar a la meta y luego enviar el resultado con la victoria.

Elegí jugar "Sandy Start", y esto es lo que puede verse en la parte superior del cuerpo de la solicitud:

![[sandy_start_1.png]]

Llegué a la conclusión de que estas entidades eran el **punto de inicio** y el **punto final**:
- `[1,5,0]`: entidad `START` en las coordenadas `(1,5)`.
- `[11,5,1]`: entidad `END` en las coordenadas `(11,5)`.

![[entity_types.png]]

También había un array vacío llamado "clicks", que es el que almacena las rotaciones que el jugador hace mientras juega.

En la parte inferior del archivo `guide.js` había una `const` que era bastante extraña:

![[springs.png]]

y un ASCII art que se imprime en la consola cuando se llama a la función.

El profesor nos dio una pista: los resortes son la única entidad que puede colocarse en los bordes de cada cuadrado que compone el terreno (quizá porque la aplicación no los maneja correctamente). Así que, definitivamente, los resortes debían de jugar un papel importante.

Luego de leer el código en `guide.js`, vi que los resortes en el juego actúan como mecanismos para redirigir al héroe dependiendo de su posición y los segmentos o entidades conectadas. Entonces, entender la lógica del juego para calcular los objetivos de los resortes (`getSpringTarget`) podía servirme para alcanzar la meta.

Al final resolví el nivel utilizando los resortes y rotando los segmentos de las líneas rojas para guiar al Elfo desde el punto de inicio hasta la meta (es el mismo procedimiento que con el resto de niveles), juntando todas las cajas en el camino. Cuando el Elfo llegó al resorte que esta abajo de la piedra, fue redirigido hacia arriba como indica la flecha azul con un 1, aterrizando en la caja que estaba en la parte superior de la pantalla y juntándola. Después de esto, roté el segmento de la línea roja conectado a la caja para que apuntara hacia abajo, permitiendo que el Elfo regresara al segundo resorte, cuando el Elfo alcanzó el segundo resorte, fue lanzado en diagonal hacia abajo y a la izquierda como indica la segunda flecha azul que tiene un 2, aterrizando en la línea roja cerca de la meta. Finalmente, el Elfo siguió esta línea roja directamente hasta la meta, completando el nivel. 

![[elf_minder_9000_solution.png]]
