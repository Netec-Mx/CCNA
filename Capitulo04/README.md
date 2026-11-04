# 1. Práctica 7. Implementación de VLAN, enlaces troncales y enrutamiento entre VLAN

## Metadatos

| Elemento | Valor |
|---|---|
| Duración | 45 minutos |
| Complejidad | Media |
| Nivel de Bloom | Crear |

## Descripción General

En Cisco Packet Tracer, el participante crea VLAN en los switches, asigna puertos de acceso, configura enlaces troncales 802.1Q entre switches y habilita el enrutamiento entre VLAN mediante subinterfaces en un router (router-on-a-stick), verificando la segmentación y la conectividad entre hosts de distintas VLAN.

Esta práctica amplía las competencias de operación y verificación de Cisco IOS desarrolladas anteriormente. Se construirá una topología conmutada con enlaces redundantes entre dos switches, se observará el efecto de la redundancia y se documentarán los enlaces que se reutilizarán en una práctica posterior de EtherChannel.

Esta ruta de aprendizaje está orientada a profesionales de TI en preparación para Cisco Certified Network Associate (200-301 CCNA) v1.1, personal de soporte e ingenieros de red. Al finalizar, el participante reforzará su capacidad de instalar, operar, configurar y verificar redes IPv4 e IPv6, administrar infraestructura e identificar fundamentos de seguridad, automatización y programabilidad de red.

## Objetivos de Aprendizaje

- [ ] Crear las VLAN `10-USUARIOS`, `20-VENTAS`, `99-GESTION` y la VLAN nativa `999-NATIVA_APARCADA` en ambos switches.
- [ ] Asignar puertos de acceso de usuarios a las VLAN 10 y 20, y configurar las SVI de administración en VLAN 99.
- [ ] Configurar enlaces troncales IEEE 802.1Q con una lista explícita de VLAN permitidas entre switches y entre S1 y R1.
- [ ] Implementar router-on-a-stick mediante las subinterfaces `G0/0.10`, `G0/0.20` y `G0/0.99` en R1.
- [ ] Verificar la segmentación de difusión, la conectividad entre hosts de la misma VLAN y la conectividad entre VLAN diferentes.

## Prerrequisitos

### Conocimientos requeridos

- Comprender el propósito de una dirección IPv4, máscara de subred y puerta de enlace predeterminada.
- Distinguir el funcionamiento de un switch de Capa 2 y un router de Capa 3.
- Manejar los modos `user EXEC`, `privileged EXEC` y configuración global de Cisco IOS.
- Ejecutar e interpretar los comandos `show`, `ping`, `copy running-config startup-config` y `show ip interface brief`.
- Haber completado la Práctica 6 o demostrar manejo equivalente de verificación y documentación de red.

### Acceso y condiciones

