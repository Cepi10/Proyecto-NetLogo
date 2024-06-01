NOMBRE:Cristian Sebastian Luna
CODIGO: 1004548668

[Uploading Fireworks.nlogo…]()

[MARCO_TEORICO.txt](https://github.com/user-attachments/files/15520279/MARCO_TEORICO.txt)[UplEste código en NetLogo simula un espectáculo de fuegos artificiales, donde cohetes explotan en
 el aire liberando partículas que se desvanecen gradualmente. A continuación, se presenta un 
marco teórico.

Marco Teórico
El modelo se basa en la física del movimiento bajo gravedad y la dinámica de partículas. Los 
cohetes (representados por tortugas de la breed rockets) se lanzan desde posiciones aleatorias 
y se mueven hacia abajo debido a la gravedad. Cuando alcanzan cierta velocidad vertical 
(definida por terminal-y-vel), explotan, liberando partículas (representadas por tortugas de la breed frags)
. Estas partículas tienen velocidades aleatorias y colores variados 
(En este caso fue modificado ah qeu solo las luces sean grises), creando un efecto visual de explosión.
 Con el tiempo, estas partículas desvanecen gradualmente hasta desaparecer.oading MARCO_TEORICO.txt…]()


Desarrollo del Código
1. Configuración inicial: La función setup configura el entorno, limpiando todo el mundo, 
estableciendo la forma predeterminada de las tortugas a "triangle" y reiniciando el contador de ticks.

2. Ejecución del modelo: La función go verifica si hay tortugas en el mundo. Si no hay ninguna, 
inicia una nueva salva de fuegos artificiales llamando a init-rockets y establece un temporizador 
(countdown) para la próxima salva. Si hay tortugas, ejecuta el movimiento de proyectiles y avanza 
el tiempo en 1 tick.

3. Inicialización de cohetes: init-rockets crea un número aleatorio de cohetes basado en el control 
deslizante FUEGOS ARTIFICIALES. Cada cohete tiene valores iniciales aleatorios para su posición, 
velocidad y color, y una velocidad de explosión (terminal-y-vel).

4. Movimiento de proyectiles: projectile-motion simula el movimiento de caída libre de las tortugas. 
Si la tortuga es un cohete, comprueba si ha alcanzado la velocidad de explosión. Si es un fragmento,
reduce su opacidad (dim) y cambia de color.

5. Explosión: Al explotar, un cohete genera un número de fragmentos (fragments) basado en el control 
deslizante FRAGMENTS. Cada fragmento tiene una velocidad aleatoria y se coloca en una dirección aleatoria.
 Algunos fragmentos pueden dejar un rastro si el control deslizante trails? está activado.

6. Desvanecimiento de fragmentos: Los fragmentos reducen su opacidad (dim) y cambian de color 
(EN este caso de un mismo color) gradualmente hasta desaparecer completamente.

(NOTA): Este modelo combina elementos de física, simulación de agentes y diseño gráfico para crear 
un espectáculo visual dinámico y atractivo. La interacción entre cohetes y fragmentos, junto 
con la variabilidad en sus propiedades, contribuye a la diversidad y belleza del espectáculo de fuegos
 artificiales simulado.
[DESARROLLO_DEL_CODIGO.txt](https://github.com/user-attachments/files/15520278/DESARROLLO_DEL_CODIGO.txt)

Link del programa que se modifico:
https://www.netlogoweb.org/launch#https://www.netlogoweb.org/assets/modelslib/Sample%20Models/Art/Fireworks.nlogo

CODIGO MODIFICADO

breed [ rockets rocket ]
breed [ frags frag ]

globals [
  countdown       ; Cuántos ticks esperar antes de una nueva salva de fuegos artificiales
]

turtles-own [
  col             ; Establece el color de una partícula de explosión
  x-vel           ; x-velocity
  y-vel           ; y-velocity
]

rockets-own [
  terminal-y-vel  ; Velocidad a la que explotará el cohete
]

frags-own [
  dim             ; Se utiliza para partículas que se desvanecen
]

to setup
  clear-all
  set-default-shape turtles "triangle"
  reset-ticks
end

; Este procedimiento ejecuta el modelo. Si no hay tortugas,
; iniciará una nueva salva de fuegos artificiales llamando a
; INIT-ROCKETS o continuar con la cuenta regresiva si no ha llegado a 0.
; A continuación, llama a PROYECTILE-MOTION, que se lanza y explota
; cualquier fuego artificial existente en la actualidad.
to go
  if not any? turtles [
    ifelse countdown = 0 [
      init-rockets
      ; Utilice una cuenta regresiva más alta para obtener una pausa más larga cuando se dibujan senderos
      set countdown ifelse-value trails? [ 30 ] [ 10 ]
    ] [
      ; Cuenta atrás antes de lanzar una nueva salva
      set countdown countdown - 1
    ]
  ]
  ask turtles [ projectile-motion ]
  tick
end

; Este procedimiento crea un número aleatorio de cohetes de acuerdo con el
; control deslizante FUEGOS ARTIFICIALES y establece todos los valores iniciales para cada fuegos artificiales.
to init-rockets
  clear-drawing
  create-rockets (random max-fireworks) + 1 [
    setxy random-xcor min-pycor
    set x-vel ((random-float (2 * initial-x-vel)) - (initial-x-vel))
    set y-vel ((random-float initial-y-vel) + initial-y-vel * 2)
    set col white
    set color (col + 2)
    set size 3
    set terminal-y-vel (random-float 4.0) ; ¿A qué velocidad explota el cohete?
  ]
end

; Esta función simula el movimiento real de caída libre de las tortugas.
; Si una tortuga es un cohete, comprueba si ha disminuido la velocidad lo suficiente como para explotar.
to projectile-motion ; turtle procedure
  set y-vel (y-vel - (gravity / 5))
  set heading (atan x-vel y-vel)
  let move-amount (sqrt ((x-vel ^ 2) + (y-vel ^ 2)))
  if not can-move? move-amount [ die ]
  fd (sqrt ((x-vel ^ 2) + (y-vel ^ 2)))

  ifelse (breed = rockets) [
    if (y-vel < terminal-y-vel) [
      explode
      die
    ]
  ] [
    fade
  ]
end

; Aquí es donde se crea la explosión.
; Las llamadas EXPLODE eclosionan un número de veces indicado por el control deslizante FRAGMENTS.
to explode ; Procedimiento de tortuga
  hatch-frags fragments [
    set dim 0
    rt random 360
    set shape "triangle"
    set size 5
    set color yellow
    set x-vel (x-vel * .5 + dx + (random-float 2.0) - 1)
    set y-vel (y-vel * .3 + dy + (random-float 2.0) - 1)
    ifelse trails?
      [ pen-down ]
      [ pen-up ]
  ]
end

; Esta función cambia el color de un fragmento.
; Cada fragmento desvanece su color en una cantidad proporcional a CANTIDAD DE FUNDIDO.
to fade ; Procedimiento de fragmentación
  set dim dim - (fade-amount / 10)
  set color scale-color col dim -5 .5
  if (color < (col - 3.5)) [ die ]
end
