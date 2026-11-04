<p align="center">
  <img src="https://raw.githubusercontent.com/Netec-Mx/261103-CCNA/main/assets/LogoNetec.png" alt="NETEC" width="180" />
</p>

# Implementación y administración de soluciones Cisco (CCNA)

La ruta de aprendizaje Implementación y administración de soluciones Cisco (CCNA) enseña cómo instalar, operar, configurar y verificar una red básica IPv4 e IPv6, configurar componentes de red como switches, routers y controladores de redes de área local inalámbricas (WLAN), administrar dispositivos de red e identificar amenazas básicas de seguridad. Esta ruta de aprendizaje también aborda la introducción de la inteligencia artificial (IA) y el aprendizaje automático (ML) en las operaciones de red. Prepara para el examen Cisco Certified Network Associate (200-301 CCNA) v1.1; al aprobarlo, se obtiene la certificación CCNA.

## Accesos rápidos

- [**Setup Guide del curso**](https://github.com/Netec-Mx/261103-CCNA/blob/main/SETUP_GUIDE.md)
- [Laboratorios por capítulo](#lista-de-laboratorios)

## Estructura

- `SETUP_GUIDE.md`: guía de instalación y preparación del entorno.
- `CapituloXX/README.md`: guía de laboratorio por capítulo.

## Lista de laboratorios

### Capítulo 1

- [3. Práctica 1. Operación del software Cisco IOS](Capitulo01/README.md#3-práctica-1-operación-del-software-cisco-ios)
  - Descripción: En Cisco Packet Tracer, el participante accede a un dispositivo Cisco por consola, identifica y navega entre los modos EXEC de usuario, EXEC privilegiado y configuración global, utiliza la ayuda contextual y el historial de comandos, consulta información del dispositivo con comandos show y guarda la configuración en ejecución en la configuración de inicio.
  - Duración estimada: 60 min
  - [Ver capítulo completo](Capitulo01/README.md)

### Capítulo 2

- [2. Práctica 2. Exploración de la capa de enlace TCP/IP e inicialización de un switch](Capitulo02/README.md#2-práctica-2-exploración-de-la-capa-de-enlace-tcpip-e-inicialización-de-un-switch)
  - Descripción: En Cisco Packet Tracer, el participante inicia un switch Cisco, asigna nombre de host, dirección IP de administración y puerta de enlace predeterminada, revisa el estado, la velocidad y el modo dúplex de las interfaces, y genera tráfico entre hosts para observar cómo el switch aprende direcciones MAC y reenvía tramas Ethernet.
  - Duración estimada: 80 min
  - [Ver capítulo completo](Capitulo02/README.md)

### Capítulo 3

- [1. Práctica 3. Introducción a la capa de Internet de TCP/IP, el direccionamiento IPv4 y las subredes](Capitulo03/README.md#1-práctica-3-introducción-a-la-capa-de-internet-de-tcpip-el-direccionamiento-ipv4-y-las-subredes)
  - Descripción: En Cisco Packet Tracer, el participante calcula subredes IPv4 a partir de un bloque de direcciones dado, determina para cada subred la dirección de red, la máscara, el rango de hosts válidos y la dirección de difusión, asigna las direcciones a hosts e interfaces de la topología y verifica la configuración y la conectividad con ipconfig y ping.
  - Duración estimada: 40 min
- [2. Práctica 4. Explicación de las capas de transporte y aplicación de TCP/IP](Capitulo03/README.md#2-práctica-4-explicación-de-las-capas-de-transporte-y-aplicación-de-tcpip)
  - Descripción: En el modo de simulación de Cisco Packet Tracer, el participante genera tráfico de servicios como HTTP, DNS y DHCP entre clientes y servidores, observa el establecimiento de sesiones TCP y el intercambio UDP, e identifica los números de puerto, encabezados y el encapsulamiento de cada capa del modelo TCP/IP.
  - Duración estimada: 30 min
- [3. Práctica 5. Configuración de un router Cisco](Capitulo03/README.md#3-práctica-5-configuración-de-un-router-cisco)
  - Descripción: En Cisco Packet Tracer, el participante configura el nombre de host, las contraseñas básicas y las direcciones IPv4 de las interfaces de un router Cisco, habilita las interfaces, utiliza CDP para descubrir dispositivos vecinos y verifica la tabla de enrutamiento y la conectividad entre hosts de distintas subredes mediante ping.
  - Duración estimada: 35 min
- [4. Práctica 6. Introducción a IPv6 básico](Capitulo03/README.md#4-práctica-6-introducción-a-ipv6-básico)
  - Descripción: En Cisco Packet Tracer, el participante habilita el enrutamiento unicast IPv6 en un router, asigna direcciones IPv6 globales y de enlace local a las interfaces, configura los hosts mediante asignación estática o SLAAC y verifica la conectividad IPv6 con los comandos show ipv6 interface, show ipv6 route y ping.
  - Duración estimada: 30 min
  - [Ver capítulo completo](Capitulo03/README.md)

### Capítulo 4

- [1. Práctica 7. Implementación de VLAN, enlaces troncales y enrutamiento entre VLAN](Capitulo04/README.md#1-práctica-7-implementación-de-vlan-enlaces-troncales-y-enrutamiento-entre-vlan)
  - Descripción: En Cisco Packet Tracer, el participante crea VLAN en los switches, asigna puertos de acceso, configura enlaces troncales 802.1Q entre switches y habilita el enrutamiento entre VLAN mediante subinterfaces en un router (router-on-a-stick), verificando la segmentación y la conectividad entre hosts de distintas VLAN.
  - Duración estimada: 45 min
- [3. Práctica 8. Mejora de topologías conmutadas redundantes con EtherChannel](Capitulo04/README.md#3-práctica-8-mejora-de-topologías-conmutadas-redundantes-con-etherchannel)
  - Descripción: En Cisco Packet Tracer, el participante observa el efecto de STP sobre los enlaces redundantes entre switches, agrupa varios enlaces físicos en un EtherChannel mediante LACP, configura el canal como enlace troncal y verifica su estado y su tolerancia a fallas con los comandos show etherchannel summary y show spanning-tree.
  - Duración estimada: 35 min
  - [Ver capítulo completo](Capitulo04/README.md)

### Capítulo 5

- [3. Práctica 9. Resolución de problemas de una red sencilla](Capitulo05/README.md#3-práctica-9-resolución-de-problemas-de-una-red-sencilla)
  - Descripción: En Cisco Packet Tracer, el participante recibe una topología con fallas preconfiguradas, aplica un método de resolución de problemas por capas, utiliza ping, traceroute y comandos show para aislar problemas de cableado, interfaces, direccionamiento IP y puerta de enlace predeterminada, corrige la configuración y documenta la causa raíz de cada falla.
  - Duración estimada: 50 min
  - [Ver capítulo completo](Capitulo05/README.md)

### Capítulo 6

- [1. Práctica 10. Configuración del enrutamiento estático](Capitulo06/README.md#1-práctica-10-configuración-del-enrutamiento-estático)
  - Descripción: En Cisco Packet Tracer, el participante configura rutas estáticas IPv4 e IPv6 hacia redes remotas, una ruta predeterminada y una ruta estática flotante de respaldo, y verifica la tabla de enrutamiento, la selección de rutas y la conectividad extremo a extremo entre las distintas redes de la topología.
  - Duración estimada: 45 min
- [2. Práctica 11. Introducción a OSPF](Capitulo06/README.md#2-práctica-11-introducción-a-ospf)
  - Descripción: En Cisco Packet Tracer, el participante habilita OSPF de área única en varios routers, anuncia las redes conectadas, define el ID de router e interfaces pasivas, y verifica la formación de adyacencias, la base de datos de vecinos y las rutas aprendidas con los comandos show ip ospf neighbor, show ip ospf interface y show ip route.
  - Duración estimada: 55 min
  - [Ver capítulo completo](Capitulo06/README.md)

### Capítulo 7

- [1. Práctica 12. Explicación de los fundamentos de las ACL](Capitulo07/README.md#1-práctica-12-explicación-de-los-fundamentos-de-las-acl)
  - Descripción: En Cisco Packet Tracer, el participante crea ACL IPv4 estándar y extendidas, numeradas y nombradas, utilizando máscaras wildcard, las aplica en la interfaz y dirección adecuadas, restringe el acceso a las líneas VTY y comprueba el filtrado mediante pruebas de conectividad y los contadores del comando show access-lists.
  - Duración estimada: 70 min
  - [Ver capítulo completo](Capitulo07/README.md)

### Capítulo 8

- [1. Práctica 13. Protección del acceso administrativo e implementación del endurecimiento (hardening) de dispositivos](Capitulo08/README.md#1-práctica-13-protección-del-acceso-administrativo-e-implementación-del-endurecimiento-hardening-de-dispositivos)
  - Descripción: En Cisco Packet Tracer, el participante configura contraseñas cifradas y enable secret, cuentas de usuario locales, acceso remoto seguro mediante SSH, banners de inicio de sesión, port security en los puertos de acceso del switch y la deshabilitación de servicios y puertos no utilizados, verificando que solo el acceso autorizado sea permitido.
  - Duración estimada: 60 min
  - [Ver capítulo completo](Capitulo08/README.md)

### Capítulo 9

- [2. Práctica 14. Habilitación de la conectividad a Internet](Capitulo09/README.md#2-práctica-14-habilitación-de-la-conectividad-a-internet)
  - Descripción: En Cisco Packet Tracer, el participante configura la interfaz del router de borde hacia el proveedor de servicios, una ruta predeterminada, un servidor DHCP para los hosts internos y NAT estático y PAT para la traducción de direcciones privadas, verificando el acceso a servidores externos y las traducciones activas con show ip nat translations.
  - Duración estimada: 70 min
  - [Ver capítulo completo](Capitulo09/README.md)

## Flujo de colaboración

- Trabajar en `changes_course`.
- Crear Pull Request hacia `main`.
- Merge por `Squash and merge`.

---

*Material didáctico preparado por Global K, S.A. de C.V.*
