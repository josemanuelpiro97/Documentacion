# Core Bash

+ Bash es un ejecutable binario que toma los comandos que el usuario ingresa y los convierte en llamados al sistema operativo. Es, en algun punto, un lenguaje que sirve de pegamento para otros softwares.

---

## Globbing and Quoting

### Globing

+ Uno de las características que nos ofrece bash es el Globbing **" * "**

+ Este comando se podría interpretar como un **"todo"**

+ Por ejemplo podríamos utilizarlo de la siguiente manera:

        $ touch file1 file2 file3
        $ ls *
        file1 file2 file3        

+ La consola toma el caracter y lo convierte para que coincida con todos los files en el directorio actual

+ otros caracteres de globing pueden ser los corchetes **"[ ]"** estos pueden ser utilizados de la siguiente manera

        ls file[a]
        ls file[a-n]
        ls file[0-9]

Aquí podemos ver tres lineas, en la primera busca los archivos que **comiencen con la palabra file y termine con a**, en la segunda los que **comiencen con file y terminen con alguna letra entre la a y la n**, y en la tercer fila buscara los archivos que **comiencen con file y terminen con un numero del 0 al 9**

### Quoting

+ Otra de las características que nos ofrece bash es el citado, este puede estar constituido por comillas simples **' '**, o por comillas dobles **" "**. Ambas tendrán diferentes utilidades al momento de citar, mas adelante cundo se describe el uso de variables quedara mas claro la distinción.

### Differences with Regular Expressions

+ Hay una diferencia muy importante entre las expresiones regulares y el globing **"( * )"** y es que unas son interpretadas por un software o comando, mientras que el otro es interpretado por la consola, para hacer esta distinción debemos hacerle Quoting a nuestro globbing, ara que de esta manera el **" * "** no sea interpretado por bash:

        rename -n 's/(.*)/new$1/' *

en este ejemplo podemos ver como el primer  **" * "** es interpretado por el comando rename como una expresión regular y el segundo **" * "** como una globbing en bash.

---

## Variables

+ La creación de ua variable en bash tiene el siguiente formato:

        VARIABLE_NAME=variable_value

Por convención el nombre de las variables es en mayúsculas, luego la asignación debe ser sin espacios.

### Variables and Quoting

+ En caso de que queramos poner toda una cadena de texto en una variable y esta cadena contenga espacio deberé realizar un **Quoting del valor con comillas dobles**:

        VARIABLE_NAME="value1 value2 value3"

+ En caso de que queramos poner dentro del valor de una variable el valor de otra, deberemos tener en cuenta **2 cosas.** la primera es que **para invocar el contenido de una variable se utiliza el caracter " $ "**, la segunda cosa a tener en cuenta es que **para definir una cadena donde dentro esta la invocación a la otra variable debo usar comillas dobles, porque las simples anulan la lectura de bash:**

        $ var1=1
        $ VAR2="la primer variable es: $VAR1"
        $ VAR3='una mala invocación de VAR1 seria asi $VAR1'
        $ echo $VAR2
        la primer variable es 1
        $ echo $VAR3
        una mala invocación de VAR1 seria asi $VAR1

### Shell Variables

+ Shell por defecto define sus propias variables, estas no pueden ser modificadas y desactivadas debido a que son del tipo **readonly**, nosotros podemos definir nuestras propias variables **readonly** de la siguiente manera:

        readonly VAR1=value1

### environment variables

+ Al definir variables normales, estas solo podrán ser accedidas por la sesión de bash actual.

+ Shell posee variables de entorno, estas son variables que pueden ser utilizadas desde cualquier proceso hijo de la sesión actual de bash, nosotros podemos definir variables de entorno para una sesión de bash en particular mediante la utilización del comando **export**:

        export VAR1=value1

De esta manera, cualquier proceso que sea iniciado desde esa sesión de bash tendrá a disposición las variables de entorno definidas

+ Para ver todas las variables de entorno tenemos a deposición el comando **env**, el cual listara todas las variables de entorno que hay en la actual sesión de bash

+ En caso de que queramos ver no solo las variables de entorno sino que también las variables locales no exportadas, tenemos a disposición el comando **compgen -V**

### Arrays

+ En primer instancia cabe aclarar dos cosas, **los arreglos en bash son indexados en 0**, y que **todas las variables pueden ser tratadas como un arreglo de 1 elemento**

+ Pero para la asignación y escritura de un arreglo se comienza por el index 1(recordar que para acceder a una posición del arreglo debo indicar  indice con **" [ ] "** e indicarle a bash que vas a preguntar por un indice del arreglo utilizando las llaves **" { } "** ):

        $ VAR1[1]=1
        $ VAR1[2]=2
        $ echo ${VAR1[1]}
        1
        $ echo ${VAR1[2]}
        2        

### Associative Arrays

+ bash nos proporciona la capacidad de asociar algún string a un indice mediante el uso del comando **declare:**

        declare -A VAR1=([uno]=1 [dos]=2 [tres]=3)
        VAR1[uno]=1
        VAR1[dos]=2
        VAR1[tres]=3
        echo ${VAR1[uno]}${VAR1[dos]}${VAR1[tres]}

---

## Funciones

+ Las funciones en bash pueden ser de gra utilidad, una ves creadas pueden invocarse como un comando regular.

+ hay varias cuestiones a tener en cuenta para definir una variable:

  + **debe haber un espacio entre el corchete inicial del cuerpo y la primer linea de ejecución de la función**
  + **luego de cada linea de instrucción debemos poner un " ; " para indicar que finaliza**
  + **el cuerpo de la función es declarado entre llaves**
  + **en caso de querer borrar una función usaremos el comando "unset -f"**

