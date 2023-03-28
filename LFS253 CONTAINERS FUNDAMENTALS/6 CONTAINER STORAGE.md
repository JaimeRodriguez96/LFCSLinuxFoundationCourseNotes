# **Chapter Overview**

In a world of application-centric microservices, storage methods play a key role in infrastructure architecture, and in application design and implementation. In addition to differentiating between microservices that are either data producers, data consumers, or both, users also have to determine the significance of the data for the enterprise in order to categorize the data as either volatile/non-persistent data, or critical/persistent data, in order to determine the appropriate storage management approach.

# **Learning Objectives**

By the end of this chapter, you should be able to:

- Understand the Copy-on-Write feature of UnionFS.
- Create and mount volumes with Docker and Podman.
- Understand storage management with CRI-O and rkt.

# **Manage Storage with Docker**

The complexity of Docker as a container management framework is also reflected in its methods of handling [storage](https://docs.docker.com/storage/) for containerized applications. We will explore various storage concepts, access modes, storage drivers and volume types as we learn how Docker manages storage and volumes for its containers.

UnionFS (Union File System) es un sistema de archivos que permite la unión de varios sistemas de archivos en un solo punto de montaje. Es utilizado en Docker como el sistema de archivos base para almacenar y gestionar los contenedores y sus capas de imagen.

UnionFS utiliza una técnica de capas de montaje para combinar varios sistemas de archivos en un único árbol de archivos. Esto significa que cada capa se superpone a la anterior y puede agregar, modificar o eliminar archivos y directorios. Cuando un archivo se accede en un sistema de archivos unificado, el sistema busca primero en la capa más alta, y si no lo encuentra, busca en la capa inferior, y así sucesivamente.

En Docker, las capas de imagen de los contenedores se combinan a través de UnionFS para formar un único sistema de archivos que se utiliza para ejecutar el contenedor. Esto permite que los contenedores compartan capas comunes, lo que reduce el espacio de almacenamiento necesario y mejora el rendimiento de la creación y gestión de los contenedores.

# **UnionFS with Copy-on-Write**

UnionFS is used by Docker to overlay a base container image with storage layers, such as ephemeral storage layer, custom storage layer, and config layer at the time a new container is created. The ephemeral storage is reserved for the container’s Input/Output (I/O) operations and it is not recommended to be used for persistent data; instead, a volume should be mounted on the container to provide persistent storage not managed by UnionFS.

The [Copy-on-Write (CoW) strategy](https://docs.docker.com/storage/storagedriver/#the-copy-on-write-cow-strategy) of UnionFS allows users to “indirectly” modify the content of files available to the running container from the base container image storage layer. While the container image files are Read-Only, when a user attempts to modify such a file, no errors or mechanisms will prevent the user from doing so. Instead, the base container image file is copied and saved on the ephemeral storage layer of the container and the user is allowed to make changes to the new copy of the file. In essence, a copy of a file is being saved when a user attempts to edit a Read-Only file of the base container image, all while the base container image file remains intact.

This strategy is used at the operating system level for memory management and process management, and it is used by Docker to manage the storage for container images, running containers, and to minimize I/O and the size of each storage layer.

Copy-On-Write (COW) es una técnica utilizada por Docker para minimizar el uso de recursos del sistema al crear y administrar contenedores. Cuando se crea un contenedor Docker a partir de una imagen, se utiliza una técnica de COW para crear una capa de sistema de archivos única y separada para ese contenedor.

Cada vez que se realiza una acción en el contenedor que modifica el sistema de archivos, Docker crea una nueva capa de sistema de archivos que contiene solo las diferencias entre el estado anterior y el nuevo estado. Esto se hace sin modificar la capa original, lo que significa que múltiples contenedores pueden compartir la misma capa de imagen base, lo que ahorra espacio en el disco.

La técnica de COW también se utiliza para reducir el consumo de recursos de la CPU y la memoria al compartir recursos entre múltiples contenedores. Por ejemplo, si varios contenedores ejecutan la misma aplicación, los recursos de la aplicación se compartirán entre los contenedores, en lugar de duplicarlos para cada uno.

En resumen, Copy-On-Write en Docker es una técnica que permite crear y administrar contenedores de manera eficiente al compartir recursos y minimizar el uso de espacio en disco.

# **Docker Storage Drivers**

Typically, not a lot of data needs to be written to the container’s writable layer. However, when such a requirement presents itself, then a storage driver needs to be used to control how the container images and the running containers are stored and managed on the host system.

While Docker supports a variety of [storage drivers](https://docs.docker.com/storage/storagedriver/), each driver may be limited by its supported backing filesystems. The **overlay**, **overlay2** and **aufs** drivers are supported by xfs and ext4 filesystems, **devicemapper** driver is backed by **direct-lvm**, while **vfs** is supported by any filesystem. Despite the implementation and feature differences between storage drivers, they all use the CoW strategy for stacked storage layers.

Los Docker Storage Drivers son componentes del motor de Docker que se encargan de administrar cómo se almacenan y acceden a los datos en los contenedores Docker. Los Storage Drivers son responsables de realizar la tarea de mapear los archivos y directorios dentro de un contenedor a un sistema de archivos de host subyacente.

Docker utiliza diferentes Storage Drivers para manejar la persistencia de datos dentro de los contenedores. Cada Storage Driver tiene su propio enfoque para administrar y almacenar los datos en el host subyacente. Los Storage Drivers disponibles incluyen:

- aufs (Advanced Multi-Layered Unification Filesystem)
- btrfs (B-tree File System)
- overlay and overlay2
- devicemapper
- zfs (Z File System)
- vfs (Virtual File System) - utilizado solo para pruebas y desarrollo.

La elección del Storage Driver dependerá del sistema de archivos subyacente que se utilice en el host y de las necesidades específicas de la aplicación que se está ejecutando en los contenedores.

En resumen, los Docker Storage Drivers son responsables de administrar cómo se almacenan y acceden a los datos dentro de los contenedores Docker, y la elección del Storage Driver adecuado dependerá del sistema de archivos subyacente y de las necesidades específicas de la aplicación.

Es importante señalar que Copy-On-Write (COW) es una técnica de gestión de almacenamiento de Docker que es utilizada por algunos de los Storage Drivers, como Overlay y Overlay2. Por lo tanto, no se trata de una elección entre Docker Storage Drivers y COW, sino de una elección entre diferentes Storage Drivers que implementan la técnica de COW.

Sin embargo, podemos comparar Overlay y Overlay2 (que utilizan COW) con otro Storage Driver que no lo utiliza, como Devicemapper, para resaltar las diferencias en su enfoque de almacenamiento:

| Características | Overlay/Overlay2 | Devicemapper |
| --- | --- | --- |
| Eficiencia de almacenamiento | Muy eficiente. Utiliza COW para reducir el espacio de almacenamiento necesario. | Menos eficiente que Overlay y Overlay2. No utiliza COW, lo que significa que los datos se copian en cada contenedor, ocupando más espacio en disco. |
| Rendimiento | Excelente rendimiento debido a que utiliza COW para evitar la duplicación de datos y reducir la cantidad de operaciones de E/S necesarias. | Un poco más lento que Overlay y Overlay2 debido a que no utiliza COW. |
| Compatibilidad del sistema de archivos subyacente | Limitado a sistemas de archivos que admiten características como enlaces simbólicos y archivos regulares. | Compatible con una amplia variedad de sistemas de archivos subyacentes. |
| Mantenimiento | Puede requerir una mayor cantidad de mantenimiento debido a la necesidad de administrar varias capas de imágenes. | Requiere menos mantenimiento porque utiliza una única capa de almacenamiento en cada contenedor. |
| Características avanzadas | Permite compartir partes de una imagen base entre varios contenedores para ahorrar espacio en disco. | Ofrece características avanzadas como snapshots y thin provisioning. |

En general, Overlay y Overlay2 son una opción más eficiente en términos de espacio y rendimiento, pero pueden requerir un poco más de mantenimiento debido a la necesidad de administrar varias capas de imágenes. Por otro lado, Devicemapper es compatible con una amplia variedad de sistemas de archivos subyacentes y ofrece características avanzadas como snapshots y thin provisioning. La elección del Storage Driver dependerá de las necesidades específicas de la aplicación y del sistema de archivos subyacente que se utilice en el host.

# **Storage and Docker Volumes**

Applications and containers make use of two different types of data - ephemeral and persistent; therefore Docker takes separate approaches when managing ephemeral vs. persistent data.

Ephemeral data is data typically needed for a very short period of time, and there is no damage to the enterprise when that data is lost once the container is terminated and deleted. Ephemeral data is stored on ephemeral storage, which is specified and defined within the scope of the container, and the ephemeral storage also gets deleted together with the container. For ephemeral data management, Docker provides a bind mount mechanism tied in with the host’s directory structure. The bind mount references a directory of the host system created on-demand if not existing when the container is created. The downsides of bind mounts is that they allow containers to access sensitive data on the host system, and they cannot be directly managed from the Docker CLI.

It is recommended, however, the use of a tmpfs mount for ephemeral data, not only to prevent the data from being stored permanently on the container’s writable storage layer, but also to increase the container’s performance.

Persistent data, however, is critical data that has to be stored in a location that provides data resilience, to then later be managed by an aggregator or other applications designed to process and manage critical data. Persistent data is recommended to be stored on external persistent volume mounts managed from the Docker CLI for easy backup and migration, flexibility, portability, sharing, and extended functionality. Persistent volumes are not managed by UnionFS, are not specified and defined only within the scope of a container, and do not get deleted together with the container.

An additional method that allows a Docker host to communicate with a container on Windows is a named pipe.

Docker users utilize volume plugins to store Docker volumes on remote hosts or cloud providers, to encrypt the contents of volumes, or to add other functionality. Such plugins are the Azure File Storage plugin, BeeGFS Volume Plugin, DigitalOcean Block Storage plugin, Flocker plugin, gce-docker plugin, GlusterFS plugin, VMware vSphere Storage plugin, etc.

- Las aplicaciones y contenedores utilizan dos tipos diferentes de datos: efímeros y persistentes.
- Los datos efímeros se almacenan en un almacenamiento efímero definido en el ámbito del contenedor y se eliminan junto con el contenedor al finalizar su ciclo de vida.
- Docker proporciona un mecanismo de montaje vinculado con la estructura de directorios del host para la gestión de datos efímeros, pero este mecanismo tiene algunas limitaciones.
- Se recomienda el uso de un montaje tmpfs para los datos efímeros, ya que esto no solo evita que los datos se almacenen permanentemente en la capa de almacenamiento del contenedor, sino que también mejora el rendimiento del contenedor.
- Los datos persistentes son datos críticos que deben almacenarse en una ubicación que proporcione resiliencia de datos, y se recomienda almacenarlos en volúmenes persistentes externos gestionados desde la CLI de Docker.
- Los volúmenes persistentes no son administrados por UnionFS, no se especifican y definen solo en el ámbito de un contenedor, y no se eliminan junto con el contenedor.
- Los usuarios de Docker pueden utilizar plugins de volumen para almacenar los volúmenes de Docker en hosts remotos o proveedores de la nube, cifrar el contenido de los volúmenes o agregar otras funcionalidades.
- Algunos ejemplos de plugins de volumen son el plugin de Azure File Storage, el plugin de BeeGFS Volume, el plugin de DigitalOcean Block Storage, el plugin de Flocker, el plugin de gce-docker, el plugin de GlusterFS y el plugin de VMware vSphere Storage.

# **Manage Storage with Podman**

Podman uses the [containers/storage](https://github.com/containers/storage) library to manage image storage, container storage, and storage layers.

A layer is a Copy-on-Write (CoW) filesystem defined by a set of changes from its parent layer. While any layer can have only one parent layer, a layer can be a parent to multiple layers, a reason why parent layers should be read-only. Considering the layers and parent layers, an image is a reference to a desired layer, which becomes the image’s read-only top layer. A container then becomes a read-write child of the image’s top layer, and we can spawn multiple containers from one parent image.

Podman also uses drivers to implement storage for its containers, supporting overlayfs, devicemapper, AUFS, btrfs, and zfs drivers. While the default driver for storage volumes is local, other desired plugins need to be defined in a special configuration file. When defining a storage volume with the local driver, it allows for a mounting on the host filesystem, and its supported options closely resemble flags supported by the Linux **mount** command.

# **Manage Storage with CRI-O**

CRI-O also uses the Copy-on-Write (CoW) strategy in conjunction with the [containers/storage](https://github.com/containers/storage) library when unpacking a downloaded container image into the root filesystem of a container. CRI-O uses the containers/storage library to manage storage layers and to create the root filesystems for OCI-compliant containers running in a Kubernetes pod. It implements a variety of storage drivers, such as **overlayfs**, **devicemapper**, **AUFS** and **btrfs**, with **overlayfs** being the default storage driver.

# **Manage Storage with rkt (Archived)**

**The section describing storage management with rkt, an archived project, is included here for its historical value.**

Rkt supports the mounting of host volumes and empty volumes to each application running inside a Pod, with or without mount points, as specified in the ACI Pod manifest.

The host volume type allows for a host directory to be mounted by all applications in the Pod, where an optional parameter may specify whether the volume is read-only or not. The host volume type is specific for the storage of persistent data, which is considered critical and should outlive the Pod and the applications running in the Pod.

For non-persistent data that only needs to be shared among the applications of a Pod, the empty volume may be used instead. Since the volume is created on the fly, the user may specify the access mode, UID and GID of the empty volume.

While a mount point may have been specified for the host and the empty volume types described above, rkt also supports the mounting of volumes without mount points. In such scenarios, the user may specify individual mount points in each application running in the Pod.




if we delete an existing file from a container, the file also gets deleted from the base container image storage layer. True or False?

A.
True
B.
False

La respuesta es B. False (Falso).

Cuando se elimina un archivo existente de un contenedor, no se elimina automáticamente de la capa de almacenamiento de la imagen del contenedor base. Las capas de almacenamiento de la imagen del contenedor base son solo de lectura y no se pueden modificar. Por lo tanto, si se elimina un archivo de un contenedor, solo se elimina del sistema de archivos del contenedor en sí y no afecta la capa de almacenamiento de la imagen base.

Es importante tener en cuenta que los contenedores utilizan una arquitectura de capas, donde cada capa representa una modificación o adición al sistema de archivos. Por lo tanto, si se elimina un archivo de una capa específica, no afectará las capas superiores ni las capas inferiores en la jerarquía de capas.



Assuming myvol is a named volume created with Docker or Podman, what is the correct syntax to mount it inside a container, under /data? Select all answers that apply.

A. run -it --mount source=myvol,target=/data alpine sh
B. run -it -v myvol:/data:ro alpine sh
C. run -it -v /data:/myvol alpine sh
D. run -it -v /data:myvol alpine sh
E. All of the above
F. None of the above

La respuesta correcta es A. run -it --mount source=myvol,target=/data alpine sh.

La opción A utiliza la sintaxis actual para montar un volumen en un contenedor Docker o Podman. La opción B utiliza la sintaxis anterior, pero aún es compatible con versiones más antiguas de Docker. La opción C invierte los términos de origen y destino y la opción D utiliza un formato incorrecto.

Es importante tener en cuenta que el comando "run" inicia un nuevo contenedor. Si el contenedor ya se está ejecutando, se debe utilizar el comando "exec" para interactuar con él. El parámetro "-it" se utiliza para iniciar el contenedor en modo interactivo.

La opción "--mount" especifica que se va a utilizar un volumen y la opción "source=myvol" indica que el nombre del volumen es "myvol". La opción "target=/data" indica que el volumen se montará en el directorio "/data" dentro del contenedor.




What kind of volumes do we use to mount a host directory inside a Pod with rkt?

- **A.** Machine volumes
- **B.** Container volumes
- **C.** Shared volumes
- **D.** Host volumes

La respuesta correcta es D. Host volumes.

En rkt, se utilizan host volumes para montar un directorio del host dentro de un pod. Los host volumes permiten que un pod tenga acceso a un directorio del sistema de archivos del host y se utiliza el mismo enfoque que en Docker. Los host volumes se pueden crear utilizando el comando "rkt run" con la opción "--volume".

Las opciones A, B y C no son correctas en el contexto de rkt. "Machine volumes" no es un término utilizado en rkt. "Container volumes" se refiere a los volúmenes que se crean y administran dentro de un contenedor, mientras que "Shared volumes" se refiere a los volúmenes que se comparten entre varios contenedores en un pod.



What is the default storage driver for CRI-O?

A. overlay
B. Kubernetes
C. OCI
D. containers/storage
E. overlayfs

La respuesta correcta es E. overlayfs.

Overlayfs es el controlador de almacenamiento predeterminado para CRI-O. CRI-O es un implementación de tiempo de ejecución de contenedor compatible con Open Container Initiative (OCI) y se utiliza comúnmente en los clústeres de Kubernetes.

El controlador de almacenamiento es responsable de administrar cómo se almacenan y recuperan los datos en los contenedores. Overlayfs es una capa virtual de archivos que permite combinar varios sistemas de archivos en un solo sistema de archivos. Esto permite que el sistema de archivos del contenedor sea construido sobre la imagen base y las capas adicionales de los contenedores.

Las opciones A y E se refieren a diferentes tipos de controladores de almacenamiento, pero en este contexto, "overlay" es un controlador de almacenamiento para Docker, mientras que "overlayfs" es utilizado por CRI-O. Las opciones B y C no son controladores de almacenamiento, sino estándares de contenedor y especificaciones. La opción D se refiere a un paquete en particular utilizado por algunos sistemas, pero no es el controlador de almacenamiento predeterminado para CRI-O.