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
