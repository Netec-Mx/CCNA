# 2. Práctica 14. Habilitación de la conectividad a Internet

## Metadatos

| Campo | Valor |
|---|---|
| Duración | 70 minutos |
| Complejidad | Difícil |
| Nivel de Bloom | Crear |

## Descripción General

En Cisco Packet Tracer, el participante configura la interfaz del router de borde hacia el proveedor de servicios, una ruta predeterminada, un servidor DHCP para los hosts internos y NAT estático y PAT para la traducción de direcciones privadas, verificando el acceso a servidores externos y las traducciones activas con show ip nat translations.

La práctica amplía la red endurecida de la práctica 13, conserva la configuración de seguridad existente y convierte a `R1-CORE` en el router de borde de la organización. Se simula una conexión WAN Ethernet hacia un ISP, una red pública externa y un servidor interno publicado mediante NAT estático.

## Objetivos de Aprendizaje

Al finalizar la práctica, el participante podrá:

- [ ] Configurar un enlace WAN Ethernet simulado entre `R1-CORE` y un router ISP, incluyendo una ruta predeterminada IPv4.
- [ ] Implementar un servicio DHCP en Cisco IOS para asignar parámetros IPv4 dinámicos a usuarios de LAN-A.
- [ ] Configurar NAT estático para publicar un servidor interno y PAT con sobrecarga para las subredes internas autorizadas.
- [ ] Verificar rutas, arrendamientos DHCP, conectividad HTTP y traducciones NAT mediante comandos `show`.
- [ ] Relacionar la conexión Ethernet simulada con conceptos WAN básicos, demarcación, CPE, ISP y consideraciones iniciales de QoS.

## Prerrequisitos

### Conocimientos requeridos

- Haber completado la práctica 13 o disponer del archivo `08-00-01_hardening_base.pkt`.
- Comprender direcciones IPv4 privadas, públicas, gateway predeterminado y rutas por defecto.
- Distinguir entre NAT estático y PAT/NAT overload.
- Conocer la configuración básica de interfaces Cisco IOS y comandos `show`.
- Comprender que `R1-CORE` funciona como CPE del cliente y que el router `ISP` representa el límite lógico del proveedor.
- Reconocer que una WAN real puede tener latencia, pérdida, SLA y políticas QoS; esta práctica usa Ethernet simulado y no reproduce garantías reales de un proveedor.

### Acceso y archivos requeridos

- Cisco Packet Tracer Desktop 8.2.2, arquitectura de 64 bits, usado exclusivamente para esta tanda.  
  Fuente oficial: <https://www.netacad.com/courses/packet-tracer>
- Cisco IOS simulado incluido en Cisco Packet Tracer 8.2.2. La versión exacta de IOS mostrada por cada router debe registrarse con `show version` como **[VERSIÓN POR VALIDAR]**, pues depende de la imagen integrada en el archivo `.pkt`.  
  Referencia oficial de comandos IOS: <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/command/ina-cr-book.html>
