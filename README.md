# Desarrollo-y-Despliegue-en-la-Nube-con-Google-Cloud-Platform

**SRE** es una aplicación practica de los principios de DevOps. SRE complementa DevOps con guías tangibles a lo largo de la cultura de DevOps.

**DevOps** es una cultura y un conjunto de prácticas diseñadas para quitar las diferencias entre los equipos de Desarrollo y Operación.

## Mejores prácticas de SRE

1. **Shared Ownership / Titularidad compartida**: Todos somos responsables de la velocidad entrega y por la estabilidad del software.
2. **Blameless postmortem** _Post Mortem sin culpa_ : Aceptar el fracaso como algo normal, no buscamos a quien culpar sino como podemos mejorar el proceso o código para que no vuelva a ocurrir.
3. **Reducir el costo del error**: Implementar cambios graduales, pequeños y frecuentes; para que el impacto sea acotado. Entregar software de forma continua no solo agrega valor a los clientes sino que también reduce el impacto que un error pueda tener.
4. **Automatizar los casos comunes**: Aprovechar las herramientas y la automatización para evitar errores humanos.
5. **Medir todo**: Medir nos ayuda a mejorar, nos ayuda a identificar problemas de forma proactiva. Medir el esfuerzo y reliability.

Libros: https://sre.google/books/

## Service Level Terminology

**SLI**
_Service Level Indicator_
Un SLI es un indicador de nivel de servicio, una medida cuantitativa cuidadosamente definida de algún aspecto del nivel de servicio que se proporciona.

- La mayoría de los servicios consideran la latencia de la solicitud(request latency) (cuánto tiempo se tarda en devolver una respuesta a una solicitud) como un SLI clave.
- Otros SLI comunes incluyen la tasa de error(error rate), a menudo expresada como una fracción de todas las solicitudes recibidas.
- Por ultimo el rendimiento del sistema(system throughput), tambien es un SLI común, generalmente medido en solicitudes por segundo.
- Otro tipo de SLI importante para las SRE es la disponibilidad(availability), o la fracción del tiempo que se puede utilizar un servicio.

**SLO**
_Service Level Objective_
Un Objetivo de nivel de servicio es un valor objetivo o rango de valores para un nivel de servicio medido por un SLI. El objectivo al que apuntamos es al SLO, mientra

- La elección y publicación de SLO para los usuarios establece expectativas sobre el rendimiento de un servicio. Esta estrategia puede reducir las quejas infundadas a los propietarios del servicio sobre, por ejemplo, la lentitud del servicio.
- Sin un SLO explícito, los usuarios a menudo desarrollan sus propias creencias sobre el desempeño deseado, que pueden no estar relacionadas con las creencias de las personas que diseñan y operan el servicio.

**SLA**
_Service level agreements_
Un Acuerdo a nivel de servicio es un contrato explícito o implícito con sus usuarios que incluye las consecuencias de cumplir (o no cumplir) los SLO que contienen.

Las consecuencias se reconocen más fácilmente cuando son financieras (un reembolso o una multa), pero pueden adoptar otras formas.

Una manera fácil de distinguir entre un SLO y un SLA es preguntarse “¿qué sucede si no se cumplen los SLO?”: Si no hay una consecuencia explícita, es casi seguro que esté mirando un SLO.

Por lo general, los y las SRE no se involucra en la construcción de SLA, porque los SLA están estrechamente vinculados a las decisiones comerciales y de productos.

Sin embargo, SRE se involucra para ayudar a evitar desencadenar las consecuencias de los SLO omitidos. También pueden ayudar a definir los SLI: obviamente, debe haber una forma objetiva de medir los SLO en el acuerdo, o surgirán desacuerdos.

## Error Budget

El Porcentaje de error aceptable es la diferencia entre el 100% y nuestro objetivo (SLO) que tenemos como oportunidad para hacer cambios/mejoras/mantenimiento.

El Porcentaje de error aceptable nos permite:

- Aumentar la velocidad de desarrollo.
- Incrementar mejoras.
- Inovar en nuestros productos.

**Beneficios del porcentaje de error aceptable**

- Incentivos comunes para desarrolladores y SREs: Encuentra el balance adecuado entre innovación y reliability.

- Los equipos de desarrollo pueden gestionar el riesgo por su cuenta: Ellos deciden cómo utilizar el porcentaje de error aceptable.

- Los objetivos de reliability poco realistas no son atractivos: Estos objetivos disminuyen la velocidad de la innovación.

- Responsabilidad compartida por uptime del sistema: Los fallos de la infraestructura utilizan el porcentaje de error aceptable de los desarrolladores.

**Consecuencias del porcentaje de error aceptable**
✅ Cuando hay porcentaje de error aceptable: Priorizar la velocidad

- Lanzamiento de nuevas funciones.
- Cambios previstos en el sistema.
- Fallos inevitables en el hardware, las redes, etc.
- Experimentos arriesgados.

- ❌ Cuando se agota el porcentaje de error aceptable: Priorizar la estabilidad
- Ralentizar o detener los lanzamientos de nuevas funciones.
- Priorizar items del postmortem.
- Automatizar los procesos de implementación.

## Construcción de Imágenes de Contenedor

Preparando los archivos fuente

```
quickstart.sh
```

Para hacer ejecutable el script `chmod +x quickstart.sh`

```
echo "Hola mundo!"
```

`Dockerfile`

```
FROM alpine
COPY quickstart.sh /
CMD ["/quickstart.sh"]
```

Crear un repo de Docker en Artifact Registry

```
gcloud artifacts repositories create quickstart-docker-repo --repository-format=docker \
--location=us-central1 --description="Docker repository"
```

Para verificar que el repo se creo:

```
gcloud artifacts repositories list
```

Construyendo la imagen usando Docker

El `project-id` se puede obtener con el comando `gcloud config ger-value project`

```
gcloud builds submit --tag us-central1-docker.pkg.dev/project-id/quickstart-docker-repo/quickstart-image:tag1
```

Construyendo usando un build config file ```cloudbuild.yaml```
El ```$PROJECT_ID``` se puede obtener con el comando ```gcloud config ger-value project```

```
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1', '.' ]
images:
- 'us-central1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1'
```

Para ejecutar el ```cloudbuild.yaml``` usamos:

```
gcloud builds submit --config cloudbuild.yaml
```

En Cloud Build bajo Build history podemos ver los archivos que se han compilado con el comando anterior.