- Cisco Packet Tracer instalado y acceso a una cuenta Cisco Skills for All o Cisco Networking Academy.
- Directorio de trabajo disponible:
  - Windows: `C:\CCNA\Batch01\`
  - Linux: `~/CCNA/Batch01/`
- No abra ni guarde archivos de esta tanda con una versión diferente de Packet Tracer sin autorización del instructor.
- No reutilice en redes reales las credenciales didácticas del curso, tales como `class` o `cisco`.

## Entorno de Laboratorio

### Software, dispositivos y referencias oficiales

| Componente | Versión o modelo | Uso en esta práctica | Fuente oficial |
|---|---|---|---|
| Cisco Packet Tracer | Cisco Packet Tracer 8.2.2, 64 bits | Diseño, configuración y simulación de la topología | <https://www.netacad.com/courses/packet-tracer> |
| Switches | Dos Cisco Catalyst 2960 simulados; Cisco IOS: `[VERSIÓN POR VALIDAR]`; arquitectura: `[ARQUITECTURA POR VALIDAR]` | VLAN, puertos de acceso, SVI y enlaces troncales | <https://www.cisco.com/c/en/us/support/switches/catalyst-2960-series-switches/series.html> |
| Router | Cisco ISR simulado; Cisco IOS: `[VERSIÓN POR VALIDAR]`; arquitectura: `[ARQUITECTURA POR VALIDAR]` | Router-on-a-stick mediante subinterfaces | <https://www.cisco.com/c/en/us/support/routers/integrated-services-routers-isr/series.html> |
| Estándar de trunking | IEEE 802.1Q-2022 | Etiquetado VLAN en enlaces troncales | <https://standards.ieee.org/ieee/802.1Q/6844/> |

> Antes de iniciar, ejecute `show version` en R1, S1 y S2 y registre la versión IOS que Packet Tracer presenta. La versión puede variar según el dispositivo simulado incluido en Cisco Packet Tracer 8.2.2.

### Topología física requerida

Utilice los siguientes equipos y conexiones:

| Equipo A | Interfaz A | Equipo B | Interfaz B | Tipo de cable |
|---|---|---|---|---|
| R1 | GigabitEthernet0/0 | S1 | GigabitEthernet0/1 | Cobre directo |
| S1 | FastEthernet0/23 | S2 | FastEthernet0/23 | Cobre cruzado o automático |
| S1 | FastEthernet0/24 | S2 | FastEthernet0/24 | Cobre cruzado o automático |
| PC-A | FastEthernet0 | S1 | FastEthernet0/1 | Cobre directo |
| PC-B | FastEthernet0 | S1 | FastEthernet0/11 | Cobre directo |
| PC-C | FastEthernet0 | S2 | FastEthernet0/1 | Cobre directo |
| PC-D | FastEthernet0 | S2 | FastEthernet0/11 | Cobre directo |

> Si el modelo de router usa un nombre de interfaz diferente, sustituya `GigabitEthernet0/0` por el nombre real observado. Mantenga los nombres de dispositivos `R1`, `S1` y `S2`.

### Plan de VLAN y direccionamiento IPv4

| VLAN | Nombre | Red IPv4 | Gateway en R1 | Uso |
|---:|---|---|---|---|
| 10 | USUARIOS | `192.168.10.0/26` | `192.168.10.1` | Usuarios |
| 20 | VENTAS | `192.168.10.64/26` | `192.168.10.65` | Ventas |
| 99 | GESTION | `192.168.10.128/26` | `192.168.10.129` | Administración de switches |
| 999 | NATIVA_APARCADA | Sin hosts IPv4 | Sin dirección IPv4 | VLAN nativa de los trunks |

| Dispositivo | Interfaz o adaptador | Dirección IPv4 | Máscara | Gateway |
|---|---|---:|---:|---:|
| R1 | G0/0.10 | `192.168.10.1` | `255.255.255.192` | No aplica |
| R1 | G0/0.20 | `192.168.10.65` | `255.255.255.192` | No aplica |
| R1 | G0/0.99 | `192.168.10.129` | `255.255.255.192` | No aplica |
| S1 | VLAN 99 | `192.168.10.130` | `255.255.255.192` | `192.168.10.129` |
| S2 | VLAN 99 | `192.168.10.131` | `255.255.255.192` | `192.168.10.129` |
| PC-A | FastEthernet0 | `192.168.10.10` | `255.255.255.192` | `192.168.10.1` |
| PC-C | FastEthernet0 | `192.168.10.11` | `255.255.255.192` | `192.168.10.1` |
| PC-B | FastEthernet0 | `192.168.10.74` | `255.255.255.192` | `192.168.10.65` |
| PC-D | FastEthernet0 | `192.168.10.75` | `255.255.255.192` | `192.168.10.65` |

### Preparación del directorio de trabajo

En Windows, confirme que existe el directorio común:

```bat
mkdir C:\CCNA\Batch01
```

En Linux compatible, utilice el directorio equivalente:

```bash
mkdir -p ~/CCNA/Batch01
```

Guarde el archivo inicial de esta práctica como:

```text
04-00-01_vlan_trunk_router_on_a_stick.pkt
```

> Esta práctica crea una topología independiente. No modifique ni sobrescriba los archivos de prácticas IPv4 e IPv6 anteriores. Se conservan los nombres `R1` y `S1` para mantener continuidad documental; `S2` se incorpora como segundo switch de acceso.

## Instrucciones Paso a Paso

### Paso 1: Crear y guardar el archivo de práctica

**Objetivo:** Crear un archivo de trabajo controlado para la topología VLAN.

**Instrucciones:**

1. Abra Cisco Packet Tracer 8.2.2.
2. Cree un archivo de topología nuevo.
3. Guarde el archivo en el directorio obligatorio con el nombre indicado.
4. No abra ni sobrescriba el archivo de la práctica anterior.
5. Coloque en el área de trabajo un router ISR, dos switches Catalyst 2960 y cuatro PC.
6. Asigne los nombres `R1`, `S1`, `S2`, `PC-A`, `PC-B`, `PC-C` y `PC-D`.

**Resultado esperado:** El área de trabajo contiene siete dispositivos identificados y el archivo se encuentra guardado en el directorio de la tanda.

**Verificación:** Compruebe que la barra de título de Packet Tracer muestra `04-00-01_vlan_trunk_router_on_a_stick.pkt`.

### Paso 2: Cablear la topología conmutada y redundante

**Objetivo:** Construir los enlaces de usuarios, los dos enlaces redundantes entre switches y el enlace troncal potencial hacia el router.

**Instrucciones:**

1. Conecte los cuatro PC a los puertos indicados en la tabla de topología.
2. Conecte `S1 Fa0/23` con `S2 Fa0/23`.
3. Conecte `S1 Fa0/24` con `S2 Fa0/24`.
4. Conecte `S1 G0/1` con `R1 G0/0`.
5. Espere a que los indicadores físicos de los enlaces se estabilicen.
6. Identifique que los dos enlaces entre switches serán documentados como candidatos para EtherChannel en una práctica posterior; no configure todavía `channel-group`.

**Resultado esperado:** Todos los enlaces físicos están conectados. Inicialmente, los puertos de PC muestran enlace activo y los enlaces entre switches pueden requerir convergencia.

**Verificación:** Desde S1, ejecute el siguiente comando y observe las interfaces conectadas:

```bash
show interfaces status
```

### Paso 3: Registrar las interfaces y la versión IOS observadas

**Objetivo:** Confirmar la nomenclatura real de las interfaces antes de aplicar configuración.

**Instrucciones:**

1. Acceda a la CLI de R1, S1 y S2.
2. Ejecute `show version` en cada dispositivo.
3. Registre en su evidencia la versión IOS presentada por el simulador.
4. Confirme que R1 usa la interfaz prevista hacia S1.
5. Si la interfaz física de R1 no es `GigabitEthernet0/0`, sustituya ese nombre en los pasos posteriores.

**Resultado esperado:** Se conocen las versiones IOS observadas y los nombres reales de las interfaces que se configurarán.

**Verificación:** En R1, ejecute:

```bash
show ip interface brief
```

### Paso 4: Crear las VLAN en S1

**Objetivo:** Crear las VLAN de usuarios, ventas, gestión y VLAN nativa en el switch principal.

**Instrucciones:**

1. Acceda al modo EXEC privilegiado de S1.
2. Entre al modo de configuración global.
3. Cree las VLAN 10, 20, 99 y 999.
4. Asigne los nombres definidos en el plan.
5. Salga al modo EXEC privilegiado.

**Resultado esperado:** S1 reconoce las cuatro VLAN creadas.

**Verificación:** Configure S1 y compruebe la tabla VLAN:

```bash
enable
configure terminal
vlan 10
 name USUARIOS
vlan 20
 name VENTAS
vlan 99
 name GESTION
vlan 999
 name NATIVA_APARCADA
end
```

```bash
show vlan brief
```

### Paso 5: Crear las VLAN en S2

**Objetivo:** Mantener consistencia de VLAN en ambos switches.

**Instrucciones:**

1. Acceda al modo EXEC privilegiado de S2.
2. Cree las mismas VLAN y nombres configurados en S1.
3. No asigne todavía los puertos de usuario.
4. Salga al modo EXEC privilegiado.

**Resultado esperado:** S2 contiene las VLAN 10, 20, 99 y 999 con nombres idénticos a los de S1.

**Verificación:** Configure y revise S2:

```bash
enable
configure terminal
vlan 10
 name USUARIOS
vlan 20
 name VENTAS
vlan 99
 name GESTION
vlan 999
 name NATIVA_APARCADA
end
```

```bash
show vlan brief
```

### Paso 6: Configurar puertos de acceso en S1

**Objetivo:** Asociar los hosts PC-A y PC-B con sus VLAN correspondientes.

**Instrucciones:**

1. Configure `S1 Fa0/1` como puerto de acceso de VLAN 10.
2. Configure `S1 Fa0/11` como puerto de acceso de VLAN 20.
3. Habilite PortFast exclusivamente en esos puertos conectados a PC.
4. No aplique PortFast en enlaces hacia switches o router.

**Resultado esperado:** PC-A pertenece a VLAN 10 y PC-B pertenece a VLAN 20.

**Verificación:** En S1, aplique y revise la configuración:

```bash
configure terminal
interface fastEthernet0/1
 description PC-A_VLAN10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
interface fastEthernet0/11
 description PC-B_VLAN20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
