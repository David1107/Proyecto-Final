# Proyecto Sistemas Distribuidos

En el siguiente proyecto, se presenta el despliegue automático de una infraestructura para dar soporte a procesos de integración continua. Para esto, se plantea como ejemplo la arquitectura que se puede observar en la Imagen 1. Para la implementación, se plantea un nodo maestro de Jenkins que se encargue del despliegue de Jobs (tareas ejecutables que son controladas o monitoreadas por Jenkins.) que para este caso, se va a encargar del despliegue del nodo “Slave”. 

El nodo esclavo se aprovisionara mediante una imagen propuesta por Ervin Varga, dicha imagen es "evarga/jenkins-slave" esto se debe a que dicha imagen tiene los requerimientos básicos para ser un nodo “Slave” de Jenkins.

A continuación, se describe el despliegue y la configuración del nodo maestro con Jenkins. Ademas, se ejecuta una prueba de cobertura con el plugin “pytest-cov” (Python, 2016) en el nodo “Slave” y se visualiza el resultado de la prueba mediante jenkins. Por último, se presentan los problemas encontrados con sus respectivas soluciones y las conclusiones del proyecto. 
 
 ![][1]
 
Imagen 1: Infraestructura Jenkins maestro y esclavo


# Paso 1: Descripción y construcción de los contenedores a desplegar
##1.1. Descripción:
### Maestro de Jenkins:

Inicialmente, para la implementación del nodo maestro, se crea el Dockerfile que especifica la imagen de Jenkins que se usara y los plugins necesarios, además del comando “JAVA_OPTS="-Djenkins.install.runSetupWizard=false"”, que se encarga de evitar la configuración inicial del wizard de Jenkins.

Dockerfile:
```
FROM jenkins
#Install plugins
RUN /usr/local/bin/install-plugins.sh docker:0.16.2
RUN /usr/local/bin/install-plugins.sh saferestart:0.3
RUN /usr/local/bin/install-plugins.sh git:3.0.1

#setup no run setup wizard
ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"
```

### Esclavo de Jenkins

Se hizo uso de una imagen evarga que contiene las dependencias precisas para implementar un nodo "esclavo" de Jenkins. Esto con el fin de poder generar los contenedores esclavos que permitan la ejecución de las pruebas del entorno. A continuación, se presenta el archivo Dockerfile con el que se implementa este nodo:

Dockerfile:
```
FROM evarga/jenkins-slave
run apt-get update
run apt-get install -y wget
run apt-get install -y git
run apt-get install -y python
run wget https://bootstrap.pypa.io/get-pip.py
run python get-pip.py
USER root
workdir /home/jenkins/workspace/test
run chmod -R 777 /home/jenkins/workspace/test
copy requeriments.txt /home/jenkins/workspace/test
run pip install -r requeriments.txt
```

##1.2. Construcción:
### Maestro de Jenkins:
Luego de tener los archivos descargados se deben construir los contenedores virtuales. Para el maestro de jenkins, el primer paso es estar en la carpeta /jenkinsmaster, luego de esto se debe ejecutar el siguiente comando:

```
docker build -t jenkins_YorQuiCos .
```

###Esclavo de Jenkins:
Después, se debe construir la imagen que el contenedor maestro de jenkins usara para desplegar los contenedores. Para esto se debe estar en la carpeta jenkis_slave, luego de esto se debe ejecutar el siguiente comando:

```
docker build -t distrislave .
```

# Paso 2: Conexión con el maestro de Jenkins y configuración inicial:
##2.1. Conexión:
Luego, a partir de la imagen construida, se ejecuta un contenedor virtual que implemente el maestro de Jenkins:
```
docker run -d -p 8080:8080 jenkins_YorQuiCos
```
El comando anterior especifica que el contenedor se ejecutara en modo 'detached' y que se enlazaran el puerto 8080 del contenedor con el puerto 8080 de la maquina host.

Para acceder a la pagina inicial Jenkins se hace mediante la ip de la maquina que corre el contenedor virtual, especificando que la conexión se hará por el puerto 8080. Es decir, mediante la url http://<ipcontenedor>:8080.

![][4]
Imagen 2: Pagina inicial de jenkins

##2.2. Configuración inicial:
Despues de tener acceso a Jenkins, se debe configurar la nube y la plantilla de docker a usar en el despliegue de los esclavos. Se debe ingresar a:
Administrar Jenkins -> Configurar el Sistema

![][5]
Imagen 3: Pagina de configuración

Despues, se debe buscar al final de la pagina la opción "Añadir una nueva nube" y se selecciona la opcion "Docker".

![][6]
Imagen 4: Opcion Añadir una nueva nube de docker.

En los campos de la nube de docker, se debe especificar el nombre de la nube, la direccion url de docker*, credenciales de acceso los tiempos maximos para la conexion y lectura, la cantidad de contenedores en la nube y una plantilla de docker.

![][7]
Imagen 5: Configuracion de la nube de docker

![][8]
Imagen 6: Configuracion de las credenciales de Jenkins para los contenedores de la nube

Despues se debe añadir una plantilla de docker, mediante la cual se podra especificar la imagen a usar para los contenedores a aprovisionar, una etiqueta de identificación, el modo de uso, las credenciales de acceso para los nuevos contenedores, entre otras cosas.

