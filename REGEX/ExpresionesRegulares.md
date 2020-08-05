# Expresiones Regulares

---

## Componentes de las Expresiones Regulares

### Literales

Cualquier caracter se encuentra a si mismo a menos que se trate de un **metacaracter**

### Secuencia de escape

Las expresiones regulares nos permiten el uso de secuencias de escape, tales como el salto de linea **"\n"**, el tabulador **"\t"**, la barra diagonal inversa **"\\\\'**, la tabulacion vertical **"\v"**, etc

### Clase de caracteres

Se pueden pueden especificar clases de caracteres encerrando una lista de caracteres entre corchetes **"[ ]"**. Si el primer símbolo después del **"["** es un **"^"**, la clase encuentra cualquier caracter que no este en la lista.

### Metacaracteres

Estos son la esencia de los lenguajes regulares, en la siguiente sección se explica con detalle.

---

## Metacaracteres (Explicación)

### Delimitadores

Nos permiten delimitar donde queremos buscar los patrones de búsqueda.

| Metacaracter  |  Descripción                              |
|---            |---                                        |
| ^             | inicio de linea                           |
| $             | fin de linea                              |
| \A            | inicio de texto                           |
| \Z            | fin de texto                              |
| .             | cualquier caracter en la linea            |
| \b            | encuentra limite de palabra               |
| \B            | encuentra distinto a limite de palabra    |

### Clases Predefinidas

Estas son clases de caracteres predefinidas

| Metacaracter  |  Descripción |
|---|---|
| \w  | un caracter alfanumérico (incluye "-")  |
| \W  | un caracter NO alfanumérico  |
| \d  | un caracter numérico  |
| \D  | un caracter NO numérico  |
| \s  | cualquier espacio (lo mismo que [\t\n\r\f])  |
| \S  | un NO espacio  |

### iteradores

Permiten especificar el numero de ocurrencias del caracter previo

| Metacaracter  | Descripción  |
|---|---|
| *  | cero o mas  |
| +  | uno o mas |
| ?  | cero o una  |
| {n}  | exactamente n veces  |
| {n,}  | n veces o mas  |
| {n,m}  | de n a m veces  |
| *?  | cero o mas  |
| +?  | cero o una  |
| ??  | cero o una  |
| {n}?  | exactamente n veces  |
| {n,}?  | n veces o mas  |
| {n,m}?  | de n a m veces  |

### alternativas

se puede especificar alternativa usando **"|"** para separar c/u.Por lo tano "do|re|mi" encontrara cualquier 'do", "re" o "mi" en el texto.

Las alternativas se evalúan de izquierda a derecha.

### sub-Expresiones

la secuencia **(...)** puede ser empleada para definir una sub-expresión, para que quede mas claro mirar el siguiente ejemplo:

+ (foobar){10} --> encuentra cadenas que contengan 10 instancias de 'foobar'

+ foob([0-9]|a+)r --> encuentra 'foob0r', 'foobar', 'foobaaaar', etc

### memorias

Los caracteres \1 a \9 son interpretados como memorias, \ encuentra la sub-expresión previamente encontrada, para que quede mas claro mirar los siguientes ejemplos:

+ (.)\1+ --> encuentra 'aaaa', 'cc', etc

+ (.+)\1+ --> encuentra 'abab', '123123', etc

---