### Argumentos

+ en bash las funciones tienen sus **argumentos autoindexados, va desde 1 a n**

+ pueden ser utilizados de la siguiente manera:

        $ function myfunc { echo $1 ; echo $2 ;}
        $ myfunc "parametro 1" "parametro 2"
        parametro 1
        parametro 2

### Alcance de las variables

+ En bash el alcance de las variables no tiene nada de especial, **una variable creada desde fuera de la función puede ser vista desde dentro y viceversa**

+ Bash nos proporciona la capacidad de crear variables locales dentro de las funciones mediante el premodificador **local**:

        $ function func { local VAR1="variable local" ; echo $VAR1 ; }
        $ func
        variable local
        $ echo VAR1

        $

Como podemos ver, el ultimo "echo" no arroja nada, dado que invocamos el contenido de una variable local declarada dentro de la función, lo que quiere decir que una ves terminada la función, esta morirá.

---

## Pipes and Redirects

### Basic Redirects

+ El redireccionamiento esta basado en el caracter **" > "**, este en su manera mas básica puede **tomar la salida de un comando e insertarla en un nuevo archivo especificado**

        echo "cadena de texto" > file1

+ En caso de que el el archivo ya exista, lo pisara con la nueva informaron, si queremos evitar esto y que se escriba en una siguiente linea, usaremos **" >> "**

+ Cuando utilizamos el caracter **" > "**, implícitamente estamos usando **" 1> "**, es decir el **standard output file descriptor** (revisar apéndice)

### otros tipos de redireccionamiento

+ Antes de leer esta sección, leer el apéndice de **file descriptor**

+ Podemos utilizar otros formatos de redireccionamiento, uno comúnmente utilizado es el **" 2> "**, el cual redirecciona **standard error**, como ejemplo podría ser:

        command_doesnt_exist 2> /dev/null

+ Otro redireccionamiento utilizado es **" 2>&1 "**, este redireccionamiento lo que hace es **decirle al shell que envíe la salida en el error estándar (2) a cualquier salida estándar de punto final que se señale en ese punto del comando**.  
 Para entender esto tenemos el siguiente ejemplo:

        $ command_does_not_exist 2>&1 > outfile
        command not found: command_does_not_exist
        $ cat outfile

        $ command_does_not_exist > outfile 2>&1
        $ cat outfile
        command not found: command_does_not_exist

Aquí podemos ver que en ambos casos el redireccionamiento funciono correctamente, pero no nos dio el mismo resultado, esto es debido a que como dijimos anteriormente **"se mapea la salida de error a la salida estándar"** por consiguiente:

+ 1) en la primera linea la salida standard se mapea a la shell, dado que el comando no existe, por consiguiente la salida errónea también es mapeada a la shell.

+ 2) mientras que en la 4 linea la salida standard es mapeada al file, por consiguiente la salida errónea es mapeada al file.

### Basic pipe

+ La tubería esta basada en el caracter **" | "**, el cual toma la **salida estándar** de un **comando** y lo inserta como el argumento de **otro comando**

        $ echo " cadena " | grep -c cadena
        1

+ Una cuestión a tener en cuenta es que los comandos unidos por el pipe son independientes en si, uno podría dar error, y el otro ejecutarse.

### Estándar Output vs Standard Error

+ Cundo algún comando tiene algún tipo de error, este tendrá u output, pero no sera **standard output** sin que sera **standard error** . Estos dos tipos viajan por canales separados, es decir:

        $ doesnt_exist | grep -c algo
        bash: doesnt_exist : command not found
        0

+ como podemos ver, **el mensaje de error no ingresa al grep**, esto es debido a que viajan por canales distintos

### diferencia entre pipe y redireccionamiento

+ El pipe pasa un **stadard output** a un **standard input** de otro comando
+ El redireccionamiento pasa un **standard output** a un **archivo**

+ Um buen uso conjunto de los dos comandos, podría ser el siguiente

        grep -c file1 < file2

  Aquí tomaremos al file2 como el **standard input** del grep, es equivalente a usar

        cat file2 | grep -c file      

---

## Apéndice

### File descriptors

+ Hay 3 tipos de descriptores de archivos, estos son:
  + **standard input (0)**
  + **standard output (1)**
  + **standard error (2)**

### Functions, Builtin, Aliases and Programs

+ Hay al menos 4 maneras de hacer llamados en bash, la diferencia es sutil pero importante.

**Builtin:** son comandos que vienen incorporados en bash, ejemplos de estos pueden ser **cd**, **ls**,  etc

**functions** con respecto a las funciones, estas pueden sobrescribir a una builtin, dado esto, para estar seguro que llamamos a la builtin agregamos el prefijo **builtin**

        builtin cd

**aliases** las aliases son cadenas que bash toma y reemplaza por lo que sea que este definido como alias, los alias se declaran a través del comando **alias**

        alias move=cd
        move /home/jpiro

**Programs** Los programas son archivos ejecutables como grep, vim, etc.

**TIPS**
Algunos comandos de utilidad pueden ser

+ **declare -f** : te muestra las funciones definidas en tu entorno
+ **declare -F** : te muestra ls nombres de las funciones definidas en tu entorno
+ **type** : indica el tipo del comando, si es alias, si es builtin, etc
+ **which** : comando para determinar donde se almacena el archivo en tu sistema de archivos