- Archivo base: `08-00-01_hardening_base.pkt`.
- Directorio obligatorio:
  - Windows: `C:\CCNA\Batch01\`
  - Linux compatible: `~/CCNA/Batch01/`

> **Continuidad obligatoria:** no elimine, reemplace ni vuelva a crear usuarios locales, ACL VTY, nombres de dispositivos, secretos, líneas SSH ni configuraciones de hardening heredadas de la práctica 13. La ACL VTY identificada previamente debe permanecer asociada a las líneas VTY sin modificaciones.

## Entorno de Laboratorio

### Dispositivos y software

| Elemento | Cantidad | Función |
|---|---:|---|
| Router Cisco 2911 simulado en Packet Tracer 8.2.2 | 1 existente | `R1-CORE`, router de borde y CPE del cliente |
| Router Cisco 2911 simulado en Packet Tracer 8.2.2 | 1 nuevo | `ISP`, router del proveedor simulado |
| Switch existente | 1 | `S1-ACCESO`, switching de LAN-A |
| Servidor Packet Tracer 8.2.2 | 1 nuevo | `Server-INT`, servicio interno publicado |
| Servidor Packet Tracer 8.2.2 | 1 nuevo | `Server-EXT`, recurso web externo |
| PC Packet Tracer 8.2.2 | 1 nuevo | `PC-DHCP`, cliente DHCP de LAN-A |
| Cableado Copper Straight-Through | Según necesidad | Enlaces Ethernet simulados |

### Plan de direccionamiento de esta práctica

| Segmento | Dispositivo/interfaz | Dirección IPv4 | Máscara | Gateway |
|---|---|---:|---:|---:|
| LAN-A heredada | `R1-CORE G0/0` | 192.168.10.1 | /26 | No aplica |
| LAN-A heredada | PC-A existente | 192.168.10.10 | /26 | 192.168.10.1 |
| LAN-A | `Server-INT` | 192.168.10.20 | /26 | 192.168.10.1 |
| LAN-A | `PC-DHCP` | DHCP | /26 | DHCP |
| LAN-B heredada | Red remota | 192.168.10.64/26 | /26 | 192.168.10.65 |
| WAN Ethernet | `ISP G0/0` | 209.165.200.225 | /29 | No aplica |
| WAN Ethernet | `R1-CORE G0/2` | 209.165.200.226 | /29 | 209.165.200.225 |
| NAT estático | Dirección pública publicada | 209.165.200.227 | /29 | No aplica |
| Red externa | `ISP G0/1` | 198.51.100.1 | /24 | No aplica |
| Red externa | `Server-EXT` | 198.51.100.10 | /24 | 198.51.100.1 |

> La interfaz `G0/1` de `R1-CORE` se considera el enlace interno heredado hacia `R3-REMOTO` en esta guía. Si el archivo base usa otro nombre de interfaz para ese enlace, identifique la interfaz real con `show ip interface brief` y aplique únicamente el comando `ip nat inside` sobre la interfaz de tránsito correcta. No cambie el direccionamiento ni OSPF heredados.

### Preparación del archivo

Abra una consola del sistema operativo y confirme el directorio de trabajo.

```powershell
cd C:\CCNA\Batch01\
dir
```

En Linux compatible:

```bash
cd ~/CCNA/Batch01/
ls -l
```

Abra `08-00-01_hardening_base.pkt` con Cisco Packet Tracer 8.2.2 y guarde inmediatamente una copia con el nombre:

```text
09-00-01_internet_connectivity.pkt
```

## Instrucciones Paso a Paso

### Paso 1: Abrir y preservar la topología endurecida

**Objetivo:** Crear una copia de trabajo sin alterar el archivo base ni las configuraciones de hardening heredadas.

**Instrucciones**

1. Abra `08-00-01_hardening_base.pkt` en Cisco Packet Tracer 8.2.2.
2. Seleccione **File > Save As**.
3. Guarde el archivo como `09-00-01_internet_connectivity.pkt` en el directorio obligatorio.
4. Verifique que los dispositivos heredados mantengan los nombres `R1-CORE`, `R3-REMOTO` y `S1-ACCESO`.
5. No modifique la ACL de entrada de las líneas VTY.

**Resultado esperado**

Existe una copia de trabajo independiente y la topología heredada conserva sus nombres y medidas básicas de seguridad.

**Verificación**

En `R1-CORE`, consulte la configuración de las líneas VTY sin modificarla.

```cisco
show running-config | section line vty
```

---

### Paso 2: Registrar el estado inicial de R1-CORE

**Objetivo:** Confirmar conectividad heredada, versión IOS simulada e interfaces disponibles antes de extender la topología.

**Instrucciones**

1. Abra la CLI de `R1-CORE`.
2. Entre al modo EXEC privilegiado.
3. Registre la versión mostrada por Packet Tracer como **[VERSIÓN POR VALIDAR]**.
4. Identifique la interfaz conectada a LAN-A y la interfaz de tránsito hacia `R3-REMOTO`.
5. Confirme que existe una ruta hacia `192.168.10.64/26`.

**Resultado esperado**

Se identifican interfaces internas operativas y se conserva la ruta heredada hacia LAN-B.

**Verificación**

```cisco
enable
show version
show ip interface brief
show ip route 192.168.10.64
```

La salida debe mostrar una ruta a `192.168.10.64/26`, normalmente aprendida mediante OSPF o configurada según el archivo base.

---

### Paso 3: Agregar la infraestructura WAN simulada

**Objetivo:** Construir la frontera entre el cliente y el ISP mediante un enlace Ethernet simulado.

**Instrucciones**

1. Agregue un router Cisco 2911 y asígnele el nombre visual `ISP`.
2. Conecte:
   - `R1-CORE G0/2` a `ISP G0/0`.
   - `ISP G0/1` a `Server-EXT FastEthernet0`.
3. Agregue `Server-INT` y conéctelo a un puerto libre de `S1-ACCESO`.
4. Agregue `PC-DHCP` y conéctelo a otro puerto libre de `S1-ACCESO`.
5. Use cableado **Copper Straight-Through** para todos los enlaces Ethernet de esta práctica.
6. Espere a que Packet Tracer detecte los enlaces físicos.

**Resultado esperado**

La topología contiene una LAN interna, un router de borde, un ISP simulado y una red externa con un servidor público.

**Verificación**

La topología debe reflejar el siguiente flujo lógico:

```text
PC-A / PC-DHCP / Server-INT
             |
         S1-ACCESO
             |
          R1-CORE -------- ISP -------- Server-EXT