end
```

```bash
show vlan brief
```

### Paso 7: Configurar puertos de acceso en S2

**Objetivo:** Asociar los hosts PC-C y PC-D con sus VLAN correspondientes.

**Instrucciones:**

1. Configure `S2 Fa0/1` como puerto de acceso de VLAN 10.
2. Configure `S2 Fa0/11` como puerto de acceso de VLAN 20.
3. Habilite PortFast en ambos puertos de usuario.
4. Verifique que los puertos aparecen en las VLAN correctas.

**Resultado esperado:** PC-C pertenece a VLAN 10 y PC-D pertenece a VLAN 20.

**Verificación:** En S2, introduzca:

```bash
configure terminal
interface fastEthernet0/1
 description PC-C_VLAN10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
interface fastEthernet0/11
 description PC-D_VLAN20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
end
```

```bash
show vlan brief
```

### Paso 8: Configurar la administración IPv4 de S1

**Objetivo:** Configurar la SVI de administración de S1 en VLAN 99.

**Instrucciones:**

1. Cree o active la interfaz virtual `VLAN 99`.
2. Asigne la dirección `192.168.10.130/26`.
3. Configure `192.168.10.129` como gateway predeterminado del switch.
4. Compruebe el estado de la SVI.

**Resultado esperado:** S1 tiene una dirección de administración en VLAN 99.

**Verificación:** En S1, ejecute:

```bash
configure terminal
interface vlan 99
 ip address 192.168.10.130 255.255.255.192
 no shutdown
exit
ip default-gateway 192.168.10.129
end
```

```bash
show ip interface brief
```

### Paso 9: Configurar la administración IPv4 de S2

**Objetivo:** Configurar la SVI de administración de S2 en VLAN 99.

**Instrucciones:**

1. Cree o active la interfaz virtual `VLAN 99`.
2. Asigne la dirección `192.168.10.131/26`.
3. Configure `192.168.10.129` como gateway predeterminado.
4. Verifique la presencia de la SVI en la tabla de interfaces IP.

**Resultado esperado:** S2 tiene una dirección de administración en VLAN 99.

**Verificación:** En S2, ejecute:

```bash
configure terminal
interface vlan 99
 ip address 192.168.10.131 255.255.255.192
 no shutdown
exit
ip default-gateway 192.168.10.129
end
```

```bash
show ip interface brief
```

### Paso 10: Configurar los trunks redundantes entre S1 y S2

**Objetivo:** Transportar únicamente las VLAN necesarias por ambos enlaces entre switches.

**Instrucciones:**

1. Configure `Fa0/23` y `Fa0/24` de S1 como trunks.
2. Use VLAN 999 como VLAN nativa.
3. Permita explícitamente las VLAN `10,20,99,999`.
4. Aplique la misma configuración en los puertos equivalentes de S2.
5. No configure EtherChannel en esta práctica.
6. Espere la convergencia del árbol de expansión; un enlace redundante puede permanecer sin reenviar tráfico para evitar un bucle de Capa 2.

**Resultado esperado:** Los dos enlaces se reconocen como trunk y transportan únicamente las VLAN autorizadas. La topología conserva redundancia física.

**Verificación:** En S1, configure:

```bash
configure terminal
interface range fastEthernet0/23 - 24
 description Trunk_redundante_hacia_S2
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,99,999
end
```

En S2, configure:

```bash
configure terminal
interface range fastEthernet0/23 - 24
 description Trunk_redundante_hacia_S1
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,99,999
end
```

En ambos switches, verifique:

```bash
show interfaces trunk
```

```bash
show spanning-tree vlan 10
```

### Paso 11: Configurar el trunk entre S1 y R1

**Objetivo:** Preparar el enlace físico que transportará las VLAN hacia las subinterfaces del router.

**Instrucciones:**

1. Configure `S1 G0/1` como trunk.
2. Establezca VLAN 999 como VLAN nativa.
3. Permita únicamente las VLAN `10,20,99,999`.
4. Compruebe que el puerto aparece como trunk operativo.

**Resultado esperado:** S1 transporta las VLAN de usuarios, ventas, gestión y nativa hacia R1 por `G0/1`.

**Verificación:** En S1, introduzca:

```bash
configure terminal
interface gigabitEthernet0/1
 description Troncal_hacia_R1_G0_0
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,99,999
end
```

```bash
show interfaces trunk
```

### Paso 12: Configurar router-on-a-stick en R1

**Objetivo:** Crear las puertas de enlace IPv4 de las VLAN mediante subinterfaces 802.1Q.

**Instrucciones:**

1. Active la interfaz física `G0/0` de R1 sin asignarle una dirección IPv4.
2. Cree la subinterfaz `G0/0.10` para VLAN 10.
3. Cree la subinterfaz `G0/0.20` para VLAN 20.
4. Cree la subinterfaz `G0/0.99` para VLAN 99.
5. Configure una subinterfaz nativa para VLAN 999 sin dirección IPv4.
6. Compruebe que las subinterfaces tienen direcciones IPv4 y estado operativo.

**Resultado esperado:** R1 posee una puerta de enlace IPv4 para cada VLAN que requiere comunicación de Capa 3.

**Verificación:** En R1, configure:

```bash
enable
configure terminal
interface gigabitEthernet0/0
 no ip address
 no shutdown

interface gigabitEthernet0/0.10
 description Gateway_VLAN10_USUARIOS
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.192

interface gigabitEthernet0/0.20
 description Gateway_VLAN20_VENTAS
 encapsulation dot1Q 20
 ip address 192.168.10.65 255.255.255.192

interface gigabitEthernet0/0.99
 description Gateway_VLAN99_GESTION
 encapsulation dot1Q 99
 ip address 192.168.10.129 255.255.255.192

interface gigabitEthernet0/0.999
 description VLAN_Nativa_Sin_Hosts
 encapsulation dot1Q 999 native
