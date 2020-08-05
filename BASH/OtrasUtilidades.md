# Utilidades

## HereDocs

HereDocs, es una utilidad de bash que nos permite insertar un bloque de comandos o texto, como el input de otro comando

    $ cat > result.txt << 'END'
    > algun texto
    > algun texto
    > END
    $ cat result.txt
    algun texto
    algun testo

### Uso avanzado

+ Siempre es recomendado **usar comillas simples** en los hereDocs, ya que esto permite el correcto so de parámetros de entrada (debido a que bash no lo interpreto)

        $ function func
        > cat > $1 << 'END'
        > #!/bin/bash
        > echo "$1"
        > END
        > chmod +x $1
        > }
        $ func script1
        $ ./script1 "algun parametro"
        algun parametro

+ Otra característica a tener en cuenta es el uso de  guion medio **" - "** antes de la asignación del input. Esto nos **permite usar espacios o tabulaciones** en el cuerpo del hereDoc

### Here String

De manera similar se puede utilizar el **"hereString"**, esto es utilizado para asignar un string al regular input de un comando. Esto se hace mediante los caracteres **" <<< "**

    $ fuction func {
    > cat > $1 <<< $2    
    }
    $ func archivo "string con mensaje"
    $ cat archivo
    string con mensaje

---

## Bash History

El historial de bash es básicamente el historial de comandos que fueron ejecutados, ya sean correctos o erróneos. A este se puede acceder mediante:

    history

A continuación se listaran alguna de las utilidades que tiene este historia.

+ **!!** : ejecuta el comando anterior

        $ echo "algo
        algo
        $ !!
        algo

+ **!(** algun texto **)** : ejecuta el ultimo comando que comience con el texto explicitado

        $ echo "algo"
        algo
        $ ls -la
        $ !e
        algo

+ **!$** : ejecuta el comando usando como argumento el del comando anterior

        $ mkdir direc
        $ cd !$
        $ pwd
        direc

+ **!(** num **)** : ejecuta el comando que esta indexado con el numero especificado

---

## Output con tiempo

+ Lo único a tener en cuenta es la linea que nos proporciona esta información

        echo "$line $(date '+ %m-%d-%Y %H:%M:%S')"
        