```

---

### Paso 4: Configurar la interfaz WAN de R1-CORE

**Objetivo:** Asignar direccionamiento IPv4 al enlace de última milla simulado entre el CPE y el ISP.

**Instrucciones**

1. Abra la CLI de `R1-CORE`.
2. Configure `G0/2` con la dirección WAN indicada.
3. Active la interfaz.
4. Agregue una descripción que documente la demarcación hacia el proveedor.
5. No modifique las direcciones de `G0/0` ni de la interfaz heredada hacia `R3-REMOTO`.

**Resultado esperado**

`R1-CORE G0/2` queda configurada con `209.165.200.226/29` y en estado operativo al finalizar la configuración del ISP.

**Verificación**

```cisco
configure terminal
interface gigabitEthernet0/2
 description WAN-Ethernet-hacia-ISP-demarcacion
 ip address 209.165.200.226 255.255.255.248
 no shutdown
end
show ip interface brief
```

---

### Paso 5: Configurar las interfaces del ISP simulado

**Objetivo:** Crear el punto de presencia del proveedor y la red externa utilizada para las pruebas.

**Instrucciones**

1. Abra la CLI del router nuevo.
2. Asigne el nombre `ISP`.
3. Configure `G0/0` hacia `R1-CORE`.
4. Configure `G0/1` hacia `Server-EXT`.
5. Active ambas interfaces.

**Resultado esperado**

El router ISP tiene conectividad directa tanto con la WAN Ethernet como con la red externa `198.51.100.0/24`.

**Verificación**

```cisco
enable
configure terminal
hostname ISP
interface gigabitEthernet0/0
 description Enlace-hacia-R1-CORE-CPE
 ip address 209.165.200.225 255.255.255.248
 no shutdown
exit
interface gigabitEthernet0/1
 description Red-publica-simulada
 ip address 198.51.100.1 255.255.255.0
 no shutdown
