Container runtimes were designed at their core to serve a similar purpose - to provide a platform for the management of container environments and their lifecycle. Although containers follow different standards and container runtimes implement different interfaces, the runtimes come equipped with a minimal set of commands allowing users to interact with the runtime environments and containers.

# **Learning Objectives**

By the end of this chapter, you should be able to:

- Understand containers and container operations in general.
- Explain different operations supported by the container runtimes: runc, Docker, Podman, crictl/CRI-O, and rkt.
- Perform container operations with runc, Docker, podman, and crictl/CRI-O.

# **Containers**

A **container** is a process running on the host system. This process is started by the container runtime from a container image, and the runtime provides a set of tools to manage the container process. The container is an isolated environment that encapsulates a running application, and it runs based on the configuration defined in the container image.

At runtime, virtualization features available at the kernel level are attached to the container process to help manage various aspects of the container’s virtual environment. Namespaces virtualize the container process’s PID, network, root, and users. Cgroups help set resource usage limits the container process can consume on the host system, and security contexts enforce permissions the container process has on the host system.

A container, as a runtime object, consumes the typical resources any running process would consume on a system: storage for the file system and any saved configuration files, CPU, memory, and networking to serve traffic to/from external clients, and other containers or devices on the system.

- Un contenedor es un proceso que se ejecuta en el sistema host.
- El proceso se inicia a partir de una imagen de contenedor y el tiempo de ejecución proporciona herramientas para gestionar el proceso del contenedor.
- El contenedor es un entorno aislado que encapsula una aplicación en ejecución y se ejecuta en función de la configuración definida en la imagen del contenedor.
- Las características de virtualización disponibles a nivel del kernel se adjuntan al proceso del contenedor para ayudar a gestionar varios aspectos del entorno virtual del contenedor.
- Los namespaces virtualizan el PID, la red, la raíz y los usuarios del proceso del contenedor.
- Cgroups ayudan a establecer límites de uso de recursos que el proceso del contenedor puede consumir en el sistema host, y los contextos de seguridad hacen cumplir los permisos que el proceso del contenedor tiene en el sistema host.
- Un contenedor consume los recursos típicos que cualquier proceso en ejecución consumiría en un sistema: almacenamiento para el sistema de archivos y cualquier archivo de configuración guardado, CPU, memoria y redes para atender el tráfico hacia/desde clientes externos, y otros contenedores o dispositivos en el sistema.

# **Container Operations**

Since a running container resembles a Virtual Machine and there are tools facilitating various management operations on VMs, then, as expected, there are a variety of operations available to manage a container’s lifecycle - operations facilitated by the container runtimes.

While some operations may be basic in nature and allow us to create, start, run, pause, resume, stop, restart, and remove/delete a container, other operations may be advanced and allow us to interact directly with the application in the running container or further customize the behavior of the running container.

Advanced operations may allow us to

- run a container in the background
- run a container in daemon mode
- list and sort containers
- interact with the container’s environment byinspecting a running container’s statuslisting processes inside a containeropening an interactive terminal in the container’s environmentlimiting resources utilization such as CPU and memorysetting access privileges.

For troubleshooting purposes, we can also view container events and logs to help determine the cause for any unexpected behavior. Based on the complexity of a container runtime, various operations may or may not be supported.

runc

