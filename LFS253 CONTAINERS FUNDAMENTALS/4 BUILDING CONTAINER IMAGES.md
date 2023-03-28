# **Chapter Overview**

One of the key requirements of a good application is the source code. A running application is as good as the source code behind it, paired with the reliability and the resilience of the running environment. In the case of application containers, a good running container relies on a properly specified and built container image, because the traditional application source code translates into container images.

There are three distinct methods of creating container images - from scratch, from an existing running container, and through image conversion. Let’s explore each method together with some of the tools supporting each method.

# **Learning Objectives**

By the end of this chapter, you should be able to:

- Describe methods to build container images.
- Describe the image build process with Dockerfile and Containerfile.
- Discuss the CMD and ENTRYPOINT instructions of Dockerfile.
- Discuss Docker, Podman, and Buildah.

# **Create a Container Image from Scratch**

Creating a container image from scratch implies that all components of the image are specified manually or through scripts. The approaches vary when it comes to building images from scratch. Some tools require certain steps to be performed by the user prior to specific instruction can be issued by the runtime while other tools support all aspects of the container image build. There are several tools that manage various aspects of the image build process from scratch, such as [runc](https://github.com/opencontainers/runc), [Docker](https://www.docker.com/), [Buildah](https://buildah.io/h), [Podman](https://podman.io/), [Distrobuilder](https://github.com/lxc/distrobuilder), [Goaci](https://github.com/appc/goaci), and [Acbuild](https://github.com/containers/build).

A few of these tools, however, have automated and/or scripted the build steps. They allow users to write up a script file that includes a set of instructions to be then executed by the container runtime during the build process. Such an approach makes it so much easier to share and distribute container images, requiring only the sharing of a template file that guides the runtime during the container build, instead of sharing the pre-built container image.

A popular example of such a script file is the Dockerfile, introduced by Docker. The Docker daemon, the main component of the Docker Engine, reads this Dockerfile and runs every instruction in it to assemble the image. During this build process, a context is also required, which is either a path to a set of files on the localhost or a URL to a similar set of files. All the files found in the context are recursively added into the build and are the basis of the root filesystem of the future container. Typically the Dockerfile sits at the root of the context, and the context only includes files needed for the image build. The Dockerfile specifies a starting point, a generic base image that is to be customized to achieve the desired configuration of the new image.

Another tool following a similar path to build OCI container images is [Buildah](https://buildah.io/). Buildah is composed of an extensive set of CLI tools, while it supports reading Dockerfiles in order to build container images. Dockerfile is supported by [Podman](https://podman.io/) as well, an image and container management tool and runtime. However, the Dockerfile is not the only file that allows runtimes to build container images. Podman introduces what appears to be its own file to build container images, named Containerfile, which in reality uses the same syntax as Dockerfile.

So it seems that users find it beneficial to have template script files with instructions for container image builds because this build method offers a great level of flexibility towards the build of the container image. It is not surprising after all, considering that it is easier to control the build process from scratch, instead of starting with an image built and customized for other purposes and going through a reverse-engineering phase to strip the image of unwanted features and finally adding the desired features we then want to see in our running containers.

1. Crear una imagen de contenedor desde cero implica especificar manualmente o a través de scripts todos los componentes de la imagen.
2. Hay varias herramientas que gestionan diferentes aspectos del proceso de creación de imágenes desde cero, como runc, Docker, Buildah, Podman, Distrobuilder, Goaci y Acbuild.
3. Algunas de estas herramientas han automatizado o creado scripts para las etapas de construcción, lo que facilita la distribución de imágenes de contenedores.
4. Un ejemplo popular de un archivo de script para la construcción de imágenes de contenedores es Dockerfile, utilizado por Docker.
5. Buildah es otra herramienta que permite la creación de imágenes de contenedores y es compatible con Dockerfile.
6. Podman también admite Dockerfile y ha introducido su propio archivo para la creación de imágenes de contenedores llamado Containerfile.
7. Utilizar plantillas de archivos de script para la construcción de imágenes de contenedores es beneficioso porque ofrece un alto nivel de flexibilidad en el proceso de construcción de la imagen.

# **Create an Image from a Running Container**

This container image creation method assumes there is a running container that can be used as a source to create a new image out of it. The method aims to build a new container image based on the root filesystem of an existing running container. The process requires an export of the running container’s root filesystem into an archive, typically a tarball. The following steps extract the root filesystem from the archive and through an import feature, the root filesystem is added into the build of the new container image.

A few of the tools supporting such root filesystem export, extraction and import operations are Docker, Buildah, Podman, and Distrobuilder.

It is worth noting that this method applies to containers that have been customized and to base containers alike. It is particularly beneficial in the case of customized containers because it allows for the container’s configuration to be "persisted" as a new container image representing a new version of the application container or a new application altogether.

Este método crea una imagen de contenedor a partir de un contenedor en ejecución. El sistema de archivos raíz del contenedor en ejecución se exporta a un archivo, generalmente un archivo tar. Luego, el sistema de archivos raíz se extrae del archivo y se importa en la construcción de la nueva imagen del contenedor. Docker, Buildah, Podman y Distrobuilder son herramientas que admiten este método. Este método se aplica tanto a contenedores personalizados como a contenedores base. Este método es particularmente beneficioso para contenedores personalizados, ya que permite que su configuración se "persista" como una nueva imagen de contenedor que representa una nueva versión del contenedor de aplicación o una nueva aplicación en su totalidad.

# **Convert a Container Image**

Image conversion is no longer such a popular topic with users. This method assumed the existence of Docker or OCI images, and through conversion tools allowed users to build ACI formatted images through an image conversion process.

Since the ACI is no longer of interest to users, the tools supporting ACI compatible runtimes, images and containers have been retired slowly. Clearly, the tools supporting the image conversion method have stopped receiving updates as well. Such image conversion tools are [Docker2aci](https://github.com/appc/docker2aci) , and [Oci2aci](https://github.com/octools/oci2aci).

As a result of the unification effort of the container and image standards, with the focus now shifted towards OCI, the community aims to develop and implement new features for OCI and Docker supported runtimes, images, and containers.

- La conversión de imágenes ya no es un tema popular entre los usuarios.
- Este método supone la existencia de imágenes Docker o OCI y permitía a los usuarios construir imágenes en formato ACI a través de un proceso de conversión de imágenes.
- Los usuarios han perdido interés en ACI, lo que ha llevado a la retirada gradual de las herramientas compatibles con ACI.
- Las herramientas de conversión de imágenes, como Docker2aci y Oci2aci, también han dejado de recibir actualizaciones.
- Debido al esfuerzo de unificación de los estándares de contenedores e imágenes, y al enfoque en OCI, la comunidad está trabajando en el desarrollo e implementación de nuevas características para los tiempos de ejecución, imágenes y contenedores compatibles con OCI y Docker.

# **Dockerfile Alternative: Containerfile**

Dockerfile gained its popularity as a key component in the container image build process with Docker. However, containerization tools such as Podman and Buildah have also adopted a similar approach to building container images from Dockerfiles. In addition to using Docker’s specific instructions stored in a Dockerfile, these containerization tools support another file – the Containerfile, following the same instructions syntax as the Dockerfile. While the instructions described in this chapter are Dockerfile instructions, keep in mind they can be used in Containerfiles as well, and both files are supported by Podman and Buildah to build container images.

- El Dockerfile es un componente clave en el proceso de construcción de imágenes de contenedores con Docker.
- Podman y Buildah también han adoptado un enfoque similar para la construcción de imágenes de contenedores a partir de Dockerfiles.
- Estas herramientas de contenerización admiten otro archivo denominado Containerfile, que sigue la misma sintaxis de instrucciones que el Dockerfile.
- Las instrucciones descritas en este capítulo son instrucciones de Dockerfile, pero se pueden usar en Containerfiles también.
- Tanto el Dockerfile como el Containerfile son admitidos por Podman y Buildah para construir imágenes de contenedores.

# **Instructions**

There are several instructions available to aid us in building the desired images. The full list of instructions is available in [Docker's official Documentation](https://docs.docker.com/engine/reference/builder/). The instructions can be divided into two distinct categories - build time and run time instructions.

Build Time instructions are used to build the image from the Dockerfile and Run Time instructions are used while a container is starting from the pre-built image.

**FROM**
This instruction initializes a new build stage and defines the base image used to build our resulting image. Dockerfile must start with this instruction, but it may be preceded only by ARG instructions to define arguments to be used by subsequent FROM instructions. Multiple FROM instructions can be found in Dockerfile. You can see an example below:

```bash
FROM [--platform=<platform>]<image>[:<tag>][AS <name>]
FROM [--platform=<platform>]<image>[@<digest>][AS <name>]

FROM --platform=linux/amd64 alpine:3.10 AS base-alpine
```

**ARG**
This instruction can be placed before or after FROM. If it is before FROM, it is outside the build stage and cannot be used in any instruction after FROM. However, the default value of an ARG declared before the first FROM can be invoked with an ARG instruction followed by the ARG name and no value. When an ARG instruction is declared, we can pass a variable at build time, as shown in the example below:

Esta instrucción puede ser colocada antes o después de FROM. Si se coloca antes de FROM, está fuera de la etapa de construcción y no se puede usar en ninguna instrucción después de FROM. Sin embargo, el valor predeterminado de un ARG declarado antes del primer FROM se puede invocar con una instrucción ARG seguida del nombre del ARG y sin valor. Cuando se declara una instrucción ARG, podemos pasar una variable en tiempo de construcción, como se muestra en el ejemplo a continuación:

```bash
ARG <name>[=<default value>

ARG BUILD_NO

$ docker build --build-arg BUILD_NO=v2
```

RUN

This instruction is used to run commands inside the intermediate container created from the base image during the build process, and commit the results as a new image. RUN may be used in both *shell* and *exec* forms. In *shell* form the command is run by default in a shell such as **/bin/sh -c** in Linux or **cmd /S /C** in Windows. However, the shell can be modified by passing a desired shell, or with the SHELL command. You can see some examples below:

Esta instrucción se utiliza para ejecutar comandos dentro del contenedor intermedio creado a partir de la imagen base durante el proceso de construcción, y comprometer los resultados como una nueva imagen. RUN puede ser utilizado en ambas formas de *shell* y *exec*. En la forma de *shell*, el comando se ejecuta de forma predeterminada en una shell como **/bin/sh -c** en Linux o **cmd /S /C** en Windows. Sin embargo, la shell puede ser modificada pasando una shell deseada, o con el comando SHELL. Puedes ver algunos ejemplos a continuación:

```bash
RUN ["executable", "param1", "param2"]  # exec form
RUN <command>                           # shell form

RUN ["/bin/bash", "-c", "echo New Shell"] # exec form
RUN /bin/bash -c 'echo New Shell'.        # shell form
```

**LABEL**

It adds metadata to the resulting image in the key-value pair format:

Añade metadatos a la imagen resultante en formato de par clave-valor:

```bash
**LABEL <key1>=<value1> <key2>=<value2> <key3>=<value3> ...**

**LABEL version="1.0" env="dev"**
```

**EXPOSE**

This instruction defines network ports we want to open from the container, for external entities to connect with the container on those exposed ports:

Esta instrucción define los puertos de red que deseamos abrir desde el contenedor, para que entidades externas se conecten con el contenedor en esos puertos expuestos:

```bash
**EXPOSE <port> [<port>/<protocol>...]**

**EXPOSE 80/tcpEXPOSE 80/udp**
```

**COPY**

This instruction allows content to be copied from our build context to the intermediate container which would get committed to create the resulting image. It has the following forms:

Esta instrucción permite copiar contenido desde nuestro contexto de construcción hasta el contenedor intermedio que se comprometería para crear la imagen resultante. Tiene las siguientes formas:

**COPY [--chown=<user>:<group>]<src>... <dest>COPY [--chown=<user>:<group>]["<src>",... "<dest>"]**

```bash
**COPY [--chown=<user>:<group>]<src>... <dest>COPY [--chown=<user>:<group>]["<src>",... "<dest>"]**
```

The second form is required when the source name contains white spaces.

El segundo formulario es necesario cuando el nombre de la fuente contiene espacios en blanco.

```bash
**COPY --chown=2000:mygroup /host/path/file* /container/filesystem/**
```

**ADD**
Similar to COPY, but it provides more features, such as accepting a URL for source, and accepting a tar file as source which is extracted at destination.

Similar a COPY, pero proporciona más características, como aceptar una URL como fuente y aceptar un archivo tar como fuente que se extrae en el destino.

```bash
ADD [--chown=<user>:<group>]<src>... <dest>
ADD [--chown=<user>:<group>]["<src>",... "<dest>"]

ADD https://raw.githubusercontent.com/lfstudent/image.png /downloads/logo.png
```

**WORKDIR**

This instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile:

Esta instrucción establece el directorio de trabajo para cualquier instrucción RUN, CMD, ENTRYPOINT, COPY y ADD que le siga en el Dockerfile:

```bash
**WORKDIR /path/to/workdir**
```

In the example below, **run.sh** would run from **/code**:

```bash
**RUN mkdir /codeCOPY run.sh /codeWORKDIR /codeCMD run.sh**
```

ENV

This instruction sets environment variables inside the container:

Esta instrucción establece variables de entorno dentro del contenedor:

```bash
**ENV <key>=<value>**

**ENV WEB="192.168.2.3"**
```

**VOLUME**

This instruction is used to create a mount point and mount external storage on it:

Esta instrucción se utiliza para crear un punto de montaje y montar almacenamiento externo en él:

```bash
**VOLUME ["/path/data"]VOLUME /path/data**
```

**USER**

To set the user name or UID of the resulting image for any subsequent RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile. You can see an example below:

Para establecer el nombre de usuario o UID de la imagen resultante para cualquier instrucción RUN, CMD y ENTRYPOINT que la siga en el Dockerfile, puede ver el siguiente ejemplo:

```bash
USER <user>[:<group>]
USER <UID>[:<GID>]

USER student:student
USER 1000:1000
```

**ONBUILD**

Defines instructions to be executed at a later time, when the resulting image from the current build process becomes the base image of any other build:

Define las instrucciones que se ejecutarán en un momento posterior, cuando la imagen resultante del proceso de construcción actual se convierta en la imagen base de cualquier otra construcción:

```bash
**ONBUILD <INSTRUCTION>**

**ONBUILD RUN pip install docker --upgrade**

**For example, let's take the image created from the  
Dockerfile which has the instruction lfstudent/python:onbuild . 
This image becomes the base image in  Dockerfile ,
 like the following:
FROM lfstudent/python:onbuild...Then, while creating the image, we would see this message:
Step 1 : FROM lfstudent/python:onbuild
# Executing 1 build trigger...Step 1 : RUN pip install docker --upgrade---> Running in c5503d7c1475...**
```

**STOPSIGNAL**

This instruction allows us to set a system call signal that will be sent to a container to exit, such as 9, SIGNAME, or SIGKILL:

Esta instrucción nos permite establecer una señal de llamada al sistema que se enviará a un contenedor para que salga, como 9, SIGNAME o SIGKILL:

Esto es útil para la definición de una señal de salida personalizada para nuestro contenedor.

```bash
STOPSIGNAL signal
```

**HEALTHCHECK**
At times, while our container is running, the application inside may have crashed. Ideally, a container with such behavior should be stopped. To prevent a container from reaching that state, application readiness and liveliness are defined with the HEALTHCHECK instruction, available in the following forms:

En ocasiones, mientras nuestro contenedor se está ejecutando, la aplicación que se encuentra dentro puede haber fallado. Idealmente, un contenedor con este comportamiento debería detenerse. Para evitar que un contenedor llegue a ese estado, la preparación y la actividad de la aplicación se definen con la instrucción HEALTHCHECK, disponible en las siguientes formas:

```bash
HEALTHCHECK [OPTIONS] CMD command
HEALTHCHECK NONE

HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
```

**SHELL**

To define a new default shell for commands that run in Shell form, if the defaults are not desired (default in Linux is  **`/bin/sh -c**,` while in Windows is  **`cmd /S /C`**. With the SHELL instruction we may change the default shell used to run commands in shell form. You can see an example below:

Para definir una nueva shell predeterminada para comandos que se ejecutan en forma de shell, si los valores predeterminados no son deseados (el predeterminado en Linux es **`/bin/sh -c`**, mientras que en Windows es `**cmd /S /C**`). Con la instrucción SHELL podemos cambiar la shell predeterminada usada para ejecutar comandos en forma de shell. A continuación se muestra un ejemplo:

```bash
**SHELL ["/bin/bash", "-c" ]**
```

# **Run Time Instructions**

**CMD**

This is a runtime instruction executed when the container is started from the resulting image. There can only be one CMD instruction in a Dockerfile. If there are more than one CMD instructions, then only the last one would take effect. The CMD instruction provides defaults to the container, which get executed from the resulting image. CMD is used in three forms:

Esta es una instrucción de tiempo de ejecución que se ejecuta cuando el contenedor se inicia a partir de la imagen resultante. Solo puede haber una instrucción CMD en un archivo Dockerfile. Si hay más de una instrucción CMD, entonces solo la última tendrá efecto. La instrucción CMD proporciona valores predeterminados al contenedor, que se ejecutan a partir de la imagen resultante. CMD se usa en tres formas:

- *Shell* form: CMD command param1 param2
- *Exec* form (preferred): CMD ["executable","param1","param2"]
- As default parameters to ENTRYPOINT - CMD ["param1","param2"]

In the Dockerfile of nginx container image we noticed the following:

**`CMD ["nginx", "-g", "daemon off;"]`**

It means that when the container starts from the nginx image, by default, it runs the **`nginx -g daemon off;**` command to start the nginx daemon. We can override the defaults defined by CMD. In the following example, we are starting the **`/bin/s`h** application instead of the nginx daemon at start time:

Esto significa que cuando el contenedor se inicia a partir de la imagen de nginx, por defecto se ejecuta el comando `nginx -g daemon off**;**` para iniciar el demonio de nginx. Podemos anular los valores predeterminados definidos por CMD. En el siguiente ejemplo, estamos iniciando la aplicación **`/bin/sh`** en lugar del demonio de nginx en el momento de inicio:

`$ docker run -it nginx /bin/sh`

**ENTRYPOINT**

Similar to CMD, ENTRYPOINT is also a runtime instruction. But the executable/command provided during the build time cannot be overridden at runtime. Although we can change the arguments to it, whatever is passed after the executable/command, is considered an argument to the executable/command. There can only be one ENTRYPOINT instruction. It is used in two forms:

- *Shell* form: ENTRYPOINT command param1 param2
- *Exec* form: ENTRYPOINT ["executable", "param1", "param2"]

CMD can be used in conjunction with ENTRYPOINT, but in that case, CMD provides the default arguments to ENTRYPOINT.

# **Dockerfile References**

The best place for references on Dockerfiles is [Docker Hub](https://hub.docker.com/). Typically, for each Image Registry we can find the Dockerfiles for the respective tags. There are projects which maintain a collection of Dockerfiles for reference, such as Fedora Cloud.

# **Exclude Files and Directories from Build with .dockerignore**

During an image build, the Docker client zips the referenced context folder and sends it to the Docker Host. If we want to exclude some files and directories during the  docker image build process, then we should list them in the **.dockerignore** file in the referenced folder:

**`$ cat .dockerignore`**

**`code`**

**`tmp`**

**`test.py`**