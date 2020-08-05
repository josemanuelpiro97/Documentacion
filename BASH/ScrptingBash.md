# Scripting Bash

## Scripts and Startup

### The Shebang

El **Shebang** o mas conocido **Hashbang**, es la combinación de los caracteres **" #! "**. Estos dos caracteres dentro de un archivo ejecutable, al principio del mismo, indican que al sistema operativo, que deberá **correrlo bajo el control de otro programa**.

+ como ejemplo podría ser:

        echo #!/bin/bash > script

### Bandera de Ejecución

Si al crear nuestro script, no le establecemos la bandera de ejecutable, la consola nos largara un error. Esto lo podemos hacer mediante el comando:

        chmod +x script_name

+ Este comando le cambia los permisos a un archivo, y el parámetro **" +x "** le agrega el permiso de **ejecutable**

### La Variable PATH

Para ejecutar un script de bash, deberemos utilizar los caracteres **" ./ "** de la siguiente manera

        ./script_name

+ Debemos usar ese conjunto de de caracteres debido a que debemos indicar la **ruta hacia el script**, y eso depende de como este configurada la variable **PATH**

Para ver como esta configurada la variable **PATH** se puede hacer lo siguiente
        echo $PATH

+ la salida de ese echo nos mostrara los directorios **donde bash busca para encontrar comandos, en ese orden**, esta variable es configurada por defecto por el **startup scripts**

En conclusión, si queremos que nuestros scripts corra sin tener que indicar la ruta actual, **tendré que agregar la ruta actual al PATH**

        PATH = ${PATH}:.

### Startup Scripts

 Cuando **bash inicia** (lo mismo para sh y para zsh), este **llama y ejecuta una serie de archivos** que preparan el entorno en el que vamos a trabajar

### Cuando Ejecutas Bash

Como se menciono anteriormente, al momento de iniciar, bash ejecuta una serie de archivos, estos archivos dependerá de varias cosas y en un orden en particular:

+ lo primero es si bash correrá de manera **local** o **remota**, ya q si corre de manera remota estará bajo **rsh/ssh**

+ lo segundo es si bash esta en modo **login** o **non-login**, en caso de que ejecutemos **/bin/bash** desde otra shell, por ejemplo, estaremos accediendo al modo non-login

+ por ultimo es ver si bash es de **tipo interactivo o no**, en caso de que este corriendo un script, por ejemplo, sera non-interactive.

+ luego de esas 3 cosas hay una serie de archivos, que en caso de no encontrarse se ignoran.

Hay un **comando builtin** que pone en manifiesto esto de la construcción del entorno al que llegaremos, este comando es **source**.

        $ MIVAR=hello
        $ echo 'echo $MIVAR' >simple_scripr
        $ chmod +x simple_script
        $ ./simple_script
        
        $ source simple-scrit
        hello

+ Lo que sucede aquí es que de manera convencional, cuando ejecutamos un script con **" ./ "** o llamándolo por su nombre (en caso de que este configurado el PATH), se producirá un **fork del proceso init y se asignara un PID**, por lo que el entorno sera distinto al de la shell que lo invoco, dejando sin valor a la variable **MIVAR**. En cambio, con **source** el script se ejecutara en la shell actual, **conservando el entorno**.

En caso de **querer evitar todos estos scipts de inicializacion** para asi tener un **bash en estado mas puro**, lo que deberemos hacer es ejecutar bash de la siguiente manera

        env -i bash --noprofile --norc

+ **env** es un programa que funciona en el medio ambiente. El efecto del indicador -i es eliminar las variables de entorno del comando que ejecuta. Esto significa que las variables exportadas no serán heredadas por el programa bash que estamos ejecutando. Estamos ejecutando el programa bash en sí con dos banderas. El indicador --noprofile le dice a bash que no obtenga los archivos de inicio de bash en todo el sistema, y ​​el --norc le dice a bash que no obtenga los personales presentes en su carpeta de inicio.

---

## Sustitución de Comandos

Al escribir scripts de bash, a menudo desea tomar el **resultado estándar** de un comando y **"soltarlo"** en el script como si lo hubiera escrito en él.