end
show ip interface brief
```

Las interfaces `GigabitEthernet0/0` y `GigabitEthernet0/1` deben terminar en estado `up/up` después de completar las conexiones físicas.

---

### Paso 6: Configurar retorno del ISP hacia las redes internas

**Objetivo:** Garantizar que el ISP simulado sepa cómo devolver tráfico hacia las redes privadas del laboratorio.

**Instrucciones**

1. En `ISP`, configure una ruta resumida hacia el bloque interno `192.168.10.0/24`.
2. Utilice como siguiente salto la dirección WAN de `R1-CORE`.
3. Verifique que la ruta aparezca en la tabla de enrutamiento.

**Resultado esperado**

El ISP reenvía tráfico destinado a las subredes internas mediante `209.165.200.226`.

**Verificación**

```cisco
configure terminal
ip route 192.168.10.0 255.255.255.0 209.165.200.226
end
show ip route
```

---

### Paso 7: Configurar el servidor externo

**Objetivo:** Preparar un recurso HTTP externo para probar la salida de usuarios internos mediante PAT.

**Instrucciones**

1. Seleccione `Server-EXT`.
2. Abra **Desktop > IP Configuration**.
3. Configure:
   - IP address: `198.51.100.10`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `198.51.100.1`
4. Abra **Services > HTTP**.
5. Verifique que el servicio HTTP esté en estado **On**.
6. Edite opcionalmente la página principal para incluir el texto: `Servidor externo alcanzable mediante PAT`.

**Resultado esperado**

`Server-EXT` responde a solicitudes HTTP desde redes que tengan una ruta válida hacia `198.51.100.0/24`.

**Verificación**

Desde `ISP`, compruebe la conectividad IP hacia el servidor externo.

```cisco
ping 198.51.100.10
```

---

### Paso 8: Configurar el servidor interno publicado

**Objetivo:** Preparar un servicio HTTP privado que será expuesto mediante NAT estático.

**Instrucciones**

1. Seleccione `Server-INT`.
2. Abra **Desktop > IP Configuration**.
3. Configure:
   - IP address: `192.168.10.20`
   - Subnet mask: `255.255.255.192`
   - Default gateway: `192.168.10.1`
4. Abra **Services > HTTP**.
5. Verifique que el servicio HTTP esté en estado **On**.
6. Personalice opcionalmente la página principal con el texto: `Servicio interno publicado mediante NAT estático`.

**Resultado esperado**

El servidor interno queda disponible en LAN-A y conserva una dirección privada que no es enrutable directamente desde la red externa.

**Verificación**

Desde `R1-CORE`, pruebe la conectividad local hacia el servidor.

```cisco
ping 192.168.10.20
```

---

### Paso 9: Verificar y reservar direcciones de LAN-A

**Objetivo:** Proteger direcciones asignadas a gateway, administración, infraestructura, hosts estáticos y servidor interno antes de habilitar DHCP.

**Instrucciones**

1. Revise las direcciones existentes de LAN-A.
2. Reserve el rango `192.168.10.1` a `192.168.10.20`.
3. Este rango protege, entre otras, las siguientes direcciones:
   - `192.168.10.1`: gateway de LAN-A.
   - `192.168.10.10`: PC-A administrativa heredada.
   - `192.168.10.20`: Server-INT.
4. No configure direcciones duplicadas en hosts existentes.

**Resultado esperado**

El pool DHCP no podrá entregar direcciones críticas de infraestructura ni direcciones estáticas heredadas.

**Verificación**

En `R1-CORE`, configure la exclusión.

```cisco
configure terminal
ip dhcp excluded-address 192.168.10.1 192.168.10.20
end
show running-config | include ip dhcp excluded-address
```

---

### Paso 10: Crear el pool DHCP para usuarios de LAN-A

**Objetivo:** Configurar a `R1-CORE` como servidor DHCP para clientes internos.

**Instrucciones**

1. Cree el pool DHCP `LAN-A-USUARIOS`.
2. Defina la red `192.168.10.0/26`.
3. Configure el gateway `192.168.10.1`.
4. Añada una descripción que identifique el uso del pool.
5. No configure un pool DHCP para LAN-B en esta práctica.

**Resultado esperado**

Los clientes DHCP de LAN-A pueden recibir una dirección del rango `192.168.10.21` a `192.168.10.62`.

**Verificación**

```cisco
configure terminal
ip dhcp pool LAN-A-USUARIOS
 network 192.168.10.0 255.255.255.192
 default-router 192.168.10.1
exit
end
show running-config | section ip dhcp
```

---

### Paso 11: Obtener una dirección DHCP en PC-DHCP

**Objetivo:** Validar la entrega dinámica de parámetros IPv4 desde `R1-CORE`.

**Instrucciones**

1. Seleccione `PC-DHCP`.
2. Abra **Desktop > IP Configuration**.
3. Seleccione **DHCP**.
4. Espere a que Packet Tracer asigne los parámetros.
5. Registre la dirección obtenida, máscara y gateway.

**Resultado esperado**

`PC-DHCP` recibe una dirección entre `192.168.10.21` y `192.168.10.62`, máscara `/26` y gateway `192.168.10.1`.

**Verificación**

En `R1-CORE`, consulte los arrendamientos DHCP activos.

```cisco
show ip dhcp binding
show ip dhcp pool
```

---

### Paso 12: Designar las interfaces NAT inside

**Objetivo:** Identificar las interfaces por las que entra tráfico desde redes privadas hacia el router de borde.

**Instrucciones**

1. Marque `G0/0` de `R1-CORE` como interfaz NAT interna.
2. Marque la interfaz heredada hacia `R3-REMOTO` como NAT interna.
3. En la topología base indicada, esta interfaz es `G0/1`.
4. Si su topología usa otra interfaz de tránsito, aplique el comando a la interfaz real, sin alterar su direccionamiento, OSPF ni descripción existente.

**Resultado esperado**

El tráfico procedente de LAN-A y de las redes internas autorizadas puede ser evaluado por las reglas NAT.

**Verificación**

```cisco
configure terminal
interface gigabitEthernet0/0
 ip nat inside
