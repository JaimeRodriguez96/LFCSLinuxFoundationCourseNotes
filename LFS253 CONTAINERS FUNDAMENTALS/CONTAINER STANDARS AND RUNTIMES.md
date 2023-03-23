LFS253 CONTAINERS FUNDAMENTALS

For a typical application, one of the decisions requiring extensive considerations is about the application’s running environment, whether Bare-Metal, Virtual Machine, or cloud. For a container, however, there is no need for such concerns, because containers are able to run anywhere.

When it comes to scalability, security, performance, costs, maintenance, both typical applications, and containers require a great deal of consideration from the teams involved in their lifecycle management.

# **Standards**

Interoperability has not always been one of the main characteristics of containers. Having several projects implementing similar virtualization features, with each project tackling issues independently in its own way, resulted in an even larger number of guidelines and rules. Users needed to follow specified requirements to be able to use one particular containers framework versus another, and, as expected, they were not interoperable. Users of a specific type of container were locked down to a particular container runtime. In time, this became an issue that slowed down the development process in general. As a result, gradually, sets of well defined standards have been put in place to guide not only the building process of container images, but also the configuration of the container runtime environments.

Being able to move container images between different container runtime environments, by enabling containers to be interoperable and allowing them to integrate with third-party storage and network plugins of various projects and vendors, are just a few of the benefits of container standards.

For several years, the community has been split between two distinct container standards - the App Container standard and the Open Container Initiative, and their respective implementing tools. After years of rivalry between standards and their respective implementations, the community has decided to establish a single, unified standard to govern container operations across all platforms and runtimes. To ease the unification process between the two standards and to avoid reinventing the wheel, several distinct features of the App Container standard have been planned to slowly migrate into the Open Container Initiative. Image signing, dependencies, and support for pods are just a few of the features that were planned to be implemented into the Open Container Initiative. While the merging process is not that seamless, the community is still able to make great progress towards the single unified Open Container Initiative standard.

Since this new direction has been agreed upon, the App Container standard has not received any new features, except minor support for its existing features. The following sections introduce the Open Container Initiative, and the App Container standard included for its historical value.

El texto habla de la importancia de la interoperabilidad de los contenedores, que son proyectos que implementan características de virtualización similares. El texto explica que antes había muchos requisitos y reglas para usar diferentes tipos de contenedores, lo que dificultaba el proceso de desarrollo. El texto también menciona que se han establecido estándares para guiar el proceso de construcción e configuración de los contenedores y sus imágenes, lo que permite mover las imágenes entre diferentes entornos y plugins.

El texto habla de la unificación de dos estándares distintos de contenedores: el App Container standard y el Open Container Initiative, y sus respectivas herramientas. El texto explica que la comunidad ha decidido establecer un solo estándar para gobernar las operaciones de los contenedores en todas las plataformas y entornos. El texto también menciona que se han planeado migrar algunas características del App Container standard al Open Container Initiative, como la firma de imágenes, las dependencias y el soporte para pods. El texto reconoce que el proceso de fusión no es fácil, pero que la comunidad está progresando hacia el Open Container Initiative unificado.

# O**pen Container Initiative (OCI)**