### Dos métodos de Sustitución de Comandos

#### El método Dollar-Bracket

+ Este caracter permite obtener el regular output de cualquier comando, o el contenido de alguna variable

  + En el caso de una variable, una de las maneras de utilizarlo es la siguiente

         echo $MIVAR

     En el caso de que estemos tratando con un arreglo , deberemos utilizar las llaves **"{ }"**
  + En el caso de un comando, deberemos utilizar específicamente los paréntesis **"( )"** de la siguiente manera

         $(ls .)

#### El método Backtick

+ Este método tiene los mismos alcances que el **Dollar-Backet**, pero a mi parecer, es mucho mas engorroso para anidarlo y leerlo, por lo que no se explicara en esta guiá, si se quiere tener mas información revisar (<https://www.nextstep4it.com/backtick-symbol-shell-script/>) y (<https://stackoverflow.com/questions/2657012/how-to-properly-nest-bash-backticks>)

---

## Test

Un test en bash es una forma de escribir una expresión que puede ser verdadera o false

+ Para construir una expresión de este tipo se utilizan los caracteres de corchetes **"[ ]"** (este caracer es un comando de tipo builtin, es por esto que **debo poner espacios entre el caracter y la sentencia a evaluar**) para encerrar aquello que quieras testear.

+ Si la condición es **verdadera** dará como resultado un **0**, en caso contrario dara un **1**

        [ 1 = 0]

### Logical Operators

Tenemos a disposición tres operadores lógicos, **" || "** que representa una **OR** ,  **" && "** que representa una **AND**, y **" ! "** que representa **NOT**. Ademas, los paréntesis **"( )"** sirven para indicar orden de prioridad

        ([ 1 = 1] || [ 2 = 2]) && [ 3 = 3]

+ Puedo utilizar **-o** como una **OR** y **-a** como una **AND**, pero hay que tener en cuenta una cosas, y es que **no puedo utilizar parentesis para indicar prioridad**

        [ 1 = 1 -o '2' = '2' ]

### El operador " [[ "

Puede ser difícil encontrar la diferencia entre el corchete simple **" [ "** y el corchete doble **" [[ "**, ya que al utilizarlos para test, **tienen salidas muy similares**. La diferencia radica en que los corchetes dobles permiten que al momento de resolver una sentencia, **pueda que alguna de las variables NO EXISTA**.

Un ejemplo de esto podría ser:

        $ unset VAR
        $ [ $VAR = '' ]
        mensaje de error
        $ [[ $VAR = '' ]]
        $ echo $?
        0

+ aquí podemos ver como los **corchetes simples** al resolver la sentencia, le queda **[ = '']** por lo que devuelve un mensaje de error, mientras que con los **corchetes dobles**, al resolver la sentencia queda **[ '' = '' ]**, por lo que la evaluacion da **verdadero**

Una cuestión a tener en cuenta es que con los corchetes dobles, no podemos usar **-o** para **OR** ni **-a** apara **and**

En conclusión, los **corchetes dobles** pueden manejar algunos **casos extremos** que los **corchetes simples** no. Por consiguiente, podemos decir que **es una buena opción utilizarlos**.

### Operadores UNITARIOS y BINARIOS

Estos son otros operadores relacionados con los test, **los unitarios** son llamados asi dado que **aceptan solo 1 argumento**

#### Operador unitario -z

Este operador evalúa **si una variable esta vacía o no**, true en caso de que lo este, 1 en caso contrario.

        $ unset DOSNTEXIST
        $ [ -z "$DOESNTEXIST"]
        $ echo $?
        0

#### Operador unitario -a

Este operador evalúa **si un archivo existe o no**, true en caso de que exista, false en caso contrario

        $ touch true_file
        $ [-a true_file]
        $ echo $?
        0

#### Operador unitario -d

Este operador evalúa **si un directorio existe o no**, true en caso de que exista, false en caso contrario

        $ mkdir true_directory
        $ [ -d true_directory]
        $ echo $?
        0

#### Otros operadores unitarios

Existen un gran numero de operadores unitarios, aunque los mas utilizados son los 3 mencionados anteriormente. En caso de querer conocer todos, se puede acceder a (<https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html>) para mas información.

#### Operadores binarios -ls, -gt, -eq, -ne

Estos operadores son denominados binarios porque aceptan dos parámetros a evaluar

+ **-lt** representa **less than**

        $ [2 -lt 20]
        $ echo $?
        0

+ **-gt** representa **greater than**

        $ [10 -gt 5]
        $ echo $?
        0

+ **-eq** representa **equals**

        $ [5 -eq 5]
        $ echo $?
        0

+ **-ne** representa **not equals**

        $ [8 -ne 9]
        $ echo $?
        0

Se recomienda utilizar estos comandos en ves de **<, >, =, !=** debido a que estos **para funcionar deben ser escritos en corchetes dobles**.

        [[ 1 < 2 ]]

### Sentencia "if"

La sentencia if tiene la siguiente estructura:

        if [[ val1 op val2 ]]
        then
           command
        elif [[ val3 op val4 ]]
           command
        else
           comand
        fi         

---

## Loops

### Bucle "for"

La sentencia for tiene la siguiente estructura:

        for((int i=0 ; i<val ; i++))
        do
        command1
        command2
        ...
        done

+ algo para tener en cuenta es que cuando hay doble paréntesis **"(( ))"**

Otra estructura podría ser de utilidad, es similar a la de los bucles for en python:

        touch file1 file2 file3
        for f in $(ls)
        do
        echo "File $f contains: $(cat $f)"
        done

+ Es decir que definimos una variable que recorre, de alguna manera, **una lista de entrada**.

### Bucle "while"

La secuencia while tiene la siguiente estructura:

        while [[ cond ]]
        do
        command
        command
        done

+ Tener en cuenta que se puede utilizar la sentencia **"break"** para salir del bucle.

### Bloque tipo "case"

        case vulue in
        1) command1 ; command2 ; ... ;;
        2) command3 ; command4 ; ... ;;
        *) command5 ; command6 ; ... ;;
        esac