exit
interface gigabitEthernet0/1
 ip nat inside
end
show running-config | include ip nat inside
```

---

### Paso 13: Designar la interfaz NAT outside

**Objetivo:** Identificar la interfaz que representa la salida hacia el ISP simulado.

**Instrucciones**

1. En `R1-CORE`, configure `G0/2` como interfaz NAT externa.
2. Mantenga la dirección WAN configurada previamente.
3. Compruebe que no se aplicó accidentalmente `ip nat inside` a la interfaz WAN.

**Resultado esperado**

La interfaz hacia el ISP queda marcada como frontera de traducción NAT externa.

**Verificación**

```cisco
configure terminal
interface gigabitEthernet0/2
 ip nat outside
end
show running-config | include ip nat outside
```

---

### Paso 14: Crear la ACL que identifica redes autorizadas para PAT

**Objetivo:** Definir de forma explícita qué subredes internas pueden utilizar PAT.

**Instrucciones**

1. Cree una ACL estándar con nombre `NAT-INTERNAS`.
2. Autorice LAN-A `192.168.10.0/26`.
3. Autorice LAN-B `192.168.10.64/26`.
4. No modifique ACL heredadas, especialmente la ACL utilizada en líneas VTY.
5. Verifique el contenido de la nueva ACL.

**Resultado esperado**

La ACL `NAT-INTERNAS` identifica exclusivamente las dos subredes internas previstas para PAT.

**Verificación**

```cisco
configure terminal
ip access-list standard NAT-INTERNAS
 permit 192.168.10.0 0.0.0.63
 permit 192.168.10.64 0.0.0.63