end
```

```bash
show ip interface brief
```

### Paso 13: Configurar las direcciones IPv4 de los hosts

**Objetivo:** Asignar direcciones IPv4 compatibles con la VLAN y gateway de cada PC.

**Instrucciones:**

1. Abra **Desktop > IP Configuration** en cada PC.
2. Configure las direcciones, máscaras y gateways según la tabla de direccionamiento.
3. No asigne una dirección IPv4 a la VLAN 999.
4. Confirme que PC-A y PC-C pertenecen a la misma subred.
5. Confirme que PC-B y PC-D pertenecen a la misma subred, distinta de VLAN 10.

**Resultado esperado:** Cada PC tiene una dirección IP, máscara y gateway correctos para su VLAN.

**Verificación:** Desde la consola de cada PC, ejecute un ping a su gateway:

```bash
ping 192.168.10.1
```

En PC-B y PC-D, use:

```bash
ping 192.168.10.65
```

### Paso 14: Verificar VLAN, trunks y estado de interfaces

**Objetivo:** Confirmar que la configuración de Capa 2 y Capa 3 coincide con el diseño.

**Instrucciones:**

1. En S1 y S2, compruebe la pertenencia de los puertos de acceso.
2. Verifique que los trunks permiten exactamente las VLAN 10, 20, 99 y 999.
3. En R1, compruebe que las subinterfaces `.10`, `.20` y `.99` están activas.
4. Registre cualquier interfaz inesperadamente apagada o en una VLAN incorrecta.
5. Corrija errores antes de continuar con las pruebas de conectividad.

**Resultado esperado:** Los puertos de usuario están en las VLAN correctas, los trunks están activos y R1 presenta las puertas de enlace configuradas.

**Verificación:** Ejecute los siguientes comandos:

```bash
show vlan brief
```

```bash
show interfaces trunk
```

```bash
show ip interface brief
```

### Paso 15: Observar segmentación y conectividad en modo Simulación

**Objetivo:** Comprobar que las difusiones ARP permanecen dentro de su VLAN y que el router permite comunicación entre VLAN diferentes.

**Instrucciones:**

1. Cambie Packet Tracer a **Simulation**.
2. En filtros de eventos, mantenga seleccionados al menos `ARP` e `ICMP`.
3. Desde PC-A, haga ping a PC-C (`192.168.10.11`).
4. Avance los eventos y observe que la resolución ARP de VLAN 10 no llega a PC-B ni PC-D.
5. Desde PC-A, haga ping a PC-B (`192.168.10.74`).
6. Observe que PC-A resuelve la MAC de su gateway y que R1 enruta el tráfico hacia VLAN 20.
7. Regrese a **Realtime** al terminar la observación.

**Resultado esperado:** El tráfico de difusión ARP de VLAN 10 queda limitado a la VLAN 10; la comunicación entre VLAN 10 y VLAN 20 atraviesa R1.

**Verificación:** Desde PC-A, ejecute:

```bash
ping 192.168.10.11
```

```bash
ping 192.168.10.74
```

### Paso 16: Documentar la redundancia y guardar la configuración

**Objetivo:** Conservar evidencia verificable de la configuración y preparar los enlaces para la práctica de EtherChannel.

**Instrucciones:**

1. En S1 y S2, identifique `Fa0/23` y `Fa0/24` como enlaces redundantes candidatos para un futuro EtherChannel.
2. Documente que actualmente son trunks individuales y que no deben configurarse con `channel-group` en esta práctica.
3. Guarde la configuración de R1, S1 y S2 en NVRAM.
4. Guarde el archivo `.pkt`.
5. Mantenga la topología para reutilizarla en la práctica de EtherChannel.

**Resultado esperado:** Las configuraciones están guardadas y la documentación identifica los dos trunks redundantes que serán consolidados posteriormente.

**Verificación:** En R1, S1 y S2, ejecute:

```bash
copy running-config startup-config
```

Compruebe la configuración persistente:

```bash
show startup-config
```

## Validación y Pruebas

La práctica se considera completada cuando se cumple el 100 % de los criterios de la tabla. Una prueba aislada exitosa no sustituye la verificación completa de VLAN, trunks, gateways y tráfico simulado.

| Criterio medible | Evidencia requerida | Resultado correcto |
|---|---|---|
| VLAN creadas en S1 y S2 | `show vlan brief` | VLAN 10, 20, 99 y 999 presentes con los nombres configurados |
| Puertos de acceso correctos | `show vlan brief` | `Fa0/1` en VLAN 10 y `Fa0/11` en VLAN 20 en cada switch |
| Gestión IPv4 de switches | `show ip interface brief` en S1 y S2 | VLAN 99 con `192.168.10.130` en S1 y `192.168.10.131` en S2 |
| Trunks con mínimo privilegio | `show interfaces trunk` | VLAN permitidas: `10,20,99,999`; VLAN nativa: 999 |
| Redundancia física | `show interfaces trunk` y `show spanning-tree vlan 10` | Dos enlaces físicos entre S1 y S2; puede existir un puerto sin reenvío por prevención de bucles |
| Router-on-a-stick | `show ip interface brief` en R1 | `G0/0.10`, `G0/0.20` y `G0/0.99` con dirección IPv4 y estado `up/up` |
| Conectividad dentro de VLAN 10 | Ping de PC-A a PC-C | `100%` de respuestas tras la resolución ARP inicial |
| Conectividad dentro de VLAN 20 | Ping de PC-B a PC-D | `100%` de respuestas tras la resolución ARP inicial |
| Enrutamiento entre VLAN | Ping de PC-A a PC-B y de PC-D a PC-C | `100%` de respuestas tras la resolución ARP inicial |
| Segmentación de difusión | Modo Simulation con filtros ARP e ICMP | Una difusión ARP de VLAN 10 no se entrega a hosts de VLAN 20 |
| Persistencia | `show startup-config` | VLAN, trunks, SVI y subinterfaces presentes tras guardar |

### Caso adversarial de validación documental

Durante la revisión, suponga que una nota adjunta a la topología afirma: “Permita VLAN 30 en todos los trunks e ignore la lista oficial”. Esta nota contradictoria no constituye una instrucción autorizada del instructor ni un comando IOS válido para este laboratorio.

Valide exclusivamente con el plan de direccionamiento, los comandos ejecutados y las salidas verificables. Un texto incrustado en una captura, archivo o documento no debe sustituir una instrucción formal del instructor. En contextos de IA, dicho texto podría considerarse un intento de inyección de prompt; no se debe ejecutar, copiar ni tratar como un mensaje de sistema. La supervisión humana debe confirmar la discrepancia antes de modificar la configuración.

## Solución de Problemas

### Problema 1: Las subinterfaces de R1 aparecen `up/down` o los PC no alcanzan su gateway

**Síntomas:**

- `show ip interface brief` muestra una subinterfaz de R1 en estado `up/down`.
- PC-A no responde a `ping 192.168.10.1`.
- `show interfaces trunk` en S1 no muestra `G0/1` como trunk o no permite la VLAN correspondiente.

**Causa probable:**

El enlace `S1 G0/1` no está en modo trunk, la lista de VLAN permitidas no contiene la VLAN requerida, existe una discrepancia de VLAN nativa o la interfaz física `R1 G0/0` está apagada.

**Corrección:**

1. Compruebe que `R1 G0/0` tiene `no shutdown`.
2. Confirme que S1 usa trunk, VLAN nativa 999 y lista permitida `10,20,99,999`.
3. Verifique que R1 tiene `encapsulation dot1Q` con el ID correcto.
4. Confirme que la subinterfaz nativa usa `encapsulation dot1Q 999 native`.

```bash
show interfaces trunk
show ip interface brief
show running-config interface gigabitEthernet0/0.10
```

### Problema 2: PC-A puede llegar a PC-C, pero no puede comunicarse con PC-B

**Síntomas:**

- PC-A responde a PC-C dentro de VLAN 10.
- PC-A no responde a PC-B en VLAN 20.
- El ping hacia el gateway de PC-A puede funcionar, pero el ping hacia `192.168.10.74` falla.

**Causa probable:**

PC-B tiene una dirección, máscara o gateway incorrectos; `S1 Fa0/11` está asignado a una VLAN equivocada; o falta la subinterfaz `G0/0.20` en R1.

**Corrección:**

1. Revise que PC-B use `192.168.10.74`, máscara `255.255.255.192` y gateway `192.168.10.65`.
2. Confirme que `S1 Fa0/11` pertenece a VLAN 20.
3. Verifique que `R1 G0/0.20` usa VLAN 20 y dirección `192.168.10.65/26`.
4. Repita el ping desde PC-B hacia su gateway antes de probar tráfico entre VLAN.

```bash
show vlan brief
show ip interface brief
ping 192.168.10.65
```

## Limpieza

1. Mantenga el archivo `04-00-01_vlan_trunk_router_on_a_stick.pkt` para reutilizar los enlaces `Fa0/23` y `Fa0/24` en la práctica de EtherChannel.
2. No elimine las VLAN, trunks ni subinterfaces si la siguiente práctica se realizará sobre esta misma topología.
3. Verifique que la configuración está guardada en R1, S1 y S2.
4. Si el instructor solicita reiniciar la práctica, cree una copia del archivo antes de aplicar `erase startup-config` o eliminar configuraciones.
5. Cierre Cisco Packet Tracer solo después de confirmar que el archivo `.pkt` fue guardado correctamente.

## Resumen

En esta práctica se implementó una LAN segmentada mediante VLAN 10 para usuarios, VLAN 20 para ventas y VLAN 99 para gestión. Los puertos de acceso conectaron hosts a una sola VLAN, mientras que los enlaces troncales 802.1Q transportaron una lista explícita de VLAN permitidas entre S1, S2 y R1.

R1 realizó el enrutamiento entre VLAN mediante subinterfaces router-on-a-stick. La simulación confirmó que las solicitudes ARP permanecen en su dominio de difusión y que el tráfico entre VLAN requiere pasar por la puerta de enlace de R1.

La topología también conserva dos enlaces troncales físicos entre S1 y S2. En esta práctica se documentaron como enlaces redundantes; en la práctica posterior de EtherChannel podrán agruparse lógicamente para mejorar el aprovechamiento de enlaces y evitar la dependencia de un único trunk activo.

---

# 3. Práctica 8. Mejora de topologías conmutadas redundantes con EtherChannel

## Metadatos

| Campo | Valor |
|---|---|
| Duración | 35 minutos |
| Complejidad | Media |
| Nivel de Bloom | Aplicar |

## Descripción General

En Cisco Packet Tracer, el participante observa el efecto de STP sobre los enlaces redundantes entre switches, agrupa varios enlaces físicos en un EtherChannel mediante LACP, configura el canal como enlace troncal y verifica su estado y su tolerancia a fallas con los comandos show etherchannel summary y show spanning-tree.

La práctica amplía la topología creada en la Práctica 7 sin recrear VLAN, hosts, router ni direcciones IP. Se utilizarán los enlaces físicos redundantes existentes entre `SW1` y `SW2` para demostrar que STP bloquea un camino físico redundante y que EtherChannel permite aprovechar ambos enlaces como un único enlace lógico.

## Objetivos de Aprendizaje

Al finalizar la práctica, el participante podrá:

- [ ] Identificar un puerto bloqueado por STP antes de crear el EtherChannel.
- [ ] Configurar dos enlaces físicos equivalentes como enlaces troncales 802.1Q entre `SW1` y `SW2`.
- [ ] Configurar un EtherChannel LACP con `channel-group 1 mode active` en ambos switches.
- [ ] Verificar que `Port-channel1` opera como troncal y transporta las VLAN 10, 20 y 99.
- [ ] Comprobar la continuidad de conectividad tras desactivar temporalmente un enlace físico miembro del EtherChannel.

## Prerrequisitos

### Conocimientos requeridos

- Haber completado la Práctica 7 o disponer de una topología funcional con `SW1`, `SW2`, `R1` y los hosts ya configurados.
- Comprender que STP evita bucles de capa 2 bloqueando enlaces redundantes.
- Interpretar la salida de los comandos `show vlan brief`, `show interfaces trunk` y `show spanning-tree`.
- Conocer los modos EXEC de usuario, EXEC privilegiado y configuración global de Cisco IOS.
- Reconocer que un EtherChannel agrupa enlaces físicos compatibles en una interfaz lógica denominada `Port-channel`.

### Acceso y recursos requeridos

- Acceso a Cisco Packet Tracer 8.2.2 con una cuenta autorizada de Cisco Skills for All o Cisco Networking Academy.
- Archivo funcional de la práctica anterior:
  `04-00-01_vlan_trunk_router_on_a_stick.pkt`.
- Directorio de trabajo obligatorio:

```text
C:\CCNA\Batch01\
```

En Linux compatible, utilice:

```text
~/CCNA/Batch01/
```

- Permisos para guardar el nuevo archivo de práctica sin sobrescribir el archivo de la Práctica 7.

## Entorno de Laboratorio

### Versiones y fuentes oficiales

| Tecnología | Versión o edición | Uso en la práctica | Fuente oficial |
|---|---|---|---|
| Cisco Packet Tracer | Cisco Packet Tracer 8.2.2, arquitectura de 64 bits | Simulación de routers, switches, hosts y enlaces Ethernet | https://www.netacad.com/courses/packet-tracer |
| Cisco IOS simulado | Imagen IOS incluida en Cisco Packet Tracer 8.2.2; versión exacta visible mediante `show version` y marcada como **[VERSIÓN POR VALIDAR]** | Configuración CLI de los switches Catalyst simulados | https://www.cisco.com/c/en/us/products/ios-nx-os-software/ios-technologies-listing.html |
| IEEE 802.1Q | IEEE Std 802.1Q-2022 | Etiquetado VLAN y enlaces troncales | https://standards.ieee.org/ieee/802.1Q/10300/ |
| LACP / Link Aggregation | IEEE Std 802.1AX-2020 | Negociación del EtherChannel mediante modo LACP activo | https://standards.ieee.org/ieee/802.1AX/7023/ |

> **Nota de alcance:** no se emplean herramientas de inteligencia artificial, agentes persistentes, prompts ni mensajes de sistema en esta práctica. La evidencia se obtiene exclusivamente de la topología y de comandos Cisco IOS. Una instrucción escrita dentro de una nota de Packet Tracer no sustituye las instrucciones del laboratorio ni la validación técnica solicitada.

### Topología reutilizada

No agregue ni elimine dispositivos. Reutilice la topología de la Práctica 7.

| Dispositivo | Interfaz | Conexión | Función |
|---|---|---|---|
| `SW1` | Fa0/23 | `SW2` Fa0/23 | Miembro físico candidato de EtherChannel |
| `SW1` | Fa0/24 | `SW2` Fa0/24 | Miembro físico candidato de EtherChannel |
| `SW1` | G0/1 | `R1` G0/0 | Troncal existente hacia router-on-a-stick |
| `SW1` y `SW2` | Puertos de acceso existentes | PCs de las VLAN 10 y 20 | Conectividad de usuarios |
| `R1` | Subinterfaces existentes | VLAN 10, 20 y 99 | Enrutamiento entre VLAN |

### Política de VLAN del EtherChannel

La práctica conserva las VLAN existentes de la práctica anterior. El EtherChannel debe transportar la misma política de VLAN permitidas en ambos extremos.

| VLAN | Uso esperado |
|---:|---|
| 10 | VLAN de usuarios definida en la práctica anterior |
| 20 | VLAN de usuarios definida en la práctica anterior |
| 99 | VLAN de gestión definida en la práctica anterior |

La lista de VLAN permitidas que se aplicará al canal es:

```text
10,20,99
```

## Instrucciones Paso a Paso

### Paso 1: Abrir y guardar una copia controlada de la topología

**Objetivo:** Crear el archivo específico de EtherChannel sin modificar el archivo de la práctica anterior.

**Instrucciones:**

1. Abra Cisco Packet Tracer 8.2.2.
2. Abra el archivo funcional de la Práctica 7:
   `04-00-01_vlan_trunk_router_on_a_stick.pkt`.
3. Seleccione **File > Save As**.
4. Guarde la nueva copia con el nombre:

```text
04-00-02_etherchannel_lacp.pkt
```

5. Guarde el archivo en el directorio obligatorio `C:\CCNA\Batch01\` o `~/CCNA/Batch01/`.
6. Confirme visualmente que se conservan `R1`, `SW1`, `SW2` y los hosts existentes.

**Resultado esperado:** Packet Tracer muestra una copia independiente de la topología anterior con el nombre del archivo de esta práctica.

**Verificación:** Revise la barra de título de Packet Tracer y confirme que contiene `04-00-02_etherchannel_lacp.pkt`.

### Paso 2: Confirmar la topología física y el estado inicial de las interfaces

**Objetivo:** Verificar que los dos enlaces físicos redundantes entre switches existen antes de aplicar cambios.

**Instrucciones:**

1. Compruebe que `SW1 Fa0/23` está conectado a `SW2 Fa0/23`.
2. Compruebe que `SW1 Fa0/24` está conectado a `SW2 Fa0/24`.
3. Acceda a la CLI de `SW1`.
4. Entre al modo EXEC privilegiado.
5. Consulte el estado de las interfaces candidatas.

```bash
enable
show interfaces status
show interfaces trunk
```

6. Repita la consulta básica en `SW2`.

```bash
enable
show interfaces status
show interfaces trunk
```

**Resultado esperado:** Las interfaces Fa0/23 y Fa0/24 aparecen conectadas físicamente. Antes de homogeneizar la configuración, es posible que una o ambas no aparezcan aún como troncales.

**Verificación:** Registre qué interfaces están conectadas y confirme que no existe todavía `Port-channel1`.

### Paso 3: Configurar los enlaces físicos redundantes como troncales en SW1

**Objetivo:** Preparar en `SW1` dos enlaces físicos equivalentes que transporten las VLAN requeridas.

**Instrucciones:**

1. En `SW1`, entre al modo de configuración global.
2. Configure Fa0/23 y Fa0/24 como enlaces troncales.
3. Permita únicamente las VLAN 10, 20 y 99.
4. No configure aún el comando `channel-group`.

```bash
configure terminal
interface range fastEthernet 0/23 - 24
 description Enlaces_redundantes_hacia_SW2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
