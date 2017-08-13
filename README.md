# Una clase sobre Lexers

Esto es un pequeño repaso de distintas estrategias para implementar un Lexer
para algún lenguaje de programación determinado (entre otras cosas).


## 1. Autómata Implícito

```sh
# Rust
cd lex1

vim src/lexer_lisp.rs

cargo test lex1 -- --nocapture
```

Una manera muy simple y _ad hoc_ de implementar un lexer.
La idea principal es ciclar sobre cada caracter y acumular un lexeme
dependiendo de las reglas del lexer. Una vez que se completa un lexeme
se clasifica y se genera un Token.


#### Ventajas
- Hay un paralelo con un autómata que reconoce los tokens
- Mas fácil para principiantes que el resto
- El manejo de transiciones lambda es un poco mas simple


#### Desventajas
- El código se complica y pierde estructura fácilmente
- Es difícil de entender el flujo para reconocer un solo tipo de token



## 2. Autómata Explicito: Directamente Codificado

```sh
# Python
cd lex2

python direct_coded_automata.py
```

Se crea una autómata no determinístico lambda con una función de transición extendida,
que ademas de especificar el próximo estado, nos especifica la acción que se debe ejecutar.
Estas acciones típicamente incluyen acumular un lexeme, clasificar un token, terminar un token,
o error.

El autómata tiene la particularidad que sabe como interactuar con cadenas de caracteres, en lugar de un
caracter por ves. El es el encargado de procesar el próximo caracter o no (transiciones lambda y transiciones
no determinístico)

#### Ventajas
- Es una implementación de un autómata:
  - Mas fácil de estructurar que el caso (1)
  - El código tiende a ser mas simple y mas fácil de entender

#### Desventajas
- Es una implementación de un autómata finito no determinístico lambda:
  - Se necesita representar transiciones lambda
  - Se necesita representar algunos casos no determinístico
- Como no es un autómata puro desde el punto de vista teórico, la implementación puede ser mas dificultosa
- Es directamente codificado lo cual lo hace mas difícil de entender que el caso (3)


Notas
- Dibujar el autómata

## 3. Autómata Explicito: Dirigido por Tabla

```sh
# Rust
cd lex3

cargo test lex_ok -- --nocapture

# Python
cd lex2
```

La única diferencia con el caso (2) es que se separan claramente las reglas que componen la funciona de transición extendida
y el motor que procesa esas reglas. De esta manera la arquitectura del programa esta mejor organizada, y las responsabilidades
están bien definidas.

Ventajas
- Las mismas del caso (2)
- Al ser dirigido por tabla tenemos una clara separación entre la función de transición y el "motor" que corre esas reglas

Desventajas
- Las mismas del caso(2)
- Requiere una mejor estructuración del código para que pueda procesar reglas genéricas y homogéneas en forma.


## 4. Composición de Autómatas

```sh
# Rust
cd lex4

# Version parcial
cargo test simple_lexer -- --nocapture
```

La idea principal es especificar una expresión regular por cada tipo de Token que se quiere reconocer, estas
expresiones regulares se convierten en Autómatas Finitos Determinístico a los cuales se les da como entrada
caracteres. La cadena mas larga reconocida por un solo Autómata antes de que todos los autómatas queden
en un estado trampa sera nuestro lexeme, luego se clasifica y se convierte en un Token.


Ventajas
- Con unas pocas abstracciones se puede crear un generador de lexers para distintos lenguajes
- Posee una interfaz muy simple, donde cada token es representado por una RegExp

Desventajas
- Para poder crear algo genérico se debería tener un motor de RegExp, lo cual es una cantidad de trabajo considerable
- No es recomendable para un lexer de un lenguaje en particular


Notas
- lex (un generador de lexers) usa una estrategia similar
- El autómata que se usa en los casos (1), (2) y (3) es esencialmente una composición de los autómatas de cada token