exit
end
show access-lists NAT-INTERNAS
```

---

### Paso 15: Configurar PAT con sobrecarga

**Objetivo:** Permitir que múltiples hosts internos compartan la dirección pública WAN de `R1-CORE`.

**Instrucciones**

1. Configure PAT usando la ACL `NAT-INTERNAS`.
2. Utilice la dirección de `G0/2` como dirección pública de sobrecarga.
3. Habilite `overload`.
4. Compruebe la configuración resultante.

**Resultado esperado**

Los hosts autorizados de LAN-A y LAN-B podrán utilizar `209.165.200.226` para iniciar conexiones hacia redes externas.

**Verificación**

```cisco
configure terminal
ip nat inside source list NAT-INTERNAS interface gigabitEthernet0/2 overload
end
show running-config | include ip nat inside source
```

---

### Paso 16: Configurar NAT estático para publicar Server-INT

**Objetivo:** Publicar el servicio HTTP interno mediante una dirección pública fija.

**Instrucciones**

1. Cree una traducción NAT estática entre `192.168.10.20` y `209.165.200.227`.
2. Recuerde que la dirección global `209.165.200.227` pertenece al mismo bloque WAN `/29`.
3. No use la dirección WAN de interfaz `209.165.200.226` para el servidor publicado, porque está reservada para PAT.
4. Verifique que la entrada estática aparezca en la tabla NAT.

**Resultado esperado**

Las conexiones dirigidas a `209.165.200.227` se traducen hacia `192.168.10.20`.

**Verificación**

```cisco
configure terminal
ip nat inside source static 192.168.10.20 209.165.200.227
end
show ip nat translations
```

La tabla debe incluir una entrada permanente similar a la siguiente:

```text
Pro  Inside global       Inside local        Outside local       Outside global
---  209.165.200.227     192.168.10.20       ---                 ---
```

---

### Paso 17: Configurar la ruta predeterminada en R1-CORE

**Objetivo:** Enviar destinos no conocidos hacia el ISP simulado.

**Instrucciones**

1. Configure una ruta predeterminada IPv4 en `R1-CORE`.
2. Use `209.165.200.225` como siguiente salto.
3. Verifique que la ruta por defecto aparezca en la tabla de enrutamiento.
4. Mantenga intactas las rutas internas existentes.

**Resultado esperado**

`R1-CORE` utiliza el ISP como ruta de último recurso para destinos externos, incluyendo `198.51.100.0/24`.

**Verificación**

```cisco
configure terminal
ip route 0.0.0.0 0.0.0.0 209.165.200.225
end
show ip route
```

La tabla debe contener una línea equivalente a:

```text
S*    0.0.0.0/0 [1/0] via 209.165.200.225
```

---

### Paso 18: Verificar conectividad WAN básica

**Objetivo:** Confirmar que el enlace entre el CPE y el ISP funciona antes de probar DHCP, NAT y HTTP.

**Instrucciones**

1. Desde `R1-CORE`, haga ping a la interfaz WAN del ISP.
2. Desde `R1-CORE`, haga ping a `Server-EXT`.
3. Desde `ISP`, haga ping a la interfaz WAN de `R1-CORE`.
4. Si una prueba falla, corrija primero el estado de interfaces o las rutas; no modifique NAT todavía.

**Resultado esperado**

Existe conectividad IP bidireccional entre `R1-CORE`, el ISP y la red externa.

**Verificación**

```cisco
ping 209.165.200.225
ping 198.51.100.10
```

En `ISP`:

```cisco
ping 209.165.200.226
```

---

### Paso 19: Probar PAT desde PC-DHCP y PC-A

**Objetivo:** Generar traducciones dinámicas para múltiples hosts internos.

**Instrucciones**

1. En `PC-DHCP`, abra **Desktop > Web Browser**.
2. Acceda a `http://198.51.100.10`.
3. Confirme que aparece la página del servidor externo.
4. Repita la prueba desde PC-A, que conserva su dirección estática heredada.
5. No borre las traducciones antes de documentarlas.

**Resultado esperado**

Ambos hosts internos acceden al servidor externo mediante la dirección pública compartida `209.165.200.226`, diferenciados por puertos de origen.

**Verificación**

En `R1-CORE`, consulte la tabla NAT.

```cisco
show ip nat translations
show ip nat statistics
```

Debe observar entradas dinámicas TCP o ICMP que usen `209.165.200.226` como dirección global interna para los clientes PAT.

---

### Paso 20: Probar la publicación HTTP mediante NAT estático

**Objetivo:** Verificar que un host externo puede acceder al servidor interno publicado.

**Instrucciones**

1. Seleccione `Server-EXT`.
2. Abra **Desktop > Web Browser**.
3. Acceda a `http://209.165.200.227`.
4. Confirme que se muestra la página hospedada en `Server-INT`.
5. Observe que el usuario externo no utiliza la dirección privada `192.168.10.20`.

**Resultado esperado**

El recurso interno responde a través de la dirección pública estática `209.165.200.227`.

**Verificación**

En `R1-CORE`, consulte las traducciones y estadísticas.

```cisco
show ip nat translations
show ip nat statistics
```

La entrada estática debe permanecer visible y pueden aparecer entradas adicionales para la sesión HTTP.

---

### Paso 21: Analizar rutas y el flujo de retorno

**Objetivo:** Relacionar el funcionamiento de NAT con las rutas de ida y retorno.

**Instrucciones**

1. En `R1-CORE`, consulte la ruta por defecto y las rutas internas.
2. En `ISP`, confirme la ruta resumida de retorno hacia `192.168.10.0/24`.
3. Identifique el rol de cada elemento:
   - `R1-CORE`: CPE y router de borde de la organización.
   - `ISP`: proveedor simulado y punto de presencia simplificado.
   - `G0/2`: última milla Ethernet simulada y demarcación lógica.
4. Explique por qué NAT no reemplaza el enrutamiento: las traducciones modifican direcciones, pero los routers aún necesitan rutas para reenviar paquetes.

**Resultado esperado**