The [runc](https://github.com/opencontainers/runc) container runtime runs containers bundled in the OCI format, based on a JSON configuration file. As a low-level runtime, runc is capable of reading a predefined default JSON file or it may allow us to manually generate the configuration ourselves.

At runtime, we may request a terminal into the running container’s environment but an argument needs to be set in the configuration file to allow such a request. The configuration file also allows us to control whether the container should run in the background and to control the user the container runs on the host system, by allowing it to run with root privileges or as a non-root user.

We can easily run a container with the **runc run** command, which *creates*, *starts*, and then *deletes* a stopped container on our behalf - operations that otherwise can be individually invoked. At any time we can list containers and their states with the **runc list** command.

With runc, we may setup a running container and allow its lifecycle to be managed with systemd by setting up a systemd unit file so that systemd may restart the container when it exits.

While this was not meant to be an exhaustive list of operational commands, for a full listing of the runc commands and their options explore the [runc reference Documentation](https://github.com/opencontainers/runc) on GitHub.

Docker

[Docker](https://www.docker.com/), the most complex container platform, supports a wide range of container operations. Traditionally, there is a set of commands such as **docker create**, **docker start**, **docker stop**, **docker restart**, **docker kill**, **docker rm** that allow a user to alter the state of a container, together with **docker ps** for a listing of containers, **docker logs** to display container logs, **docker inspect** displays detailed information about an object, and **docker stats** displays resource usage.

A modern approach to docker commands groups commands based on their operational context, such as *container*, *image*, *engine*, *system*. Until the traditional set of commands is deprecated, some traditional and modern commands can still be used interchangeably if they produce the same results. However, there are commands that produce slightly different results, such as the traditional **docker inspect** that displays details about docker objects in general, while the modern **docker container inspect** displays details about containers. Although the modern approach introduces some extra typing, it is recommended that users adopt it and become familiar with it because the traditional command structure may not be supported for much longer. A simple workaround to the extra typing issue is the implementation of aliases. Therefore, we can use **docker container create**, **docker container start**, etc.

There are commands that allow a user to interact with the processes running inside a container. Such commands are **docker attach** or **docker container attach** to access a container's standard input, output, and error streams, **docker exec** or **docker container exec** to run commands in a running container, **docker pause** or **docker container pause** and **docker unpause** or **docker container unpause** to pause and unpause processes inside one or more containers. The **docker run** or **docker container run** (combinations of **container create** and **container start**) are complex commands that allow users to run a command in a new container while configuring its network, resources, storage, security and other options. The **docker top** or **docker container top** commands are useful to list processes running in a container.

A widely used feature of Docker is its capability to build container images out of running containers. With **docker commit** or **docker container commit**, we can create an image from a container and its most recent configuration.

While **docker run** with its extensive set of options is a popular command to deploy containers, it is limited to deploy a single container per run. However, there are multi-container applications and services, which may need to be deployed all at once, and managed as such. For these types of applications, Docker provides us with the **docker compose** tool, a more complex command that supports a wide set of operations similar to **docker run**. The **docker compose create** and **start** allow for the containers of a service to be created and then started. A cumulative effect is achieved by the **docker compose up** command which creates and starts containers of a service. The opposite commands would be **docker compose stop** and **rm** to stop and remove the stopped service containers, or the cumulative **docker compose down**. With **docker compose restart** we can restart the containers of a service, while we can also pause and unpause them if needed.

While this was not meant to be an exhaustive list of operational commands, for a full listing of the docker commands, their options, and samples, explore the [docker CLI reference Documentation](https://docs.docker.com/engine/reference/commandline/docker/).

Podman

[Podman](https://github.com/containers/libpod) is another tool for running individual containers or pods based on OCI container images. It is capable of managing containers, pods, container images both OCI-based and Docker-based.

Podman supports operations to manage the entire lifecycle of a container. For someone familiar with Docker, transitioning into the Podman environment will be seamless. Podman’s command structure follows the same commands as Docker and it supports container operations through the **podman** and the **podman container** commands – which is the reason why we may treat this section on Podman as a review of an earlier section on Docker. In addition, Podman supports sets of commands for pod management, for volumes and network management.

Podman supports commands such as **podman create**, **start**, **stop**, **restart**, **kill**, **rm** that allow a user to alter the state of a container, together with **podman ps** for a listing of containers, **logs** to display container logs, **inspect** displays detailed information about an image or a container, and **stats** displays resource usage. Keep in mind that we can also use **podman container create**, **podman container start**, **podman container inspect**, etc.

To interact with the processes running inside a container, users may use **podman attach** or **podman container attach** to attach to a running container, **podman exec** or **podman container exec** to run commands in a running container, **podman pause** or **podman container pause** and **podman unpause** or **podman container unpause** to pause and unpause processes inside one or more containers. The **podman run** or **podman container run** (combinations of **container create** and **container start**) are complex commands that allow users to run a command in a new container while configuring its network, resources, storage, security and other options. The **podman top** or **podman container top** commands are useful to list processes running in a container.

Podman can build a container image with **podman commit** or **podman container commit** commands, which create an image from a container and its most recent configuration.

While **podman run** and **podman container run** commands allow users to deploy single containers, there are multi-container applications and services, which may need to be deployed all at once, and managed as such. For these types of applications Podman provides the **podman compose** tool, a more complex command that supports a wide set of operations similar to **podman run**. The **podman compose create** and **start** allow for the containers of a service to be created and then started. A cumulative effect is achieved by the **podman compose up** command which creates and starts containers of a service. The opposite commands would be **podman compose stop** and **rm** to stop and remove the stopped service containers, or the cumulative **podman compose down**. With **podman compose restart** we can restart the containers of a service, while we can also pause and unpause them if needed.

While this was not meant to be an exhaustive list of operational commands, for a full listing of the podman commands and their options, explore the [podman CLI reference documentation](https://docs.podman.io/en/latest/Commands.html).

crictl/CRI-O

While [CRI-O](https://github.com/cri-o/cri-o) runs as a daemon on the host system, it supports container lifecycle management operations for containers created from images originating from various container image registries, such as quay.io or Docker Hub.

CRI-O’s approach is slightly different than the previously discussed runtimes. There is a single command **crio**, to start the CRI-O daemon.

The daemon's environment is managed by a handful of configuration files responsible for setting up all expected command options for the daemon, signature verification policies, container image registries, and container storage.

When the daemon is started, the **crio** command accepts a multitude of configuration options to describe how a container is expected to run. The configuration options describe mount points, runtime (default is runc), how are image volumes handled, logs and metrics, permissions, process count limits, networking namespace management, and possibly TLS encryption.

CRI-O was designed as a container runtime for container orchestrators such as Kubernetes, however, as the runtime matured, a command line tool was also designed to allow users to directly interact with the runtime and manage containers. This command line tool – **crictl**, supports a series of commands with similar effects to the ones we’ve already seen with docker and podman. The **crictl** commands structure however, is less complex, as the majority of the **crictl** commands are aimed at container management.

Users are able to run **crictl create** and **crictl start**, or **crictl run**, **crictl stop**, **crictl rm**, **crictl logs**, **crictl stats**, **crictl ps**, **crictl attach**, **crictl exec**, **crictl inspect**, etc., and they would have the expected effect against containers.

While this was not meant to be an exhaustive list of operational commands, for a full listing of the crictl commands and their options, explore the [crictl CLI reference documentation](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md).

rkt (Archived)

The section describing container operations with rkt, an archived project, is included here for its historical value.

As opposed to other container runtimes, [rkt](https://github.com/rkt/rkt) operates on pods instead of containers. In the rkt runtime, a pod represents the running instance of a container image, and this is one of the reasons why rkt integrates very well with container orchestrators such as Kubernetes and Nomad. Another reason for its easy integration is rkt’s support of the [Container Network Interface](https://github.com/containernetworking/cni) (CNI) that is also used by Kubernetes.

The primary operation supported by rkt is to run a pod from a container image with the **rkt run** command, with various supported options to specify the pod’s networking configuration, mount storage volumes, override configuration options, and change default environmental variables.

Rkt can run container images from both [quay](https://quay.io/) and [Docker](https://hub.docker.com/) registries, supporting both ACI and OCI formatted images, although broad native support is still in development. A key feature still in development is the support for Docker image signature validation.

Other operations allow us to **fetch** a container image from a registry if it is not present in our local store before it runs as a pod, **stop** a running pod, **rm** (remove) a stopped pod, **list** pods and their status, while more advanced operations allow a user to **enter** a pod to explore its applications and file system, retrieve application exit **status**.

Rkt also allows us to create a container image by *exporting* an exited pod. At times we may want to customize a stock container image while we run it as a pod, and later distribute and re-use the customized image rather than having to reconfigure again the stock container image. A neat feature of rkt is the *garbage collection* (**gc**) operation, which helps clean up stopped and exited pods. Although running as a daemon is not supported by rkt, an init system such as systemd may manage a rkt process if the pod is started with the systemd-run utility.

While this was not meant to be an exhaustive list of operational commands, for a full listing of the rkt commands and their options explore the [rkt CLI reference Documentation](https://github.com/rkt/rkt/blob/master/Documentation/commands.md).

- Las operaciones de contenedores con crictl no son tan sencillas como con Docker y Podman.
- Docker y Podman están orientados al usuario y ofrecen comandos intuitivos, fáciles de personalizar y adaptar, mientras abstraen las operaciones en tiempo de ejecución.
- Runtimes como containerd y CRI-O no vienen con utilidades de línea de comandos dedicadas, ya que están diseñados para ser utilizados en entornos complejos por herramientas de orquestación de contenedores equipadas con interfaces sofisticadas para interactuar con ellos.
- La solución es crictl, una utilidad de línea de comando de alto nivel que se puede personalizar para usar containerd, CRI-O o Docker como tiempo de ejecución de backend.
- Aunque parece tan intuitivo como las herramientas anteriores, con comandos de creación y ejecución de contenedores admitidos, entre otros, crictl utiliza un enfoque más complejo al ejecutar contenedores, requiriendo que el usuario defina un sandbox aislado para que se ejecute el contenedor.
- Este sandbox se llama pod, un concepto ampliamente utilizado por el orquestador de contenedores Kubernetes, Podman e incluso el retirado rkt.
- Este ejercicio de laboratorio tiene como objetivo presentar los pasos específicos requeridos por crictl en conjunción con el tiempo de ejecución CRI-O para ejecutar contenedores, exec, stop y remove.
- Dado que ambos proyectos siguen madurando, algunos de los pasos presentados aquí pueden requerir precaución adicional para evitar posibles cargas en tiempo de ejecución que puedan evitar que algunas funciones se comporten como se espera.
- Tenga en cuenta que el comando crictl debe ejecutarse con sudo y el servicio crio también debe estar en ejecución.
- En caso de que el servicio crio no esté en ejecución o sus funciones dejen de funcionar y el tiempo de ejecución se vuelva no receptivo, asegúrese de iniciar o reiniciar el servicio crio con sudo systemctl start crio.service o reiniciar si es necesario.

- **`crictl`** es una utilidad de línea de comandos de alto nivel para interactuar con los runtimes de contenedores CRI-O, containerd o Docker. A diferencia de las herramientas de línea de comandos dedicadas a Docker y Podman, crictl no es tan intuitivo y fácil de usar.
- **`crictl`** requiere que el usuario defina un "sandbox" aislado para que el contenedor se ejecute, utilizando el concepto de "pod". Este es un enfoque más complejo que el de Docker y Podman, que automatizan las operaciones de runtime.
- En este texto, se describe un ejercicio de laboratorio para aprender a usar **`crictl`** en conjunto con CRI-O para realizar operaciones comunes de contenedores, como crear, ejecutar, detener y eliminar contenedores.
- Es importante tener en cuenta que tanto **`crictl`** como CRI-O están en constante evolución, por lo que algunas de las instrucciones presentadas en este ejercicio pueden requerir precaución para evitar problemas de tiempo de ejecución.
- Para usar **`crictl`**, es necesario ejecutarlo con permisos de superusuario (**`sudo`**) y asegurarse de que el servicio de CRI-O esté en ejecución. Si el servicio de CRI-O se detiene o presenta problemas, se puede reiniciarlo con el comando **`sudo systemctl start crio.service`**.

En resumen, **`crictl`** es una herramienta de línea de comandos útil para interactuar con runtimes de contenedores como CRI-O, containerd o Docker, aunque su uso puede requerir más atención y configuración que herramientas más intuitivas como Docker y Podman. Es importante tener en cuenta que tanto **`crictl`** como CRI-O están en evolución constante y puede haber cambios en la forma en que se realizan ciertas operaciones de contenedor.

With Docker, Podman, and crictl, a container can be referred by:

A. Its name
B. Its ID
C. Its partial ID, as long as it is unique
D. All of the above

D. Todas las anteriores son formas válidas de referirse a un contenedor en Docker, Podman y crictl.

Es correcta porque todas las opciones mencionadas son formas válidas de referirse a un contenedor en estas herramientas de contenedores. Los contenedores pueden ser referidos por su nombre, ID completo o ID parcial si es único.

With rkt we can create a Pod from a Docker container image. True or False?

A. True
B. False

La respuesta es verdadera (True). Con rkt, podemos crear un Pod desde una imagen de contenedor de Docker.

Which of the following docker, podman, and crictl commands can be used to display detailed information about a container?

A. docker container ls, podman container list, crictl ps
B. docker container ls -a, podman container list -a, crictl pods
C. docker container inspect, podman container inspect
D. docker container exec, podman container exec

La respuesta correcta es C. docker container inspect, podman container inspect.

La opción C es la única que incluye el comando "inspect", que es utilizado para mostrar información detallada sobre un contenedor en particular. Las opciones A y B muestran una lista de contenedores y sus estados actuales, pero no proporcionan información detallada sobre un contenedor en particular. La opción D se utiliza para ejecutar un comando en un contenedor existente, pero no para mostrar información detallada sobre él.

A running container is:

A. An image on the host running the container runtime
B. A process on a host running a container runtime
C. A daemon on the host running the container runtime
D. None of the above

La respuesta correcta es B.

Explicación: Un contenedor en ejecución es un proceso que se está ejecutando en un host que ejecuta un tiempo de ejecución de contenedor, como Docker o Podman. Un contenedor es una instancia en tiempo de ejecución de una imagen de contenedor que se ejecuta como un proceso aislado en el espacio de usuario del host.

How can we create a new image from a container or Pod?

A. runc create, docker create, rkt create
B. docker container export, podman export pod, rkt export pod
C. docker container commit, podman container export, rkt export
D. runc export, rkt commit

La respuesta correcta es:

C. docker container commit, podman container export, rkt export

Podemos crear una nueva imagen a partir de un contenedor o Pod utilizando el comando "docker container commit" o "podman container commit". Este comando toma una instantánea del sistema de archivos del contenedor y crea una nueva imagen a partir de ella. Alternativamente, también podemos exportar un contenedor o Pod en formato tar con "docker container export", "podman export pod", o "rkt export pod", y luego importarlo como una nueva imagen.