+ Tener en cuenta que los **comandos** los separo con punto y coma simples **" ; "** y los **distintos cases** con doble punto y coma **" ;; "**

+ El valor de los **cases puede ser también una cadena**, no necesariamente un numero, pero **si** esta cadena **posee espacios deberé poner el index entre comillas**

        value="el valor"
        case value in
        "el valor") command ;;
        esac

### Bloque tipo "case" con linea de opción

Este en realidad no es un bloque case, sino que un **script que hace las cuentas de un case**, para explicarlo se dará un ejemplo y ampliaremos cada una de sus partes

        cat > bash.sh << 'EOF'
        #!/bin/bash
        while getopts "ab:c" opt
        do
        case "$opt" in
        a) echo '-a fue invocado' ;;
        b) echo "-b fue invocado con el argumento: ${OPTARG} ;;
        c) echo '-c invocado' ;;
        esac
        done
        EOF

+ Primero que todo esta el uso de **heredoc**(EOF), para ver mas sobre esto ir a la sección de **TIPS finales**

+ Lo segundo es el uso del comando builtin **getopts**, este comando devuelve **true cuando aun hay mas flags para procesar y setea una en la variable opt**. Tener en cuenta que el uso de **" : "** indica que el flag inmediatamente anterior **recibe un argumento** y lo **setea en la variable OPTARG**

En conclusión, podemos llamar a este script con distintos parámetros, y se ejecutaran distintos cases:

        $ ./script.sh -a
        -a fue invocado
        $ ./script.sh -b "argumento"
        -b fue invocado con el argumento: argumento
        $ ./script.sh -c
        -c invocado

---

## Exit code

Luego de ejecutar un comando, una función o un builtin, se **setea** una **variable especial ( ? )** que nos indicara cual fue el **resultado de** dicha **ejecución**.

### Códigos de salida estándar

A continuación se explicaran algunos códigos de salida estándar, pero no todos los programas singuen este estándar