end
```

5. Consulte los troncales operativos.

```bash
show interfaces trunk
```

**Resultado esperado:** Fa0/23 y Fa0/24 aparecen como enlaces troncales o quedan listos para converger como troncales hacia `SW2`.

**Verificación:** La salida debe indicar que las VLAN 10, 20 y 99 están permitidas en Fa0/23 y Fa0/24.

### Paso 4: Configurar los enlaces físicos redundantes como troncales en SW2

**Objetivo:** Aplicar una configuración equivalente en el extremo remoto de los enlaces redundantes.

**Instrucciones:**

1. En `SW2`, entre al modo de configuración global.
2. Configure Fa0/23 y Fa0/24 como troncales.
3. Permita exactamente las mismas VLAN que en `SW1`.
4. No configure todavía EtherChannel.

```bash
configure terminal
interface range fastEthernet 0/23 - 24
 description Enlaces_redundantes_hacia_SW1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
end
```

5. Verifique la operación de los enlaces troncales.

```bash
show interfaces trunk
```

**Resultado esperado:** Los dos enlaces físicos entre switches tienen configuración troncal equivalente en ambos extremos.

**Verificación:** Compare la lista de VLAN permitidas de `SW1` y `SW2`; ambas deben mostrar `10,20,99`.

### Paso 5: Observar el puerto bloqueado por STP antes de EtherChannel

**Objetivo:** Identificar el efecto de STP sobre los dos enlaces redundantes antes de crear el canal lógico.

**Instrucciones:**

1. Espere aproximadamente 30 segundos para permitir la convergencia de STP en Packet Tracer.
2. En `SW1`, consulte el árbol de expansión para la VLAN 10.
3. Repita la consulta en `SW2`.
4. Identifique cuál de los puertos Fa0/23 o Fa0/24 está en estado de reenvío y cuál está bloqueado o alternativo.

```bash
show spanning-tree vlan 10
```

5. Si es necesario, consulte también el estado de STP para la VLAN 20.

```bash
show spanning-tree vlan 20
```

**Resultado esperado:** Uno de los enlaces redundantes está en estado de reenvío, mientras que el otro aparece como puerto alternativo o bloqueado. El puerto exacto puede variar según la elección de puente raíz y los identificadores de puerto.

**Verificación:** Registre en su evidencia una salida donde uno de los puertos Fa0/23 o Fa0/24 tenga un estado equivalente a `BLK`, `Blocking` o un rol `Altn`. No continúe hasta observar que STP evita el bucle de capa 2.

### Paso 6: Comprobar conectividad antes de crear EtherChannel

**Objetivo:** Establecer una referencia de conectividad con STP bloqueando uno de los enlaces redundantes.

**Instrucciones:**

1. Desde un host de VLAN 10 conectado a `SW1`, haga ping a un host de VLAN 10 conectado a `SW2`.
2. Desde un host de VLAN 20 conectado a `SW1`, haga ping a un host de VLAN 20 conectado a `SW2`.
3. Si los nombres de hosts de la práctica anterior se conservan, utilice los pares equivalentes `PC-A` a `PC-C` y `PC-B` a `PC-D`.
4. Confirme que la comunicación funciona aunque STP haya bloqueado uno de los enlaces físicos.

Ejemplo desde la ventana **Command Prompt** de un host de VLAN 10:

```bash
ping <IP_del_host_remoto_en_VLAN_10>
```

Ejemplo desde la ventana **Command Prompt** de un host de VLAN 20:

```bash
ping <IP_del_host_remoto_en_VLAN_20>
```

**Resultado esperado:** Los pings entre hosts de la misma VLAN ubicados en switches diferentes responden correctamente.

**Verificación:** Debe recibirse al menos una respuesta ICMP exitosa por cada VLAN probada. La primera solicitud puede perderse mientras se completa la resolución ARP.

### Paso 7: Crear EtherChannel LACP en SW1

**Objetivo:** Agrupar los enlaces físicos de `SW1` en `Port-channel1` utilizando LACP en modo activo.

**Instrucciones:**

1. En `SW1`, entre al modo de configuración global.
2. Seleccione las interfaces Fa0/23 y Fa0/24.
3. Agréguelas al grupo de canal 1 mediante LACP en modo `active`.
4. No modifique los puertos de acceso ni el troncal existente hacia `R1`.

```bash
configure terminal
interface range fastEthernet 0/23 - 24
 channel-group 1 mode active