![][11]
Imagen 7: Configuración de la plantilla de docker

# Paso 3: Creacion de un nuevo job/item en jenkins:
Despues de haber configurado la nube de docker, se debe regresar a la pagina inicial y crear un nuevo item o job.

![][4]
Imagen 8: Pagina inicial de jenkins

Cuando se crea un nuevo item, Jenkins solicita un nombre y un tipo de proyecto.

![][14]
Imagen 9: Creacion de un nuevo item

Posteriormente, se deben especificar parametros como el nombre del proyecto, descripcion, expresion (label creado anteriormente), fuente del codigo (Git) y en caso de requerirlo hay una sección ejecutar, la cual permite detaller el estado de las tareas.
![][27]
Imagen 10: Especificacion del nuevo item

![][15]
Imagen 11: Especificacion del repositorio de github

![][16]
Imagen 12: Comando a ejecutar cuando el codigo haya sido clonado

Despues de crear el job/item, se guarda las configuraciones y Jenkins regresa a la ventana desde la cual podemos compilar el proyecto. Para compilar el proyecto, se debe dar clic en Construir Ahora/Build now

![][17]
Imagen 13: Pagina del item/job

Cuando la tarea es compilada, Jenkins hace uso del docker-plugin para aprovisionar y conectarse a los contenedores. Esto ultimo se puede visualizar en las siguientes imagenes, en la seccion "historia de tareas".

![][19]
Imagen 14: comando "docker ps" comprobando los contenedores aprovisionados

![][20]
Imagen 15: Pagina del item/job: el cual se esta conectando al contenedor virtual

![][21]
Imagen 16: Pagina del item/job: Console ouput

![][23]
Imagen 13: Pagina del item/job: Console ouput

![][25]
Imagen 14: Console Output: Prueba exitosa!!!


Seguidamente, para las pruebas de cobertura anteriormente mencionadas, se hace uso el repositorio de github de Daniel Barragan (2016), Inicialmente, realizamos la prueba en el montaje del contenedor con la imagen de evarga. En la Imagen 15 y 16, se pueden observar los resultados de la prueba, el primero, es sin las lineas de perdida y el segundo con lineas de perdida, al momento de construirse el contenedor. Por otro lado, en las Imágenes 17 se puede observar el procedimiento de la ejecución de la misma prueba desde Jenkins con sus respectivos resultados. Es importante mencionar que, el contenedor virtual puede realizar cualquier tipo de prueba sobre la infraestructura desplegada. En este caso, pretendemos obtener una comparación de la ejecución de las pruebas en los dos entornos obteniendo los mismos resultados.

![][2]
Imagen 15


![][3]
Imagen 16


![][14]
Imagen 17


Para concluir, Jenkins es una herramienta de integración útil y fácil de instalar, dado que permite integración distribuida por medio de los nodos, maestro y esclavo. Por otro lado, es importante mencionar que Jenkins cuenta con una gran diversidad de plugins que permiten diferentes funcionalidades en la herramienta, lo que aumenta la productividad de la misma. En este caso, solo hacemos uso de los plugins: git, docker plugin y restart plugin. Por último, se destaca la facilidad que la herramienta brinda para realizar pruebas sobre la infraestructura por medio de su interfaz gráfica. 

### Evidencias

En el siguiente video de youtube se pueden visualizar las pruebas descritas en el presente README.

* [JenkinsCI: Master and Slave using Docker Containers](https://youtu.be/OxrBCt1JLuQ)

### Referencias:
* http://sourabhbajaj.com/mac-setup/Git/README.html
* https://www.ivankrizsan.se/2016/05/18/enabling-docker-remote-api-on-ubuntu-16-04/
* http://scriptcrunch.com/enable-docker-remote-api/
* https://wiki.jenkins-ci.org/display/JENKINS/Docker+Plugin
* https://www.codeproject.com/articles/1056410/setup-configure-jenkins-for-your-team-in-automated
* https://engineering.riotgames.com/news/jenkins-ephemeral-docker-tutorial
* https://github.com/d4n13lbc/testproject
* https://pypi.python.org/pypi/pytest-cov

## Autores

* **David Quiñónez** - *12207002* 
* **Yor Jaggy Castaño** - *12107010* 
* **Mauricio Vásquez** - *11107005* 


[1]: images/Arquitectura.png
[2]: images/TestConsola.png
[3]: images/TestConsolaLines.png
[4]: images/1.jpg
[5]: images/2.jpg
[6]: images/3.jpg
[7]: images/4.jpg
[8]: images/5.jpg
[9]: images/6.jpg
[10]: images/7.jpg
[11]: images/8.jpg
[12]: images/9.jpg
[13]: images/10.jpg
[14]: images/11.jpg
[15]: images/12.jpg
[16]: images/13.jpg
[17]: images/14.jpg
[18]: images/15.jpg
[19]: images/16.jpg
[20]: images/17.jpg
[21]: images/18.jpg
[22]: images/19.jpg
[23]: images/20.jpg
[24]: images/21.jpg
[25]: images/22.jpg
[26]: images/23.jpg
[27]: images/11.5.jpg
[14]: images/TestJenkins.jpg
