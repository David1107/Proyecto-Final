# Proyecto Sistemas Distribuidos

En el siguiente proyecto, se presenta el despliegue automático de infraestructura para dar soporte a procesos de integración continua. Para esto, se plantea como ejemplo la arquitectura que se puede observar en la Imagen 1. Para la implementación, se plantea un nodo maestro de Jenkins que se encargue del despliegue de Jobs (tareas ejecutables que son controladas o monitoreadas por Jenkins.) que para este caso, se va a encargar del despliegue del nodo “Slave”. Al hacer uso de una imagen evarga para su aprovisionamiento, esto se debe a que dicha imagen tiene los requerimientos básicos para ser un nodo “Slave” de Jenkins. A continuación, se describe el despliegue y la configuración del nodo maestro con Jenkins. Además, se ejecuta una prueba de cobertura con el plugin “pytest-cov” (Python, 2016) en el nodo “Slave” directamente y en el nodo maestro de Jenkins. Por último, se presentan los problemas encontrados con sus respectivas soluciones y las conclusiones del proyecto. 
 
Inicialmente, para la implementación del nodo maestro, se crea el Dockerfile que contiene la imagen de Jenkins con los plugins necesarios para el uso de Jenkins, además del comando “JAVA_OPTS="-Djenkins.install.runSetupWizard=false"”, que se encarga de evitar la configuración inicial del wizard de Jenkins. El contenido del archivo se puede observar en la Imagen 2. Después, en Jenkins se procede a realizar la configuración manual para la creación de la maquina evarga. Dicha implementación se puede observar detalladamente en las Imágenes xx,xx. Por otro lado, en las Imágenes xx, se presenta el procedimiento para la creación de un job en Jenkins que se encargue del levantamiento del contenedor con la imagen evarga.


Seguidamente, para las pruebas de cobertura anteriormente mencionadas, se hace uso del repositorio de git hub de Daniel Barragan (2016), Inicialmente, realizamos la prueba en el montaje del contenedor con la imagen de evarga. En la Imagen xx, se puede observar el resultado de la prueba en la consola del contenedor al momento de construirse. Por otro lado, en las Imágenes xx,xx, se puede observar el procedimiento de la ejecución de la misma prueba desde Jenkins con sus respectivos resultados. Es importante mencionar que, el contenedor virtual puede realizar cualquier tipo de prueba sobre la infraestructura desplegada. En este caso, pretendemos obtener una comparación de la ejecución de las pruebas en los dos entornos.


Para concluir, Jenkins es una herramienta de integración útil y fácil de instalar, dado que permite integración distribuida por medio de los nodos, maestro y esclavo. Por otro lado, es importante mencionar que, Jenkins cuenta con una gran diversidad de plugins que permiten diferentes funcionalidades en la herramienta, lo que aumenta la productividad de la misma. En este caso, solo hacemos uso de los plugins: git, docker plugin y restart plugin. Por último, se destaca la facilidad que la herramienta brinda para realizar pruebas sobre la infraestructura por medio de su interfaz gráfica. 


## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install the software and how to install them

```
Give examples
```

### Installing

A step by step series of examples that tell you have to get a development env running

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone who's code was used
* Inspiration
* etc