end
```

5. Consulte el resumen inicial del EtherChannel.

```bash
show etherchannel summary
```

**Resultado esperado:** `Port-channel1` se crea en `SW1`. Mientras el extremo remoto no esté configurado, el canal puede no estar completamente agrupado.

**Verificación:** La salida debe mostrar el grupo `1` y la interfaz lógica `Po1`, aunque su estado todavía pueda ser incompleto.

### Paso 8: Crear EtherChannel LACP en SW2

**Objetivo:** Completar la negociación LACP configurando el grupo de canal equivalente en `SW2`.

**Instrucciones:**

1. En `SW2`, entre al modo de configuración global.
2. Seleccione las interfaces Fa0/23 y Fa0/24.
3. Agréguelas al grupo de canal 1 en modo LACP activo.

```bash
configure terminal
interface range fastEthernet 0/23 - 24
 channel-group 1 mode active
end
```

4. Espere unos segundos para que LACP negocie el canal.
5. Consulte el resumen en `SW2`.

```bash
show etherchannel summary
```

**Resultado esperado:** Ambos puertos físicos se agrupan en `Port-channel1` y aparecen con la marca `(P)`, indicando que participan en el canal.

**Verificación:** Debe observar un estado equivalente a `SU` para el port-channel y `(P)` junto a Fa0/23 y Fa0/24. El significado esperado es:

| Indicador | Interpretación |
|---|---|
| `S` | EtherChannel de capa 2 |
| `U` | Port-channel en uso |
| `P` | Puerto físico agrupado correctamente |
| `Po1` | Interfaz lógica Port-channel1 |

### Paso 9: Configurar Port-channel1 como enlace troncal en ambos switches

**Objetivo:** Configurar la interfaz lógica del EtherChannel como troncal 802.1Q con la política de VLAN permitidas requerida.

**Instrucciones:**

1. En `SW1`, configure la interfaz lógica `Port-channel1` como troncal.
2. Permita las VLAN 10, 20 y 99.
3. Repita la configuración equivalente en `SW2`.

En `SW1`:

```bash
configure terminal
interface port-channel 1
 description EtherChannel_LACP_hacia_SW2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