+ **0:** *comando ejecutado correctamente*
+ **1:** *se usa cuando hubo un error pero no hay un numero especifico para especificar el "porque", (error general)*
+ **2:** *mal uso de un comando builtin de shell*
+ **126:** *problemas de permisos o el comando no es ejecutable*
+ **127:** *no se encontró el archivo correspondiente al comando*
+ **128:** *argumento de salida invalido*
+ **128+n:** *proceso killed con la señal "n" (ej: 130)*

Los códigos del **3 al 12** generalmente **no están reservados**, por lo que podemos usarlos en nuestros scripts, para indicar lo que queramos.

### Código de salida y sentencia "if"

Como mencionamos anteriormente **no todos los programas siguen el estándar de exit codes**, es por eso que siempre debemos **hacer algunos test** o **leer la documentación** de los mismos, para determinar como son sus exit codes. En el caso de **grep**, este largara un 0 en caso de que haga algún match, y 1 en caso contrario.

Un uso que podemos darle a los **exit codes** es con la sentencia **if**

        $ echo texto > file.txt
        $ if grep texto file.txt
        > then 
        > echo "encontrado"
        >fi
        encontrado

### Configurar tus propios Códigos de Salida

Esto se hace mediante el comando builtin **"exit n"** donde **n** sera el **numero con el que queremos salir de la ejecución**.

        $ bash
        $ exit 65
        $ echo $?
        65

En caso de estar dentro de una función, podemos setear el exit code mediante el builtin **"return"**.

---

## Builtin "set"

El comando builtin **"set"** nos permite **ver, setear y dessetear** las opciones de bash.

### Opciones útiles para Scripting

A continuación describiremos algunas opciones de utilidad, en caso de querer conocer todas las opciones disponibles, se pueden leer en el **man set**.

+ **errexit**: esta opción le indica a bash que salga del script si alguno de sus comandos falla

+ **xtrace** : esta opción te muestra por pantalla cada comando que se ejecuta, para asi hacer un seguimiento del progreso del script

+ **nounset**: le indica a bash que arroje un error si una variable esta siendo referenciada pero no existe

+ **pipefail**: le indica a bash que al utilizar pipes que la linea completa retorne el ultimo valor distinto de 0, o 0 en caso de que todos los comandos corran correctamente

Cualquiera de estas opciones se establece de la siguiente manera

        set -o option

y se desactiva con

        set +o option

---

## Sustitucion de procesos

### El operador " <( ) "

Este operador es conceptualmente similar a **" $( ) "**. La diferencia es que este operador lo que hace es sustituir a un **fle** que contenga **la salida del proceso contenido**. Es decir que en ves de guardar la salida de un comando en un archivo, y luego evaluar dicho archivo, podremos evaluar directamente la comando dentro del operador " <( ) "

Para dejarlo mas claro podemos ver el siguiente ejemplo:

+ en ves de realizar esto:

        $ mkdir dir1 dir2
        $ touch dir1/a dir1/b dir1/c
        $ touch dir2/a dir2/b dir1/d
        $ echo "$(ls dir1)" > aout
        $ echo "$(ls dir2)" > bout
        $ diff aout bout
        1c1
        < a
        ---
        > 1
        3c3
        < c
        ---
        > d
        $ rm aout bout

+ podemos hacer directamente esto

        $ mkdir dir1 dir2
        $ touch dir1/a dir1/b dir1/c
        $ touch dir2/a dir2/b dir2/d
        $ diff >(ls dir1) >(ls dir2)
        1c1
        < a
        ---
        > 1
        3c3
        < c
        ---
        > d

+ Operando de esa manera podemos ahorrarnos trabajo.

---

## Subshells

Una **subshell** se define mediante la **apertura** de paréntesis **" ( "**, en ese momento la ejecución de la **shell principal queda en pausa** hasta que se **cierran** los paréntesis **" ) "**.

        (
        command
        command
        )

### subshell y scope

En una subshell podremos tener acceso a las variables de la shell padre, pero no podremos modificar su contenido

        $ VAR1="shell principal"
        $ (
        > echo $VAR1
        > VAR1="subshell"
        > echo $VAR1
        > )
        shell principal
        subshell
        $ echo $VAR1
        shell principal

