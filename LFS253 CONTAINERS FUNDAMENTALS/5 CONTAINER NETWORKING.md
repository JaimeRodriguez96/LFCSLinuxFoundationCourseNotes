# **Standards and Specifications**

A typical computing machine, whether a physical host system or a Virtual Machine, is connected to at least one network in order for the system and any running applications to become part of the enterprise’s software ecosystem. The network allows systems to connect with each other, allows applications to communicate with each other, allows for data to be transmitted not only between neighboring systems but also to systems and applications running halfway across the world. In addition, a connected system can be managed remotely, and its resources and applications performance may be monitored. Networking, however, is not at random. Instead, network connectivity and network traffic are governed by networking standards and principles, topologies, protocols, policies, rules, etc.

Since containers encapsulate running applications, similarly to individual Virtual Machines, they also need to be attached to at least one network. The container network enables communication between microservices running inside containers, containers running on the same host, running on different hosts, or a container and other applications and services from anywhere in the world. Connected containers are also easier to manage and monitor.

Container networking is guided by sets of standards that specify how a container may join one or multiple networks simultaneously. There are two container networking standards we need to be concerned about: the Container Network Model (CNM) and the Container Network Interface (CNI). Both networking models present pros and cons in terms of adoption and level of support.

You can read more about the container networking standards in *[The Container Networking Landscape: CNI from CoreOS and CNM from Docker](https://thenewstack.io/container-networking-landscape-cni-coreos-cnm-docker/)*.

1. Una máquina de computación típica, ya sea un sistema host físico o una máquina virtual, está conectada a al menos una red para formar parte del ecosistema de software de la empresa.
2. La red permite la conexión de sistemas y aplicaciones, la transmisión de datos y el monitoreo remoto de los recursos y el rendimiento de las aplicaciones.
3. La conectividad y el tráfico de red están gobernados por estándares, principios, topologías, protocolos, políticas y reglas de red.
4. Los contenedores también necesitan estar conectados a al menos una red para permitir la comunicación entre los microservicios y otras aplicaciones y servicios.
5. La conexión de los contenedores a la red también facilita su administración y monitoreo.
6. Existen dos estándares de redes de contenedores: el Modelo de Red de Contenedores (CNM) y la Interfaz de Red de Contenedores (CNI).
7. Ambos modelos de redes de contenedores tienen ventajas y desventajas en términos de adopción y nivel de soporte.

# **The Container Network Model (CNM)**

The Container Network Model (CNM) is the container network specification introduced by Docker and implemented by the [libnetwork project](https://github.com/docker/libnetwork). Companies and other projects such as Cisco, Calico, Weave, Kuryr, VMware, and Open Virtual Networking (OVN) adopted the CNM.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/t29bi9i6sgab-cnm-model.jpg](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/t29bi9i6sgab-cnm-model.jpg)

**The Container Network Model**Source: [libnetwork project on GitHub](https://github.com/docker/libnetwork/blob/master/docs/design.md)

The CNM specifies a set of objects for a container to join a network and be able to talk to other containers that are part of that network. The CNM specifies a network sandbox, an isolated networking stack inside the container which may support multiple individual networks through endpoints. A container endpoint, specified by the CNM and attached to the network sandbox, is an interface paired with an interface on a network, allowing the container to connect to that particular network. The network sandbox supports multiple endpoints, each paired with a different network, thus allowing one container to access multiple networks simultaneously, where the network objects are also specified by the CNM.

At this point, container networking implementation seems to be quite a challenging task and many different solutions have been produced to cover a multitude of use-cases. However, Docker initiated the libnetwork project, aimed to solve these challenges by providing a model that supports all existing solutions. The libnetwork model introduces a consistent programming interface to abstract applications network connectivity - a controller using driver plugins. The network controller of libnetwork is responsible to pair a network with a driver. By default, libnetwork supports drivers such as:

- **bridge** - implementing a Linux bridge
- **overlay** - implementing a network spanning multiple hosts using VXLAN network encapsulation
- **null** - for cases where no networking is required
- it also supports a **remote** package for custom network plugins.

Aside from these objects, the CNM also supports the usage of labels and options, which are attributes in an object metadata.

1. El Modelo de Red de Contenedores (CNM) es una especificación de red de contenedores introducida por Docker e implementada por el proyecto libnetwork.
2. El CNM especifica un conjunto de objetos para que un contenedor se una a una red y pueda comunicarse con otros contenedores que forman parte de esa red.
3. El CNM especifica una "sandbox" de red, una pila de red aislada dentro del contenedor que puede admitir múltiples redes individuales a través de puntos finales.
4. Un punto final de contenedor, especificado por el CNM y conectado a la "sandbox" de red, es una interfaz emparejada con una interfaz en una red, lo que permite que el contenedor se conecte a esa red en particular.
5. La "sandbox" de red admite múltiples puntos finales, cada uno emparejado con una red diferente, lo que permite que un contenedor acceda a múltiples redes simultáneamente.
6. El proyecto libnetwork proporciona una interfaz de programación consistente para abstraer la conectividad de red de las aplicaciones mediante el uso de controladores de plugins.
7. El controlador de red de libnetwork es responsable de emparejar una red con un controlador. Libnetwork admite controladores como "bridge", "overlay" y "null", y también admite un paquete remoto para plugins de red personalizados.
8. Además de estos objetos, el CNM también admite el uso de etiquetas y opciones, que son atributos en los metadatos de un objeto.

# **The Container Network Interface (CNI)**

The [Container Network Interface (CNI)](https://github.com/containernetworking/cni), a Cloud Native Computing Foundation Incubator project, is the container network specification introduced by CoreOS, adopted by projects such as Mesos, Amazon ECS, Kubernetes, Cloud Foundry, OpenShift, rkt, and supported by projects such as Calico, Cilium, Romana, CNI-Genie, VMware NSX, and Weave. It links container platforms such as Kubernetes and Cloud Foundry to multiple distinct container network implementations.

CNI consists of a [specification](https://github.com/containernetworking/cni/blob/master/SPEC.md) and libraries for network plugin development. CNI is a much simpler specification, focusing only on the network connectivity of a container and the release of networking resources once the container is deleted.

- The Container Network Interface (CNI) es una especificación de red de contenedores introducida por CoreOS y adoptada por varios proyectos y compañías.
- CNI es un proyecto incubado por la Cloud Native Computing Foundation.
- CNI se enfoca únicamente en la conectividad de red de un contenedor y la liberación de recursos de red cuando el contenedor es eliminado.
- CNI consta de una especificación y bibliotecas para el desarrollo de complementos de red.
- CNI se utiliza para enlazar plataformas de contenedores como Kubernetes y Cloud Foundry a múltiples implementaciones de redes de contenedores distintas.

# **CNI Plugins**

CNI supports several [plugin groups](https://github.com/containernetworking/plugins), such as the **main** group focused on creating new interfaces, **IPAM** focused on IP address allocation, and finally, the **meta** group for 3rd party plugin support.

Los plugins de CNI en la especificación de CNI se utilizan para implementar diferentes tipos de redes para contenedores, como redes virtuales, redes de contenedores aislados y redes de contenedores que se conectan a redes físicas. Estos plugins se utilizan para conectar los contenedores a la red y para permitir que los contenedores se comuniquen entre sí y con otros servicios y aplicaciones en la red. También se utilizan para configurar y asignar direcciones IP y otros parámetros de red a los contenedores. En resumen, los plugins de CNI son una forma de extender la funcionalidad de CNI y proporcionar una mayor flexibilidad y opciones para la configuración de redes de contenedores.

**Plugin Groups**

Main

The main plugin group includes plugins to create:

- **bridge** interface
- **ipvlan**
- **loopback**
- **macvlan**, which creates a new MAC address and forwards all traffic targeting it to a container
- **ptp**, to create a **veth** pair
- **vlan**
- **host-device**.

The main plugin group also includes a few Windows-specific plugins, such as the **win-bridge** and **win-overlay** that create a bridge and overlay interface respectively.

IPAM

The IPAM group includes:

- the **dhcp** plugin that makes DHCP requests for the container
- **host-local** to maintain a database of allocated IP addresses,
- the **static** plugin to allocate IPv4/IPv6 addresses to a container.

Meta

The meta group extends the CNI capability with plugins for:

- **flannel** compatibility
- sysctl **tuning**
- **portmap** from the host’s address space to the container
- **bandwidth** limitation with traffic control tbf
- **sbr**, for source-based routing configuration
- **firewall** for traffic rules in iptables or firewalld.

# **Container Networking with runc**

While [runc](https://github.com/opencontainers/runc) may not provide means to directly interact with a container’s networking, it does allow for a container’s network stack to be set up or configured right after the container was created and before it is started.

# **Container Networking with Docker**

In a prior section we mentioned Docker introducing the Container Network Model (CNM) and libnetwork. One key takeaway was the driver/plugin approach to implementing the CNM via libnetwork.

Regardless of the network type and driver combination used to implement the container networking, [Docker](https://docs.docker.com/network/#docker-ee-networking-features) is able to manage traffic to and from containers by editing iptables and server routing rules, while providing advanced features such as packet encapsulation, encryption, routing mesh and session affinity.

# **Docker Networking Drivers**

Although many network drivers are available and in use today, typically, a few drivers are used in conjunction in order to implement a solution to the networking requirements of containerized applications.

Bridge
The default network type that Docker containers attach to is the bridge network, implemented by the bridge driver. The main roles of a bridge network are to isolate the container network from the host system network, and to act as a DHCP server and assign unique IP addresses to containers as they are running attached to the bridge. This is a useful feature when containers of an application need to talk to each other while they all run on the same host system. Other features of bridge networks depend whether the bridge is default or user-defined. Most often, a user-defined bridge presents advantages over the default bridge, such as better traffic isolation, automatic DNS resolution, on-the-fly container connection/disconnection to and from the network, advanced and flexible configuration options, and even sharing of environment variables between containers.

Host
The host network driver option, as opposed to the bridge, eliminates the network isolation between the container and the host system by allowing the container to directly access the host network. In addition, it may help with performance optimization by eliminating the need for Network Address Translation (NAT) or proxying since the container ports are automatically published and available as host ports.

Overlay
Gaining a lot of popularity these days is the overlay network type supported by the overlay driver. It is the network type that spans multiple hosts, typically part of a cluster, allowing the containers' traffic to be routed between hosts as containers or services from one host attempt to talk to others running on another host in the cluster.

This network type is very popular with container orchestration platforms because it not only enables traffic across the entire cluster of host systems, but also provides traffic isolation and management through rules and policies.

Macvlan
The macvlan network driver allows a user to change the appearance of a container on the physical network. A container may appear as a physical device with its own MAC address on the network, thus enabling the container to be directly connected to the physical network instead of having its traffic routed through the host network.

None
The none driver option for container networking disables the networking of a container while allowing the very same container to use a custom third-party network driver, if needed, to implement its networking requirements.

Network Plugins
Network plugins expand the capabilities of Docker through third-party network drivers that integrate Docker with specialized network stacks. Certain network plugins may combine features otherwise available from single network drivers while improving network resiliency and policy management.

Bridge

- Es el tipo de red por defecto que se utiliza en Docker.
- Permite aislar la red del contenedor del sistema de red del host.
- Funciona como un servidor DHCP que asigna direcciones IP únicas a los contenedores al ejecutarse en la red del bridge.
- Una red bridge personalizada presenta ventajas como mejor aislamiento del tráfico, resolución automática de DNS, configuraciones avanzadas y flexibles, entre otras.

Host

- Elimina el aislamiento de red entre el contenedor y el sistema del host, permitiendo que el contenedor acceda directamente a la red del host.
- Mejora el rendimiento de la aplicación al eliminar la necesidad de NAT o de proxying.

Overlay

- Es un tipo de red que se extiende a través de varios hosts en un clúster.
- Permite enrutar el tráfico de los contenedores entre los hosts, así como aplicar reglas y políticas de gestión de tráfico.
- Es muy popular en plataformas de orquestación de contenedores.

Macvlan

- Permite que el contenedor tenga su propia dirección MAC y aparezca como un dispositivo físico en la red.
- Permite que el tráfico del contenedor se conecte directamente a la red física en lugar de ser enrutado a través de la red del host.

None

- Desactiva la red de un contenedor pero le permite utilizar un controlador de red de terceros personalizado si es necesario.

Plugins de red

- Expande las capacidades de Docker mediante controladores de red de terceros.
- Los plugins de red pueden combinar características disponibles en otros controladores de red mientras mejoran la resiliencia y la gestión de políticas de red.

# **Docker Network Modes**

Newer features of Docker networking modes aim to further manage network capabilities in multi-host clusters, especially with Docker Swarm.

Internal Mode
We can isolate an overlay network with the internal mode, when we want to restrict containers’ access to the outside world. Otherwise, when a container is connected to an overlay network, Docker connects that container to an additional bridge network to ensure the container has access to the outside world.

Ingress Mode
This mode targets the networking of swarms, and implements a routing-mesh across the hosts of a swarm cluster. While similar to an overlay network in supported options, only one ingress can be defined, and it cannot be removed for as long as services are using it.

Modo interno
Podemos aislar una red superpuesta con el modo interno cuando queremos restringir el acceso de los contenedores al mundo exterior. De lo contrario, cuando un contenedor está conectado a una red superpuesta, Docker conecta ese contenedor a una red de puente adicional para garantizar que el contenedor tenga acceso al mundo exterior.

Modo de ingreso
Este modo se enfoca en la red de enrutamiento de grupos de nodos (swarms) y implementa una malla de enrutamiento en los hosts de un clúster de swarm. Si bien es similar a una red superpuesta en opciones admitidas, solo se puede definir una entrada y no se puede eliminar mientras los servicios la estén usando

# **Container Networking with Podman**

Podman supports the Container Network Interface (CNI), and it builds CNI networks with the help of network drivers and plugins.

**Bridge**
The default network type that Podman builds for its containers is the bridge network, implemented by the bridge driver. The bridge network aims to isolate the container network from the host system network, while the user may configure the network subnet, IP range, and a gateway.

**Macvlan**
The macvlan network driver enables a container to be directly connected to the physical network of the host system instead of having its traffic routed through the host network. This is possible in rooted mode, when the driver can access the host network namespace, thus gaining access to host network interfaces. Otherwise, in rootless mode a separate network namespace would need to be created. When containers use macvlan connections, the dhcp plugin of the Container Network Interface (CNI) needs to be enabled, or the container image would need to include its own DHCP client for the container to be able to interact with the host network DHCP server.

# **Container Networking with CRI-O**

[CRI-O](https://cri-o.io/), the Kubernetes container runtime, has its networking designed to work with Kubernetes pods. Pod networking is set up through the Container Network Interface (CNI). The CNI enables containers to connect to a network when created and then remove resources upon containers’ deletion. It also makes it possible for any network plugin (also called CNI plugin) to work with CRI-O. [CNI plugins](https://github.com/containernetworking/cni#3rd-party-plugins) such as Flannel, Weave, OpenShift-SDN have been tested and performed successfully with CRI-O.

When the CRI-O daemon is started by the **crio** command, there are options to specify the host network IP and to manage the network namespace.

# **Container Networking with rkt**

**The section describing networking with rkt, an archived project, is included here for its historical value.**

Rkt networking supports the Container Network Interface (CNI) which enables containers to connect to a network when created and then remove resources upon containers’ deletion.

Rkt supports two networking configuration modes: the default contained mode and the host mode. The two separate modes help with pod networks grouping and isolation.

**rkt Networking Configuration Modes**

Host Mode
In host mode, the microservice running in the rkt pod inherits the network namespace of the process that invoked rkt, which could be directly from the host system. When rkt is invoked by the host system, the microservice from the rkt pod shares the host system network stack. While in host mode, no isolation is available between the host and the pod networks, the pod having access to the host’s IP address, routes, and iptables.

Contained Mode
The contained network mode implies that a rkt pod runs in a separate network namespace with the network aided by the Container Network Interface (CNI) and CNI plugins.

**The following network interface options are available....**

Built-in networks
Rkt comes with two built-in networks. The first built-in network, the default network, consists of a loopback device and a veth device. The default network veth pair enables a point-to-point link between the rkt pod and the host system while rkt sets the default route in the pod namespace and enables the host’s IP masquerading for Network Address Translation (NAT) of the outgoing traffic. There are several default built-in network types supporting the veth pair, such as ptp (point-to-point) for pod to host networking, bridge, macvlan that assigns a random MAC address to a pod and does not provide node to pod communication, and ipvlan providing IP address distinction.

The second built-in network, default-restricted network, only allows the communication with the host system through the veth interface.

No networking (loopback only)
In order to completely isolate a pod’s network it can be placed in a namespace only with the loopback device.

Additional networks
Rkt pods may join additional networks on top of the default network veth by configuring additional interfaces in the pod. When configuring additional networks users are able to specify the network type based on available plugins, and IP address management (ipam) options such as IP address assignment, gateways and routes.

The ipam section of a network configuration file allows a user to specify IP address allocation policy, gateway and route. By default, rkt offers two ipam types: host-local and DHCP. However, additional ipam types can be implemented through third-party plugins.

Unlike DHCP, the host-local ipam type allocates IP addresses from a range based on a static configuration, and it supports both IPv4 and IPv6 addresses.

For DHCP ipam type, the CNI DHCP plugin runs a client daemon on the host system to proxy between a container DHCP client and network DHCP service and to renew leases when required.

Other plugins
Part of rkt’s flexibility is its capability of working with custom third-party plugins that implement the Container Network Interface (CNI). These plugins are just binaries parsing JSON configuration files which include network configuration specifications. There is an ever growing list of projects developing and maintaining CNI plugins such as: Flannel, Calico, Weave, Cilium, Romana, and VMware-nsx.

Which default network driver is used by Docker and Podman to build networks for containers?

- **A.** host
- **B.** none
- **C.** bridge
- **D.** switch

La respuesta correcta es C. bridge.

La opción C, bridge, es el controlador de red predeterminado que utiliza Docker y Podman para crear redes para contenedores. El controlador bridge crea una red privada interna para el contenedor y enruta todo el tráfico a través de una interfaz de red virtual. Además, permite que los contenedores se comuniquen entre sí y con el host.

Las opciones A y B son controladores de red alternativos, y la opción D no es un controlador de red utilizado por Docker o Podman.

---

Which container network specification does Docker use?

- **A.** Container Network Interface (CNI)
- **B.** Container Network Model (CNM)
- **C.** Both the Container Network Interface (CNI) and the Container Network Model (CNM)
- **D.** None of the above
- 

**B.** Container Network Model (CNM)

La respuesta correcta es D. "None of the above".

Docker utiliza su propio modelo de red, que es diferente tanto de Container Network Interface (CNI) como de Container Network Model (CNM). Aunque puede haber integraciones de Docker con CNI a través de complementos de red de terceros.

Which of the following Docker and Podman commands displays detailed network information?

A. inspect
B. network inspect
C. network list
D. networks

La respuesta correcta es B, "network inspect". Este comando muestra información detallada sobre la configuración de una red específica, incluyendo información sobre contenedores conectados, configuración de controladores de red y opciones de configuración. "inspect" solo proporciona información detallada sobre un contenedor específico, mientras que "network list" y "networks" solo enumeran las redes existentes.

Containers cannot share a network namespace on the same system. True or False?

- **A.**
    
    True
    
- **B.**
    
    False
    
    Falso. Los contenedores pueden compartir un espacio de nombres de red en el mismo sistema utilizando la opción --net=container:<nombre o ID> al crear un nuevo contenedor. Esta opción permite que el nuevo contenedor comparta el mismo espacio de nombres de red que un contenedor existente con el nombre o ID especificado.
    

Which of the following are built-in network types of rkt?

A. ptp
B. bridge
C. macvlan
D. ipvlan
E. All of the above

La respuesta correcta es:

E. Todos los anteriores

Los tipos de red incorporados en rkt incluyen ptp, bridge, macvlan e ipvlan.

Which container network specification is used by Podman and CRI-O?

A. Container Network Interface (CNI)
B. Container Network Model (CNM)
C. Both the Container Network Interface (CNI) and the Container Network Model (CNM)
D. None

La opción correcta es A, "Interfaz de Red del Contenedor" (CNI, por sus siglas en inglés). Tanto Podman como CRI-O utilizan CNI como su especificación de red de contenedor predeterminada. CNI permite que múltiples contenedores se conecten a redes y dispositivos de red, y admite la configuración y el cambio dinámico de las redes de contenedores.