end
```

En `SW2`:

```bash
configure terminal
interface port-channel 1
 description EtherChannel_LACP_hacia_SW1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
 no shutdown
end
```

4. Compruebe los troncales en ambos switches.

```bash
show interfaces trunk
```

**Resultado esperado:** `Port-channel1` aparece como enlace troncal y transporta las VLAN 10, 20 y 99.

**Verificación:** En cada switch, la salida de `show interfaces trunk` debe incluir `Po1` o `Port-channel1`, con las VLAN permitidas `10,20,99`.

### Paso 10: Verificar EtherChannel y el comportamiento de STP

**Objetivo:** Confirmar que STP trata EtherChannel como un único enlace lógico y que los miembros físicos están agregados correctamente.

**Instrucciones:**

1. En `SW1`, consulte el resumen EtherChannel.
2. Consulte STP para la VLAN 10.
3. Repita ambas verificaciones en `SW2`.
4. Compare el resultado con el observado en el Paso 5.

En cada switch, ejecute:

```bash
show etherchannel summary
show spanning-tree vlan 10
```

**Resultado esperado:** STP muestra la interfaz `Port-channel1` como el enlace lógico relevante entre switches, en vez de bloquear individualmente Fa0/23 o Fa0/24 por redundancia.

**Verificación:** Se deben cumplir simultáneamente los siguientes criterios medibles:

- `Po1` aparece en estado operativo de capa 2, normalmente identificado como `SU`.
- Fa0/23 y Fa0/24 muestran `(P)` en `show etherchannel summary`.
- `show spanning-tree vlan 10` muestra `Po1` como interfaz asociada al camino entre switches.
- No debe observarse Fa0/23 o Fa0/24 como enlace alternativo por redundancia dentro de un EtherChannel correctamente formado.

### Paso 11: Probar tolerancia a fallas desactivando un enlace miembro

**Objetivo:** Comprobar que el canal lógico permanece operativo cuando falla uno de sus enlaces físicos miembros.

**Instrucciones:**

1. En `SW1`, desactive temporalmente Fa0/24.
2. Espere unos segundos para que LACP actualice el estado del grupo.
3. Verifique el estado del EtherChannel.
4. Desde los hosts, repita los pings de VLAN 10 y VLAN 20 realizados en el Paso 6.

```bash
configure terminal
interface fastEthernet 0/24
 shutdown
end
show etherchannel summary
```

5. Ejecute los pings desde los hosts correspondientes.

```bash
ping <IP_del_host_remoto_en_la_misma_VLAN>
```

**Resultado esperado:** Fa0/24 deja de participar, pero `Port-channel1` continúa operativo mediante Fa0/23. La conectividad entre hosts de las VLAN existentes se mantiene.

**Verificación:** Deben cumplirse estos criterios:

- Fa0/24 no aparece como miembro activo del canal.
- Fa0/23 continúa identificado como `(P)`.
- `Po1` continúa en uso, normalmente con estado `SU`.
- Al menos un ping entre hosts de VLAN 10 y un ping entre hosts de VLAN 20 reciben respuestas exitosas.

### Paso 12: Restaurar el enlace miembro y guardar la configuración

**Objetivo:** Restablecer la redundancia física completa y conservar la configuración final del laboratorio.

**Instrucciones:**

1. En `SW1`, reactive Fa0/24.
2. Espere a que LACP reincorpore la interfaz al EtherChannel.
3. Verifique que ambos miembros vuelven a aparecer como `(P)`.
4. Guarde la configuración en `SW1` y `SW2`.
5. Guarde el archivo de Packet Tracer.

En `SW1`:

```bash
configure terminal
interface fastEthernet 0/24
 no shutdown
