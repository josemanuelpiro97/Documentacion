# Docker

+ Los contaners son utiles para la geston de dependencias, y para abstraer un entorno de trabajo, de otro etonrno de trabajo. De este modo, en un mismo servidor podriamos estar corriendo dos aplicaciones web que requieran de la misma dependencia pero de dos versiones distintas, sin que halla ningun tipo de conflicto.

+ Otra aplicacion imoprtante es cuando no nos alcanza un solo servidor para gestionar una aplicacion, la soluion concida para esto es integrar un reverse proxy y clonar el servidor las veces que sea necesario. El problema con esto es que cada ves que requiera una atualizacion, debere actualzar todos los servidores. Esto es algo que resuelve docker de manera bastante eficiente. Dado que docker esta basado en imagenes, podemos levantar todos los contenedores que querramos con una sola imagen, que contega todas las dependencias necesarias

## Conceptos basicos

### Containers

Este es el que estara corriendo dentro de **docker**, se lo puede pensar com una maquina aislada o como una VM.

**Dentro** del conteiner **esta todo lo necesario** para que nuestras aplcaciones corran correctamente: **SO, dependencas, files, etc...** Es decir que el contenedor esta **totalmente aislado del host**, salvo explicito permiso de relacion.

Cada contenedor tendra su **propio ID**.

### Images

La imagen **describe** todo lo que es **necesario** crear en el **container**. Es una especie de tamplate para el contenedor. **De una sola imagen podemos crear tanto contenedore como querdockerramos**.

Al ejecutar el comando run en docker, este buscara la imagen de manera local en primer instancia, luego si es que no la encuentra la buscara en el registro (docker hub)

+ Una cuestios muy importante a tener en cuenta es que **las imagenes tienen una etiqueta (tasg)**, la cul indica la version de la misma. Por **defecto** esta imagen es **latest**, pero esto podria tener problemas al momento de utilizar comandos como el **RUN**, dado que de manera local podriamos tener alguna version de la imagen y por ende, se tomara a esa imagen como latest, siendo que en verdad en el repositorio hay una version mas actualzada de la misma. Por lo tanto es recomendable **SIEMPRE USAR TAGS EN TUS IMAGENES**

### Registries

Es el lugar donde se almacenan las imagenes.

---

## Conexion de red entrante

Por **default** los contenedores se ejecutan en **aislamiento**, pero esta permitido **mapear** un puerto dentro del **host** a un puerto del **contenedor**

Para esto tenemos a disposicion el parametro **-p**, el cual recibe **2 prametros**. uno es el **puerto entrante que se abrira en el host**, y el otro es el **puerto del contenedor al cual me voy a conectar**

        docker run -p HOSTPORT:CONTPORT IMAGE

---

## Uso de Volumenes

Dado que los contenedores se encontraran aislados tanto del host como de los otros contenedores, la unica forma de guardar o leer informacion fuera del contenedor es uasndo los volumenes, esto se hace a travez del parametro **-v**

    docker run -v tu/directorio:directorio/del/contenedor IMAGE

---

## Creacion de imagenes propias

### Formato

    # este tag le indicara a docker de donde tomar la imagen base, pues todas las imagenes estan basadas en otra imagen
    FROM nombre_de_imagen:version

    # este tag indica el comando que sera ejecutado una ves se construya el contenedor
    # solo puede haber 1 CMD y se tomara por defecto el ultimo escrito. 
    # puede haber mas de un comando por CMD haciendo uso de los caracteres && entre comando y comndo
    CMD comando1 && comando2 && ...

    # este tag copiara uno o varios archivos desde un directorio local a un directorio dentro del repositorio   
    COPY /ruta/al/file /ruta/en/el/contaier

    # este tag indica que la variable con su valor sera pasada con variable de entorno al contenedor. 
    # ademas, las variables podran ser accedidas desde las aplicaciones que coran en dicho contenedor
    # podemos tener tantas ENV como querramos
    ENV NOMBRE_DE_VARIABLE VALOR

    # este tag nos permite crear directorios dentro del contenedor, luego desde el comando run podemos mapear estos directorios en algun directorio de nuestra maquina host
    VOLUME /ruta/al/directorio

    # este tag solo sirve para documentar, indicando al lector de la imagen que el container estara abierto al exterior mediante un puerto en particular.  
    EXPOSE port

    # este tag, establece que comandos se ejecutaran al momento del buildeo, estos comandos solo se ejecutaran una ves
    RUN comndo1 && comando2 && ...

+ Para mas informacion sobre estos comandos, se puede consultar la siguiente pagina de docker <https://docs.docker.com/engine/reference/builder/#healthcheck>

---

## Comandos de utilidad

+ Listar los containers que estan corriendo

        docker ps [OPTION]

+ reveer el logs de un container, aun cuando ya ah sido detenido

        docker logs [OPTION] CONTAINER

  + con **--since** puedo indicar un tiempo

            docker logs --since 10s CONTAINER

+ obtener informacion sobre un contenedor que este corriendo o detenido

        docker inspect [OPTION] NAME|ID

+ detener contenedres que aun esten correindo

        docker stop [OPTIONS] CONTAINER

+ eliminar contenedres

        dcker rm [OPTION] CONTAINER

    En caso de querer eliminar todos los contenedores de una ves, podemos usar el comando

        docker container prune -f

### Comando RUN

Este comando pone en marcha un container basandose en alguna imagen, que en primer instancia busca localmente y de no encontrarla la busca en el registro (dokerhub)

#### basico

        docker run IMAGEN

#### especificando conexion entre puertos

        docker run -p MIPORT:PORTDELCONTAINER IMAGEN

#### uso de volumenes

        # tener mucho cuidado con tu raiz
        docker run -v tu/ruta:ruta/en/el/contenedor IMAGEN

#### pasaje de variables

        # en caso de que halla definida una variable con le mimsmo nombre en el docker file, esta sera pisada por la suministrada en el run
        docker run -e VARNAME=VARVAL IMAGEN

#### otras utilidades

+ para que se borre el contenedor una vez detenido

        --rm

+ para que se pueda utilizar ^c en la consola y detener el contenedor

        -it
