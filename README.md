# Proyecto Sistemas Distribuidos

En el siguiente proyecto, se presenta el despliegue automático de infraestructura para dar soporte a procesos de integración continua. Para esto, se plantea como ejemplo la arquitectura que se puede observar en la Imagen 1. Para la implementación, se plantea un nodo maestro de Jenkins que se encargue del despliegue de Jobs (tareas ejecutables que son controladas o monitoreadas por Jenkins.) que para este caso, se va a encargar del despliegue del nodo “Slave”. Al hacer uso de una imagen evarga para su aprovisionamiento, esto se debe a que dicha imagen tiene los requerimientos básicos para ser un nodo “Slave” de Jenkins. A continuación, se describe el despliegue y la configuración del nodo maestro con Jenkins. Además, se ejecuta una prueba de cobertura con el plugin “pytest-cov” (Python, 2016) en el nodo “Slave” directamente y en el nodo maestro de Jenkins. Por último, se presentan los problemas encontrados con sus respectivas soluciones y las conclusiones del proyecto. 
 
 ![][1]
 Imagen 1
 
 
Inicialmente, para la implementación del nodo maestro, se crea el Dockerfile que contiene la imagen de Jenkins con los plugins necesarios para el uso de Jenkins, además del comando “JAVA_OPTS="-Djenkins.install.runSetupWizard=false"”, que se encarga de evitar la configuración inicial del wizard de Jenkins. El contenido del archivo se puede observar en la carpeta "jenkinsmaster/". Después, en Jenkins se procede a realizar la configuración manual para la creación de la maquina evarga. Dicha implementación se puede observar detalladamente en las Imágenes 2,3. Por otro lado, en las Imágenes 4,5,6,7,8 se presenta el procedimiento para la creación de un job en Jenkins que se encargue del levantamiento del contenedor con la imagen evarga.

### Imagen "Slave"

![][8]
Imagen 2


![][9]
Imagen 3


### Job

![][4]
Imagen 4


![][5]
Imagen 5


![][6]
Imagen 6


![][7]
Imagen 7


![][8]
Imagen 8



Seguidamente, para las pruebas de cobertura anteriormente mencionadas, se hace uso del repositorio de git hub de Daniel Barragan (2016), Inicialmente, realizamos la prueba en el montaje del contenedor con la imagen de evarga. En la Imagen 9 y 10, se pueden observar los resultados de la prueba, el primero, es sin las lineas de perdida y el segundo con lineas de perdida, al momento de construirse el contenedor. Por otro lado, en las Imágenes 11 se puede observar el procedimiento de la ejecución de la misma prueba desde Jenkins con sus respectivos resultados. Es importante mencionar que, el contenedor virtual puede realizar cualquier tipo de prueba sobre la infraestructura desplegada. En este caso, pretendemos obtener una comparación de la ejecución de las pruebas en los dos entornos obteniendo los mismos resultados.

![][2]
Imagen 9


![][3]
Imagen 10


![][11]
Imagen 11


Para concluir, Jenkins es una herramienta de integración útil y fácil de instalar, dado que permite integración distribuida por medio de los nodos, maestro y esclavo. Por otro lado, es importante mencionar que, Jenkins cuenta con una gran diversidad de plugins que permiten diferentes funcionalidades en la herramienta, lo que aumenta la productividad de la misma. En este caso, solo hacemos uso de los plugins: git, docker plugin y restart plugin. Por último, se destaca la facilidad que la herramienta brinda para realizar pruebas sobre la infraestructura por medio de su interfaz gráfica. 

## Preparativos de Docker

## Despliegue

En esta sección, se realizará una descripción detallada de los procesos a seguir para lograr el levantamiento del entorno de desarrollo especificado anteriormente.

### Jenkins Master

Existe un contenedor que hará las veces de maestro en la arquitectura. En este punto, se definió un archivo Dockerfile, en el cual se configuran las dependencias y plugins necesarios para la correcta implementación del nodo maestro. Para realizar su despliegue, se necesitan las siguientes configuraciones:

```
FROM jenkins
#Install plugins
RUN /usr/local/bin/install-plugins.sh docker:0.16.2
RUN /usr/local/bin/install-plugins.sh saferestart:0.3
RUN /usr/local/bin/install-plugins.sh git:3.0.1

#setup no run setup wizard
ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"
```

Con este archivo de configuración, se procede a construir la imagen de Docker. Para esto, se usa el siguiente comando:

```
docker build -t jenkins_YorQuiCos .
```

Luego, a partir de la imagen construida, se ejecuta un contenedor virtual que implemente el maestro Jenkins:

```
docker run -d -p 8080:8080 jenkins_YorQuiCos
```
![][4]
Imagen 12

### Configuración contenedor evarga

Se hizo uso de una imagen evarga que contiene las dependencias precisas para implementar un nodo "esclavo" de Jenkins. Esto con el fin de poder generar los contenedores esclavos que permitan la ejecución de las pruebas del entorno. A continuación, se presenta el archivo Dockerfile con el que se implementa este nodo:

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

### Creando un job

### Evidencias

### Problemas, situaciones y soluciones



## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds



## Autores

* **David Quiñónez** - *12207002* 
* **Yor Jaggy Castaño** - *11107005* 
* **Mauricio Vásquez** - *12207002* 




[1]: images/Arquitectura.png
[2]: images/TestConsola.png
[3]: images/TestConsolaLines.png
[4]: images/1.png
[5]: images/11.png
[6]: images/12.png
[7]: images/13.png
[8]: images/14.png
[9]: images/8.png
[10]: images/9.png
[11]: images/TestJenkins.jpg