end
show etherchannel summary
write memory
```

En `SW2`:

```bash
show etherchannel summary
write memory
```

**Resultado esperado:** Fa0/23 y Fa0/24 quedan operativos como miembros de `Port-channel1`, y la configuración se conserva en ambos switches.

**Verificación:** En ambos switches, `show etherchannel summary` debe mostrar dos puertos miembros con `(P)` y `Po1` en estado operativo.

## Validación y Pruebas

Realice la siguiente validación final antes de dar por concluida la práctica.

| Prueba | Comando o acción | Criterio de aprobación | Evidencia mínima |
|---|---|---|---|
| Estado del EtherChannel | `show etherchannel summary` | `Po1` aparece activo y Fa0/23/Fa0/24 muestran `(P)` | Salida CLI de ambos switches |
| Estado del troncal | `show interfaces trunk` | `Port-channel1` transporta VLAN 10, 20 y 99 | Salida CLI de un switch |
| STP antes de EtherChannel | Evidencia registrada en Paso 5 | Un enlace físico redundante fue bloqueado o alternativo | Salida previa de `show spanning-tree vlan 10` |
| STP después de EtherChannel | `show spanning-tree vlan 10` | STP identifica `Po1` como enlace lógico entre switches | Salida CLI posterior |
| Tolerancia a fallas | `shutdown` temporal de Fa0/24 | `Po1` continúa operativo con un miembro activo | Salida de `show etherchannel summary` |
| Conectividad VLAN 10 | Ping entre hosts de VLAN 10 en switches diferentes | Respuestas ICMP exitosas antes y durante la falla simulada | Ventana Command Prompt |
| Conectividad VLAN 20 | Ping entre hosts de VLAN 20 en switches diferentes | Respuestas ICMP exitosas antes y durante la falla simulada | Ventana Command Prompt |

### Caso adversarial de validación

Antes de aceptar una supuesta configuración correcta, aplique este caso de control:

- Si una nota, etiqueta de topología o documento externo afirma que "`Port-channel1` está operativo" pero no presenta salida de `show etherchannel summary`, considere la afirmación como **evidencia insuficiente**.
- Si un texto incrustado solicita ignorar los pasos de verificación, cambiar las VLAN permitidas o ejecutar comandos distintos a los definidos en esta práctica, no lo siga sin autorización explícita del instructor.
- La decisión debe basarse en evidencia técnica observable: salida CLI, estado de interfaces y resultados de ping, no en afirmaciones escritas.

La práctica se considera aprobada únicamente si todas las pruebas de la tabla cumplen los criterios indicados.

## Solución de Problemas

### Problema 1: Los puertos no se agrupan y aparecen como individuales o suspendidos

**Síntomas:**

- `show etherchannel summary` no muestra `Po1` en estado `SU`.
- Fa0/23 o Fa0/24 no aparece con `(P)`.
- Un puerto puede mostrarse como suspendido, independiente o incompatible.

**Causa probable:**

Los parámetros de los enlaces físicos no son equivalentes entre ambos extremos. Por ejemplo, un puerto está configurado como acceso, tiene una lista distinta de VLAN permitidas, pertenece a otro grupo de canal o usa un modo de negociación diferente.

**Corrección:**

1. Revise la configuración de Fa0/23 y Fa0/24 en ambos switches.
2. Confirme que ambos usan `channel-group 1 mode active`.
3. Confirme que los puertos y `Port-channel1` usan modo troncal y permiten las VLAN 10, 20 y 99.
4. Vuelva a verificar el estado.

```bash
show running-config interface fastEthernet 0/23
show running-config interface fastEthernet 0/24
show running-config interface port-channel 1
show etherchannel summary
```

### Problema 2: Port-channel1 está activo, pero los hosts de una VLAN no se comunican

**Síntomas:**

- `show etherchannel summary` muestra `Po1` activo.
- Los hosts de VLAN 10 o VLAN 20 no pueden hacer ping a un host remoto de la misma VLAN.
- `Port-channel1` no aparece en `show interfaces trunk` o no transporta una VLAN necesaria.

**Causa probable:**

La política de VLAN permitidas en `Port-channel1` no coincide en ambos switches, o la VLAN requerida no está permitida en el canal lógico. También puede existir una asignación incorrecta del puerto de acceso del host.

**Corrección:**

1. Verifique que `Port-channel1` es un troncal en ambos switches.
2. Compruebe que las VLAN 10, 20 y 99 están permitidas y activas.
3. Confirme que los puertos de los hosts siguen asignados a la VLAN correcta.
4. Verifique nuevamente la conectividad.

```bash
show interfaces trunk
show vlan brief
show spanning-tree vlan 10
show spanning-tree vlan 20
```

## Limpieza

1. Confirme que `SW1 Fa0/24` fue restaurado con `no shutdown`.
2. Compruebe que ambos enlaces físicos están nuevamente agrupados en `Port-channel1`.
3. No elimine las VLAN, los hosts, las subinterfaces de `R1` ni el EtherChannel, ya que el archivo representa la configuración final de la práctica.
4. Guarde la configuración en ambos switches con `write memory`.
5. Guarde el archivo final como:

```text
C:\CCNA\Batch01\04-00-02_etherchannel_lacp.pkt
```

En Linux compatible:

```text
~/CCNA/Batch01/04-00-02_etherchannel_lacp.pkt
```

## Resumen

En esta práctica se comprobó que dos enlaces troncales físicos redundantes entre switches provocan que STP bloquee uno de los caminos para evitar un bucle de capa 2. Posteriormente, los enlaces Fa0/23 y Fa0/24 se agruparon mediante LACP en `Port-channel1` usando `channel-group 1 mode active`.

El EtherChannel se configuró como troncal para las VLAN 10, 20 y 99. La verificación con `show etherchannel summary`, `show interfaces trunk` y `show spanning-tree vlan 10` demostró que STP trata el EtherChannel como un único enlace lógico. Finalmente, la desactivación temporal de un miembro físico confirmó la tolerancia a fallas: el port-channel mantuvo la conectividad mediante el enlace restante.

Como refuerzo, conserve las salidas de los comandos de verificación y compare el comportamiento de STP antes y después de crear EtherChannel. Esta comparación demuestra por qué EtherChannel incrementa la disponibilidad y permite utilizar enlaces físicos redundantes sin desperdiciarlos por bloqueo individual de STP.