+ Aquí queda en evidencia como dentro de una **subshell** podemos **enmascarar** el valor de una **variable de la shell padre**

  + Cabe destacar que ni aunque le pusiéramos el builtin **"export"** podríamos pisar el valor que tenia la variable en la shell padre.

### Subshells and Redirection

Una de las **propiedades útiles** que tiene la **subshell** es que el código puede tratarse como **una unidad**, por lo que podrías **redireccionar el output** de este **conjunto de comandos**

        $ (
        > echo "primer comando" > com1
        > echo "segundo comando" > com2
        > mkdir coms
        >mv com1 coms
        >mv com2 coms
        > ls coms
        > cat coms/com1
        > cat coms/com2
        > ) >> logfile
        $ cat logfile
        com1
        com2
        primer comando
        segundo comando

### "( )" vs "{ }"

Una de las confusiones frecuentes en bash, se puede dar por este par de caracteres

Si miramos el siguiente ejemplo:

        $ (echo 1 ; echo 2 )
        1
        2
        $ {echo 1 ; echo 2 ;}
        1
        2

notamos que la salida es **la misma**, la única diferencia es que en el caso de **las llaves**, debemos agregar **el punto y coma final**. Esto se debe a que los **paréntesis** generan una **subshell** la cual es terminada con **" ) "**, en cambio en el caso de **las llaves**, es un **agrupador de comandos**, por lo que deberemos indicar cuando termina el comando, **no se genera ninguna subshell**.

### Subshell y directorios de trabajo

Otra de las características de la **subshell** es que si dentro de esta, cambiamos de directorio y luego salimos, el directorio de trabajo de la shell padre no se vera afectado

        $ mkdir directorio
        $ cd directorio
        $ (
        > pwd
        > mkdir otro_directorio
        > cd otro_directorio
        > pwd
        > )
        /home/user/directorio
        /home/user/directorio/otro_diretorio
        $ pwd
        /home/user/directorio

---

## El "internal Field Separator"

### archivos con espacios

un **posible problema** a experimentar es el de la **creación de archivos** haciendo uso de **espacios**:

        $ echo "algo de texto" > "archivo con espacio"
        $ ls
        archivo\ con\ espacio

Hasta ahi todo bien, en tanto y cuanto, siempre que invoquemos a este archivo lo hagamos entre **comillas dobles o simples**

        $ cat "archivo con espacio"
        algo de texto

**El problema es cuando**, por ejemplo, utilicemos el bucle for que recorre listas

        $for f in $(ls)
        > do
        > cat $f
        >done
        cat: archivo : not found
        cat: con : not found
        cat: espacio : not found

Aquí el problema es que el bucle for, **toma a cada palabra** que forma el nombre del archivo, como un **token distinto**.

Para poder controlar eso y trabajar con archivos que tengan espacios en sus nombres tenemos a disposición la variable **IFS**

### La variable IFS

Bash toma los **caracteres** especificados en **estra variable** para determinar cuales serán **los separadores**.

        $ set | grep IFS
        IFS=$' \t\n'

Por lo tanto, para **resolver** el tema de los **espacios**, podríamos **quitarlo** de la **variable**, y que de ese modo el **espacio** sea tomado como **un caracter mas**
s
        IFS=$'\t\n'

---

## TIPS finales

Una **herramienta util** al momento de escribir un script puede ser el uso de **heredoc**, este **sirve para suministrarle a un comando un bloque de texto o comandos**.

        command <<[-] 'delimitador'
        here-document
        delimitador

+ Nótese que el uso del **" - "** es opcional, y sirve para usar tabulacion dentro del **heredoc** y q estos se ignoren, ya que en el heredoc no se permiten espacios ni tabulaciones.

## READ

Una herramienta de gran utilidad podría ser **"read"**, la cual podemos utilizarla de la siguiente manera

        $ read miargument
        algo
        $ echo $miargumento
        algo

## ENVÍO DE PASSWORD

Podemos asignar una password o valor requerido luego de que se ah corrido algún comando

        #!/bin/bash
        pass="mipassword"
        sudo fdisk -l
        expect la_password
        send $pass