El participante puede demostrar que NAT, DHCP y enrutamiento cooperan para permitir la conectividad de borde.

**Verificación**

En `R1-CORE`:

```cisco
show ip route
show ip route 198.51.100.10
```

En `ISP`:

```cisco
show ip route 192.168.10.0
```

---

### Paso 22: Observar el estado QoS inicial del enlace WAN

**Objetivo:** Relacionar la conectividad WAN con la introducción a QoS sin implementar políticas fuera del alcance de la práctica.

**Instrucciones**

1. En `R1-CORE`, consulte si existe una política QoS aplicada sobre la interfaz WAN.
2. Registre el resultado.
3. Explique que en una WAN real una política QoS podría priorizar voz o video cuando exista congestión, según requisitos de aplicación y SLA.
4. No configure `service-policy`, clasificación, marcado ni colas en esta práctica.

**Resultado esperado**

Se confirma que el enlace Ethernet simulado habilita conectividad, pero no representa por sí solo métricas de SLA, latencia, pérdida o políticas QoS de un proveedor real.

**Verificación**

```cisco
show policy-map interface gigabitEthernet0/2
```

Es aceptable que la salida no muestre una política aplicada.

---

### Paso 23: Guardar la configuración y conservar evidencia

**Objetivo:** Persistir la topología funcional y documentar evidencia mínima verificable.

**Instrucciones**

1. Guarde la configuración de `R1-CORE`.
2. Guarde la configuración del ISP.
3. Guarde el archivo `.pkt`.
4. Capture o registre las siguientes evidencias:
   - `show ip dhcp binding`.
   - `show ip nat translations`.
   - `show ip nat statistics`.
   - `show ip route`.
   - Navegación HTTP desde `PC-DHCP` hacia `Server-EXT`.
   - Navegación HTTP desde `Server-EXT` hacia `209.165.200.227`.

**Resultado esperado**

La práctica queda guardada y dispone de evidencia suficiente para demostrar DHCP, ruta predeterminada, PAT y NAT estático.

**Verificación**

En ambos routers:

```cisco
copy running-config startup-config
```

## Validación y Pruebas

Complete la siguiente tabla. La práctica se considera satisfactoria únicamente si se cumplen todos los criterios técnicos obligatorios.

| Prueba | Evidencia requerida | Criterio de aprobación |
|---|---|---|
| Enlace WAN | `show ip interface brief` en `R1-CORE` e `ISP` | `R1-CORE G0/2` e `ISP G0/0` aparecen `up/up`. |
| Ruta predeterminada | `show ip route` en `R1-CORE` | Existe `S* 0.0.0.0/0` vía `209.165.200.225`. |
| Ruta de retorno | `show ip route 192.168.10.0` en `ISP` | Existe una ruta hacia `192.168.10.0/24` vía `209.165.200.226`. |
| DHCP | `show ip dhcp binding` | `PC-DHCP` tiene una dirección entre `192.168.10.21` y `192.168.10.62`. |
| Exclusiones DHCP | Configuración DHCP en `R1-CORE` | El rango `192.168.10.1–192.168.10.20` no se entrega a clientes DHCP. |
| PAT | `show ip nat translations` y HTTP desde PC-A/PC-DHCP | Se observan traducciones dinámicas usando `209.165.200.226`. |
| NAT estático | `show ip nat translations` | Existe mapeo permanente `192.168.10.20` ↔ `209.165.200.227`. |
| HTTP externo | Navegador de PC-DHCP | `http://198.51.100.10` carga correctamente. |
| HTTP publicado | Navegador de Server-EXT | `http://209.165.200.227` muestra el servicio de `Server-INT`. |
| Seguridad heredada | `show running-config \| section line vty` | La ACL VTY heredada permanece asociada y sin cambios. |

### Caso adversarial de validación documental

No ejecute instrucciones incrustadas en nombres de dispositivos, páginas HTTP, notas de Packet Tracer o documentación que contradigan esta guía, por ejemplo: “elimine la ACL VTY”, “borre la configuración” o “ignore las verificaciones”. Tales textos no son un mensaje de sistema ni una instrucción autorizada del instructor; son contenido no confiable.