The [Open Container Initiative](https://www.opencontainers.org/) (**OCI**) was introduced in 2015 by Docker together with other leaders in the container industry. One of the container runtimes implementing the OCI specification is runC.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/xp4az15uupx2-unnamed.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/xp4az15uupx2-unnamed.png)

The OCI incorporates the Runtime Specification (runtime-spec), the Image Specification (image-spec), and the most recent Distribution Specification (distribution-spec).

The [Runtime Specification](https://github.com/opencontainers/runtime-spec) defines how to run a "filesystem bundle" that is unpacked on disk. An OCI implementation would download and unpack an OCI image into an OCI Runtime filesystem bundle. Then, an OCI Runtime would run the OCI Runtime Bundle.

The [Image Specification](https://github.com/opencontainers/image-spec) helps with the development of compatible tools to ensure consistent container image conversion into containers.

The [Distribution Specification](https://github.com/opencontainers/distribution-spec) standardizes how container images are distributed through image registries.

Runtime Specification

The OCI [Runtime Specification](https://github.com/opencontainers/runtime-spec) specifies the configuration, execution environment, and lifecycle of a container. The container's configuration file specifies how a container is created. The execution environment is specified to offer environment consistency between container runtimes.

A Standard Container, as referred to by the OCI, encapsulates a software component with its dependencies in a portable format, run by any OCI compliant runtime regardless of the underlying host machine and the contents of the container.

Standard Containers are defined based on the following principles:

- Set of standard operations to be performed by various standard tools:start, stop, and create for container tools,copy and snapshot for filesystem tools,download and upload for network tools,
- Content-agnostic operations have the same effect regardless of content,
- Infrastructure-agnostic to run in any OCI supported infrastructure,
- Designed for automation with same standard operations regardless of content and infrastructure,
- Industrial-grade delivery in-house DevOps or external customer-based software delivery mechanism.

The OCI Runtime Specification defines how container files are organized and what data and metadata they should include. The term “bundle” refers to a container and its data being stored locally to be run by an OCI compliant runtime. A container bundle includes configuration data and root filesystem information required to load and run the container.

Between the runtime and lifecycle, the container properties are specified to ensure consistency in container behavior:

- State includes container properties such as OCI version, container ID, container status, PID, bundle directory, and annotations,
- Lifecycle lists the events since the container was created until its deletion,
- Errors are not returned based on this specification,
- Warnings are not returned based on this specification,
- Operations supported by runtimes to manage container lifecycle: state, create, start, kill, delete,
- Hooks for additional actions.

The configuration file includes metadata that implements standard operations against the container. It includes the container’s root filesystem, mounts array, the container process to start, the container’s hostname, annotations, and hooks for user specified additional actions. There are also platform specific configuration options for declaring platform specific features for containers running on Linux, Solaris, Windows, and on hypervisors

Image Format Specification

The [Image Specification](https://github.com/opencontainers/image-spec) defines the OCI Image Format that includes a manifest, a set of filesystem layers, a configuration file, and an optional image index.

The OCI Image Specification includes the following:

- Image Manifest - specifying the configuration and a set of layers that are part of a single container image, for a specific architecture and operating system,
- Image Index - indexing a set of image manifests, with each manifest intended for a specific architecture and operating system,
- Image Layout - a filesystem layout of the image contents,
- Filesystem Layer - describes how to serialize a filesystem into a stack of layers,
- Image Configuration - a document determining the layer ordering in the stack, and configuration required for the conversion into a runtime bundle,
- Conversion - describing the translation method from file system layers and an image configuration to a filesystem and an OCI Runtime configuration,
- Descriptor - describing the content type, a digest as identifier, metadata and content references.

Distribution Specification

The [Distribution Specification](https://github.com/opencontainers/distribution-spec), the latest OCI standard introduced, defines an API protocol that standardizes how OCI images and other types of content are being distributed. While manifests and digests are often used to define the content of OCI images, the Distribution Specification helps with the definition of artifacts - more complex content types that can also be distributed through registries – such as charts and policies.

La Especificación de tiempo de ejecución de OCI especifica la configuración, el entorno de ejecución y el ciclo de vida de un contenedor. El archivo de configuración del contenedor especifica cómo se crea un contenedor. El entorno de ejecución se especifica para ofrecer consistencia ambiental entre los tiempos de ejecución del contenedor.

Un Contenedor Estándar, según lo referido por OCI, encapsula un componente de software con sus dependencias en un formato portátil, que se ejecuta por cualquier tiempo de ejecución compatible con OCI independientemente de la máquina host subyacente y el contenido del contenedor.

Los Contenedores Estándar se definen basándose en los siguientes principios:

Conjunto de operaciones estándar que deben ser realizadas por varias herramientas estándar:

- iniciar, detener y crear para las herramientas del contenedor,
- copiar e instantánea para las herramientas del sistema de archivos,
- descargar y cargar para las herramientas de red, Las operaciones agnósticas al contenido tienen el mismo efecto independientemente del contenido, Infraestructura agnóstica para ejecutarse en cualquier infraestructura compatible con OCI, Diseñado para la automatización con las mismas operaciones estándar independientemente del contenido y la infraestructura, Entrega industrial en casa DevOps o mecanismo externo basado en el cliente de entrega de software.

La Especificación de tiempo de ejecución OCI define cómo se organizan los archivos del contenedor y qué datos y metadatos deben incluir. El término “paquete” se refiere a un contenedor y sus datos almacenados localmente para ser ejecutados por un tiempo de ejecución compatible con OCI. Un paquete de contenedores incluye datos de configuración e información del sistema de archivos raíz requeridos para cargar y ejecutar el contenedor.

Entre el tiempo de ejecución y el ciclo de vida, las propiedades del contenedor se especifican para garantizar la consistencia en el comportamiento del contenedor:

Estado incluye propiedades del contenedor como versión OCI, ID del contenedor, estado del contenedor, PID, directorio del paquete y anotaciones, Ciclo de vida enumera los eventos desde que se creó el contenedor hasta su eliminación, Errores no se devuelven según esta especificación, Advertencias no se devuelven según esta especificación, Operaciones admitidas por ru

# **App Container (appc)**

The App Container standard has been retired together with its implementing tools, following the decision of the community to focus on a single unified container standard - the Open Container Initiative. This section describes the concepts defining the App Container standard, included here for their historical value.

The [App Container](https://github.com/appc/spec) (**appc**) specification was introduced in 2014 by CoreOS in collaboration with Google and RedHat. One of the container runtimes implementing the appc specification is rkt. The appc specification defines a container image format, how an application is packaged into a container image, a deployment mechanism and a runtime.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/nvwbepy3koy0-unnamed.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/nvwbepy3koy0-unnamed.png)

In addition to defining the Application Container Image (ACI) format for container images, the appc enables the user community to develop tools to build, validate, and convert container images to ACI image format, such as goaci, docker2aci, deb2aci, actool, acbuild, and oci2aci.

The appc [specification](https://github.com/appc/spec/blob/master/SPEC.md) intends to speed up the design and the deployment of a container while ensuring container image integrity through cryptographic signatures. Appc defines several independent, yet composable, aspects of the application container.

App Container Image

The [App Container Image](https://github.com/appc/spec/blob/master/spec/aci.md) (**ACI**) defines the packaging, compression and extraction methods of files that make up the container image together with the validation of container image’s integrity. Individual files are packaged together into ACI archives that ensure not only fast and easy build, but also safe storage when downloaded and extracted to be saved on a system.

The ACI describes the following aspects of a container image:

- File system layout of the container image with its directory tree.
- Format of the container image archive, together with compression and encryption options.
- Unique image ID that allows its integrity verification, where the image ID is obtained by hashing the uncompressed ACI archive file.
- Image manifest file which describes the image and includes attributes such as the container name, specification version, users, exec command, file system, environment, resources, mount points, ports, dependencies, and metadata.

**App Container Image Discovery**

The [App Container Image Discovery](https://github.com/appc/spec/blob/master/spec/discovery.md) defines how a container image name is linked to a downloadable container image. The container image name format is similar to a URL, however, there is no scheme to resolve it. The ACI discovery process defines the usage of the name in conjunction with attributes from the image manifest file when retrieving a particular container image.

In order to address different URL types such as image, signature and public key, the specification describes the following steps in the ACI discovery process:

- Discovery templates to render images and signature URLs,
- Discovery URL templates to include an image or signature URL together with a URL to the keys required for ACI signature validation,
- Validation rules for ACI signature (optional),
- Authentication when retrieving container images (optional).

**App Container Pod**

The [App Container Pod](https://github.com/appc/spec/blob/master/spec/pods.md) defines a Pod as the deployment and execution unit for one or a group of container images. A Pod represents an execution context obtained by namespacing various aspects and resources of the Linux Operating System:

- PID namespace where app containers can talk to each other,
- Network namespace where an IP address is shared between app containers,
- IPC namespace for app containers communication,
- UTS namespace for app containers to share a hostname.

A Pod is defined by a manifest which is a template listing the app containers to be grouped at execution time with their dependencies and isolators for resource constraints management.

**App Container Executor**

The [App Container Executor](https://github.com/appc/spec/blob/master/spec/ace.md) (**ACE**) defines how to run an app container image, more specifically environment configuration for the running app, and the app’s interaction with the environment.

Part of the app’s environment, ACE must set the following:

- UUID of Pod for unique identification within a domain,
- File System setup as a chrooted environment for each app container part of the Pod,
- Volume setup and mounted at specified mount points inside the apps,
- Network setup with a loopback interface and optional IP layer interfaces,
- Logging of apps is expected to stdout and stderr, which then ACE is able to capture and persist.

The app’s interaction with the environment is defined by specifying:

- Execution environment such as PATH, app name from the image manifest, metadata service URL and container name,
- Linux isolators specific for seccomp, SELinux, and AppArmor define app permissions and capabilities,
- Resource Isolators to enforce resource constraints for CPU and RAM at Pod level, at individual app level, or a mix of both.

# **Architecture, Description and Main Features of Container Runtimes**

A container runtime is guided by a runtime specification, which describes the configuration, execution environment and the lifecycle of the container. The role of a container runtime is to provide an environment supporting basic operations with images and the running containers, that is both configurable and consistent, where container processes are able to run. A runtime’s consistency is one of the top benefits for running containers. Regardless of the underlying infrastructure - whether an on-prem Data Center or a Cloud Infrastructure as a Service (IaaS), containers’ behavior is expected to be the same, thus allowing users to develop and test containers on any system across all tiers - from development to production.

A container runtime is designed to perform several default operations under the hood as a response to user commands. The container runtime extracts the container image content and stores it on an overlay filesystem, that utilizes the Copy-on-Write mechanism for virtual file integrity. When the runtime executes a container, it interacts with the kernel to set resource limits, build isolation layers through virtualization mechanisms like control groups and namespaces in order to run a containerized application as specified by the container image.

Let’s examine a few container runtimes to become familiar with the container image specifications they support, together with their unique architectural features.

# **Container Runtimes - runc**

[runc](https://github.com/opencontainers/runc) is a basic CLI tool that leverages the [libcontainer](https://github.com/opencontainers/runc/tree/master/libcontainer) runtime (initially developed by Docker, then later open sourced), together providing a low level container runtime focused primarily on container execution. runc implements the OCI specification, and it handles the creation and running of OCI containers.

Its simplicity, however, is not without shortcomings. runc does not expose an API and does not provide container image management capabilities. While it does not support image build operations, it does not provide image download or image integrity check capabilities either. That is, the creation of the container image components, such as the OCI bundle, is not part of runc’s scope. runc may aid with the creation of the OCI spec, but the OCI bundle has to be created separately, such as exporting a container's filesystem with Docker, and made available to runc.

Although runc does not include a centralized daemon, it may be integrated with the Linux service manager - systemd.

# **Container Runtimes - containerd**

Another simple container runtime is [containerd](https://containerd.io/), which adds robustness and portability by supporting several container operations, such as the storage and transfer of container images, executing containers, attaching storage and network to containers.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/bbwl426oixd0-containerd-horizontal-color.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/bbwl426oixd0-containerd-horizontal-color.png)

As an industry-standard container runtime, containerd was designed to run as an embedded daemon of a more robust container management system, and not to be used directly by everyday users. Among the adopters of the containerd daemon are the Docker engine, Kubernetes services of IBM (IKS) and Google Cloud (GKE), Cloud Foundry, and Kata Containers. [containerd is a Cloud Native Computing Foundation (CNCF) graduated project](https://www.cncf.io/projects/).

containerd supports the OCI container image specification and the OCI runtime specification by utilizing runc as its low level OCI runtime with the possibility to extend it with plugins to support the Kubernetes’ Container Runtime Interface (CRI) as well. containerd adds implementation for some missing, yet desired, capabilities of runc, such as support for container image pull and push operations, and network interfaces and network namespaces management operations.

Users can manage containerd with a CLI designed for CRI-compatible runtimes – [crictl](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md). This CLI tool supports operations with OCI images, containers, and pods.

Containerd es un motor de contenedores de código abierto que se utiliza para ejecutar y administrar contenedores de aplicaciones en un sistema operativo host. Fue desarrollado originalmente como una parte del proyecto Docker y posteriormente se convirtió en un proyecto independiente de la Cloud Native Computing Foundation (CNCF).

Containerd proporciona una plataforma unificada para la ejecución de contenedores, lo que significa que puede administrar contenedores de diferentes sistemas de orquestación, como Kubernetes o Docker Swarm. Es compatible con varios sistemas operativos, incluidos Linux y Windows, y se integra con herramientas de infraestructura como CNI y gRPC.

Una de las principales características de Containerd es su capacidad para administrar imágenes de contenedores de forma eficiente, incluido el almacenamiento, la distribución y la ejecución de las mismas. Además, permite la creación de redes de contenedores, el aislamiento de recursos y la implementación de políticas de seguridad.

En resumen, Containerd es una herramienta esencial para la administración y ejecución de contenedores en un entorno de infraestructura de contenedores, y es ampliamente utilizado en aplicaciones empresariales y en la nube.

# **Container Runtimes - Docker**

[Docker](https://www.docker.com/) is one of the most robust and complex container development and management platforms in the container industry. Although we list it here as a container runtime, Docker is a lot more than just a simple, or not so simple, container platform. The Docker platform supports a wide range of operations, from container image management to container lifecycle and runtime management.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/o7d7n8l793yr-unnamed.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/o7d7n8l793yr-unnamed.png)

Earlier Docker versions were based on the [Linux Containers](https://linuxcontainers.org/) (LXC) container runtime as its low level runtime, which came with a few limitations at that time. As a result, Docker decided to develop in-house its own proprietary runtime, [libcontainer](https://github.com/opencontainers/runc/tree/master/libcontainer), to replace LXC as its low level container runtime. Today however, libcontainer is an Open Source project and it is a component of the runc container runtime. Another Open Source project started by Docker is [containerd](https://containerd.io/). As a Docker Engine component, containerd is responsible for unpacking the container image bundle and for invoking runc to run the container.

Docker implements the OCI specification, and it provides the tools necessary to bundle applications into digitally signed OCI container images, to interact with container image registries, and to manage the containers’ lifecycle from creation to removal, and all of their dependencies in between.

Docker’s complexity is not only reflected by the multitude of operations it supports on container images and running containers, but also by its architecture. Docker is powered by the Docker Engine - a client-server application that runs on several Linux distributions, on MacOS and Windows systems as well. The Docker Engine is composed of the Docker host running the Docker daemon, a REST API to communicate with the daemon, and a Docker client. Additionally, one or multiple container image registries are present too.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wwqtc1iuh19b-LFS253_CourseGraphics_3.2.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wwqtc1iuh19b-LFS253_CourseGraphics_3.2.png)

**Docker Architecture**

Docker client is the CLI tool that allows users to run **docker** commands against a Docker daemon running on a Docker host. The client may run on any machine - it may run on the Docker host in conjunction with the Docker daemon, or on a remote system. Either way, the client and daemon communicate through REST API, over UNIX sockets or a network interface. Also, the client is capable of communicating with more than one daemon running on different Docker hosts.

Another tool that allows users to run multi-container applications is Docker Compose. Users define application services and dependencies with the help of a YAML file and then start them with a single command.

Docker host is a system running the Docker daemon - called **dockerd**. The daemon is responsible for building, running, and distributing Docker containers. It listens for Docker API requests from the Docker client and manages Docker objects such as images, containers, networks, and volumes. A daemon can act alone, or interact with other daemons to manage distributed Docker services across multiple Docker hosts clustered together. While it integrates easily with Linux init systems such as upstart and systemd, Docker daemon is configured by default to use a variety of resources available on the Docker host. The daemon is configured to listen to both unsecured and secured traffic through TLS certificate and key pair, routed to a particular port of the Docker host. Also, the daemon is configured to save all of its operating data to a default directory of the host. The Docker host also enables a local container image cache, a local image repository, where the most recently used container images get stored for easy access.

Docker registries store Docker container images. There is a popular public registry, the [Docker Hub](https://hub.docker.com/), which Docker uses by default to search for images, but custom private registries may be set up as well, both in the public cloud or locally on a private network. Latest Docker releases have introduced interoperability features, allowing Docker to query non-Docker registries, such as [quay](https://quay.io/) (the default image registry for the container orchestrator, Kubernetes).

As Docker evolves, so do its installation and configuration tools. Docker Desktop is the installer for the Docker framework components on Windows and Mac systems. Among the installed components are Docker Engine, CLI client, Docker Compose, and Kubernetes. As a newer tool, Docker Desktop is backward compatible with environments setup with the superseded Docker Machine, the tool used to install Docker Engine on older Mac and Windows systems, and to provision and manage remote Docker hosts.

After the installation step, the Docker Desktop provides options to:

- configure startup,
- expose the daemon unsecurely (for backward compatibility with legacy clients),
- set Engine resource limits,
- share local directories of the host with containers by enabling storage volumes to be mounted in containers,
- configure networking and DNS,
- configure a proxy for Docker to use when pulling container images.

# **Container Runtimes - Podman**

[Podman](https://podman.io/) is a container engine which allows users to develop, manage and run OCI containers, in both privileged and unprivileged mode. In contrast with other container engines, Podman is a daemonless engine which provides a REST API service that allows containers to be launched on-demand by remote applications.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wkqd9c55wsfv-podman-logo.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/wkqd9c55wsfv-podman-logo.png)

Podman uses the [libpod](https://github.com/containers/podman/tree/main/libpod) library to support pods, containers, images, and volumes. It also uses Conmon, an additional lightweight container monitoring application that runs in parallel with each container of a pod, helping Podman to run in detached mode while still allowing users to attach to containers. It mimics the Docker CLI and it is capable of managing OCI images and run containers.

# C**ontainer Runtimes - CRI-O**

[CRI-O](https://cri-o.io/) is a minimal implementation of the [Container Runtime Interface](https://github.com/containerd/cri) (CRI) to enable the usage of any [Open Container Initiative](https://www.opencontainers.org/) (OCI) compatible runtime with [Kubernetes](https://kubernetes.io/), a popular container orchestrator. As a lightweight alternative to using Docker or rkt as the runtimes for Kubernetes, it supports both GPG signed and unsigned container images. CRI-O supports runc and Kata Containers as the container runtimes but any OCI-conformant runtime can be plugged in instead. CRI-O is a [Cloud Native Computing Foundation (CNCF) incubating project](https://www.cncf.io/projects/).

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/9vypht9sz00e-crio-logo.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/9vypht9sz00e-crio-logo.png)

The CRI-O runtime has been optimized for Kubernetes, and it also implements the [Container Network Interface](https://github.com/containernetworking/cni) (CNI) for networking and supports CNI plugins. Architecturally, CRI-O is packed with libraries that pull container images from registries and create container filesystems, a tool that prepares container configuration, then it invokes runc to start containers which end up being handled by conmon, a daemon that collects logs and monitors for hazards such as out of memory (OOM) conditions. CRI-O also supports container security that is provided by several core Linux features such as SELinux, capabilities or seccomp.

Users can manage CRI-O with a CLI designed for CRI-compatible runtimes – [crictl](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md). This CLI tool supports operations with OCI images, containers, and pods.

CRI-O, por otro lado, es un proyecto de código abierto que se enfoca en proporcionar una implementación ligera de Kubernetes Container Runtime Interface (CRI). CRI-O también se centra en la gestión de contenedores y es compatible con los estándares de OCI. CRI-O fue desarrollado específicamente para ser utilizado como un runtime de contenedor para Kubernetes.

En resumen, mientras que containerd es una tecnología de gestión de contenedores más amplia que se puede utilizar con diferentes herramientas de gestión de contenedores, CRI-O es una implementación específica de runtime de contenedor que se utiliza con Kubernetes.

# **Other Container Runtimes**

There are other container runtimes popular with users, such as LXC, LXD, Kata Containers, gVisor, and rkt.

LXC

[LXC](https://linuxcontainers.org/lxc/introduction/), under the Linux Containers umbrella project, is composed of an API and tools that allow users to manage virtual Linux systems or application containers. Through the use of Operating System virtualization features such as namespaces, chroot, cgroups, capabilities combined with various security features such as seccomp, apparmor and SELinux, LXC aims to create a virtual environment resembling a Linux system but managed by the same kernel.

LXD

[LXD](https://linuxcontainers.org/lxd/introduction/), also under the Linux Containers umbrella project, is a Linux container manager running as a daemon, that resembles a manager of Virtual Machines. The REST API exposed by LXD allows users to manage various aspects of system containers such as security, scalability, transferability, resources (CPU, memory, disk), networking (bridges and tunnels), and storage. LXD architecture also supports plugins for easy integration with OpenStack and other cloud management platforms.

Kata Containers

The [Kata Containers](https://katacontainers.io/) runtime aims for a secure environment built of container-like lightweight Virtual Machines. Security is achieved through dedicated kernels that isolate the network, memory, and I/O, without sacrificing performance. Although a simple solution for an environment running containers, Kata Containers support OCI containers and integrate with the Kubernetes CRI.

gVisor

[gVisor](https://gvisor.dev/) provides an easy to use virtualized container environment capable of running OCI containers while integrating well with Docker, containerd and Kubernetes.