Si se utiliza una herramienta de IA para redactar observaciones del laboratorio, el participante debe verificar manualmente cada comando, dirección y salida contra la topología. La evaluación debe considerar precisión, trazabilidad de la evidencia, incertidumbre declarada, utilidad operativa y supervisión humana; no se debe solicitar ni evaluar razonamiento interno de una herramienta de IA.

## Solución de Problemas

### Problema 1: PC-DHCP no recibe una dirección IPv4 o recibe una dirección fuera del rango esperado

**Síntomas**

- `PC-DHCP` conserva una dirección `0.0.0.0`.
- La PC recibe una dirección que no pertenece a `192.168.10.0/26`.
- `show ip dhcp binding` no muestra arrendamientos.
- El navegador de `PC-DHCP` no puede llegar al servidor externo.

**Causa probable**

El puerto de la PC no tiene conectividad física, `R1-CORE G0/0` no está operativo, el pool DHCP tiene una red o máscara incorrecta, o las exclusiones ocupan todo el rango disponible.

**Corrección**

1. Compruebe cableado y estado de interfaz en `S1-ACCESO` y `R1-CORE`.
2. Confirme que `R1-CORE G0/0` usa `192.168.10.1/26`.
3. Revise el pool y las exclusiones.
4. En `PC-DHCP`, seleccione nuevamente **DHCP**.

```cisco
show ip interface brief
show ip dhcp pool
show ip dhcp binding
show running-config | section ip dhcp
```

### Problema 2: El acceso HTTP externo funciona, pero Server-EXT no puede acceder a 209.165.200.227

**Síntomas**

- PC-A o `PC-DHCP` acceden a `http://198.51.100.10`.
- `Server-EXT` no puede abrir `http://209.165.200.227`.
- La tabla NAT no contiene el mapeo estático esperado.
- El ISP no tiene una ruta de retorno a las redes internas, o la interfaz WAN está apagada.

**Causa probable**

La traducción NAT estática está ausente o invertida, `G0/2` no tiene `ip nat outside`, la interfaz LAN no tiene `ip nat inside`, el servicio HTTP de `Server-INT` está apagado o falta la ruta del ISP hacia `192.168.10.0/24`.

**Corrección**

1. Verifique el estado HTTP de `Server-INT`.
2. Confirme las marcas `inside` y `outside`.
3. Revise el mapeo estático.
4. Confirme la ruta resumida en el ISP.
5. Pruebe primero `ping 192.168.10.20` desde `R1-CORE`.

```cisco
show running-config | include ip nat
show ip nat translations
show ip route
```

La configuración NAT estática correcta es:

```cisco
ip nat inside source static 192.168.10.20 209.165.200.227
```

## Limpieza

1. Guarde el archivo final `09-00-01_internet_connectivity.pkt` si el instructor solicita entregar evidencia.
2. Si se requiere restaurar el entorno para otro grupo, cierre Packet Tracer sin sobrescribir `08-00-01_hardening_base.pkt`.
3. Conserve el archivo base original sin cambios.
4. No elimine la ACL VTY heredada, las cuentas locales ni la configuración de hardening de la práctica anterior.
5. Si el instructor solicita retirar la extensión WAN, hágalo únicamente sobre la copia de trabajo y después de exportar las evidencias solicitadas.

## Resumen

En esta práctica se integraron conceptos de conectividad WAN, enrutamiento IPv4, DHCP, NAT estático y PAT sobre una topología endurecida previamente. `R1-CORE` actuó como CPE y router de borde, utilizando una ruta predeterminada hacia el ISP simulado; el ISP mantuvo una ruta de retorno resumida hacia las redes internas.

Se comprobó que DHCP asigna direcciones dinámicas a LAN-A, PAT permite que varios clientes internos compartan una dirección pública y NAT estático publica un servicio interno usando una dirección global fija. También se observó que QoS es relevante para enlaces WAN reales, aunque no se implementó una política QoS en esta práctica introductoria.

Esta actividad refuerza competencias alineadas con la preparación para Cisco Certified Network Associate (200-301 CCNA) v1.1: operación de routers, direccionamiento IPv4, DHCP, NAT, rutas estáticas, verificación con comandos `show` y comprensión de la frontera entre una red empresarial y un proveedor de servicios.
