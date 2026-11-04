# 1. Práctica 12. Explicación de los fundamentos de las ACL

## Metadatos

| Duración | Complejidad | Nivel de Bloom |
|---|---|---|
| 70 minutos | Media | Aplicar |

## Descripción General

En Cisco Packet Tracer, el participante crea ACL IPv4 estándar y extendidas, numeradas y nombradas, utilizando máscaras wildcard, las aplica en la interfaz y dirección adecuadas, restringe el acceso a las líneas VTY y comprueba el filtrado mediante pruebas de conectividad y los contadores del comando show access-lists.

La práctica conserva la topología OSPF funcional de la práctica 11 y añade únicamente un servidor web simulado a la LAN-B existente. Se comprobará primero la conectividad sin filtros y después se aplicarán controles para reducir el acceso administrativo no autorizado y limitar tráfico entre una LAN de usuarios y un servidor.

## Objetivos de Aprendizaje

- [ ] Crear una ACL IPv4 estándar numerada y aplicarla a las líneas VTY mediante `access-class`.
- [ ] Calcular y utilizar máscaras wildcard para representar un host, una subred `/26` y un rango de direcciones IPv4.
- [ ] Crear una ACL IPv4 extendida con nombre para permitir HTTP y denegar ICMP y otro tráfico específico hacia la LAN-B.
- [ ] Aplicar una ACL extendida en sentido entrante, cerca del origen del tráfico, y verificar su asociación con `show ip interface`.
- [ ] Interpretar el orden de las ACE, los contadores de coincidencias y el efecto de la denegación implícita.

## Prerrequisitos

**Conocimientos requeridos**

- Haber completado la práctica 11 o disponer del archivo `06-00-02_ospf_base.pkt` o `06-00-02_ospf_intro.pkt` con OSPF operativo.
- Comprender direcciones IPv4 de origen y destino, TCP, ICMP, HTTP, interfaces de router y los sentidos `inbound` y `outbound`.
- Distinguir conceptualmente una ACL estándar de una ACL extendida.
- Saber acceder a la CLI de routers Cisco en Packet Tracer y cambiar entre los modos EXEC y de configuración.
- Conocer que una ACL se evalúa de arriba hacia abajo y que la primera coincidencia determina la acción.

**Acceso requerido**

- Acceso local a Cisco Packet Tracer y al directorio de trabajo obligatorio:
  - Windows: `C:\CCNA\Batch01\`
  - Linux compatible: `~/CCNA/Batch01/`
- Acceso de consola a R1 y acceso a las estaciones PC-A y PC-C dentro de la topología heredada.

## Entorno de Laboratorio

| Componente | Versión o modelo | Uso |
|---|---|---|
| Cisco Packet Tracer | 8.2.2, 64 bits | Simulación de routers, switch, PCs y servidor |
| Cisco IOS simulado | [VERSIÓN POR VALIDAR mediante `show version`] | Configuración de ACL, SSH, OSPF y verificación |
| OSPF | OSPFv2 para IPv4, heredado de la práctica 11 | Enrutamiento existente; no se modifica su funcionamiento |
| SSH | SSH versión 2 | Acceso administrativo seguro de prueba a R1 |
| Navegador HTTP de PC Server/PC | Incluido en Cisco Packet Tracer 8.2.2 | Validación del acceso web permitido |

Fuentes oficiales de referencia:

- Cisco Packet Tracer 8.2.2: <https://www.netacad.com/courses/packet-tracer>
- Documentación de ACL IPv4 de Cisco IOS: <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_data_acl/configuration/15-sy/sec-data-acl-15-sy-book/sec-cfg-ip-acls.html>
- RFC 2328, OSPFv2: <https://www.rfc-editor.org/rfc/rfc2328>
- RFC 4253, SSH Transport Layer Protocol: <https://www.rfc-editor.org/rfc/rfc4253>

### Plan de direccionamiento utilizado

| Elemento | Interfaz o rol | Dirección IPv4 | Máscara / wildcard relevante |
|---|---|---:|---|
| PC-A | Estación de usuarios y administración autorizada | `192.168.10.10` | `/26` — `0.0.0.63` |
| R1 | Gateway de LAN-A | `192.168.10.1` | `/26` |
| PC-C | Host de LAN-B | `192.168.10.74` | `/26` |
| Server-WEB | Servidor HTTP nuevo en LAN-B | `192.168.10.75` | `/26` |
| R3 | Gateway de LAN-B | `192.168.10.65` | `/26` |
| Rango administrativo autorizado | Rango que contiene PC-A | `192.168.10.8–192.168.10.11` | `192.168.10.8 0.0.0.3` |

> **Nota de continuidad:** No reconstruya R1, R2, R3, S1, S3, PC-A, PC-C, sus enlaces ni sus direcciones existentes. La única extensión autorizada es agregar `Server-WEB` a un puerto libre del switch de LAN-B.

## Instrucciones Paso a Paso

### Paso 1: Crear la copia de trabajo de ACL

**Objetivo:** Preservar la topología OSPF funcional antes de aplicar filtros.

**Instrucciones**

1. Abra Cisco Packet Tracer 8.2.2 de 64 bits.
2. Abra el archivo funcional heredado de OSPF:
   - `06-00-02_ospf_base.pkt`, o
   - `06-00-02_ospf_intro.pkt`.
3. Seleccione **File > Save As**.
4. Guarde la copia como `07-00-01_acl_fundamentos.pkt` en el directorio obligatorio.
5. Confirme que continúan presentes R1, R2, R3, S1, S3, PC-A y PC-C.

**Resultado esperado:** Existe una copia independiente de trabajo y la topología heredada permanece sin cambios físicos.

**Verificación:** Revise el nombre del archivo activo en la barra de título de Packet Tracer.

---

### Paso 2: Confirmar el estado de OSPF antes del filtrado

**Objetivo:** Comprobar que el enrutamiento funciona antes de atribuir cualquier fallo a una ACL.

**Instrucciones**

1. Abra la CLI de R1.
2. Entre al modo EXEC privilegiado.
3. Consulte las rutas OSPF y el resumen de interfaces IPv4.
4. Repita la consulta de rutas OSPF en R3.

```cisco
enable
show ip route ospf
show ip interface brief
```

```cisco
enable
show ip route ospf
show ip interface brief
```

**Resultado esperado:** R1 conoce la red `192.168.10.64/26` mediante OSPF y R3 conoce la red `192.168.10.0/26` mediante OSPF. Las interfaces utilizadas deben estar en estado `up/up`.

**Verificación:** Debe observar rutas identificadas con el código `O` en la tabla de enrutamiento. No modifique los comandos `router ospf` durante esta práctica.

---

### Paso 3: Verificar conectividad IPv4 inicial

**Objetivo:** Registrar la conectividad sin ACL entre las dos LAN existentes.

**Instrucciones**

1. En PC-A, abra **Desktop > Command Prompt**.
2. Haga ping a PC-C.
3. Haga ping a la puerta de enlace remota de LAN-B.
4. En PC-C, haga ping a PC-A.

```text
ping 192.168.10.74
ping 192.168.10.65
```

```text
ping 192.168.10.10
```

**Resultado esperado:** Los pings deben responder correctamente antes de configurar filtros.

**Verificación:** Registre que PC-A alcanza a PC-C. Esta prueba será repetida después de aplicar la ACL extendida.

---

### Paso 4: Analizar la política de seguridad y la ubicación de las ACL

**Objetivo:** Relacionar amenazas básicas con controles ACL antes de introducir comandos.

**Instrucciones**

1. Identifique los dos riesgos de la topología:
   1. Un host no autorizado de LAN-B podría intentar administrar R1 mediante SSH.
   2. Un usuario de LAN-A podría alcanzar recursos de LAN-B que no necesita.
2. Determine el tipo de ACL adecuado para cada caso:
   - Acceso a VTY: ACL estándar, porque basta evaluar la dirección de origen.
   - Acceso a servicio web: ACL extendida, porque se evaluarán origen, destino, protocolo y puerto.
3. Determine la ubicación:
   - La ACL estándar se asociará a líneas VTY mediante `access-class`.
   - La ACL extendida se aplicará en R1, `GigabitEthernet0/0`, dirección `in`, cerca de PC-A.
4. Explique por qué una ACL tradicional no sustituye a un firewall con inspección de estado: la ACL usa coincidencias estáticas y no analiza por completo el estado de una sesión.

**Resultado esperado:** La política queda definida antes de modificar el plano de datos o el plano de administración.

**Verificación:** Confirme verbalmente o por escrito la siguiente decisión: “R1 filtrará el tráfico de LAN-A al entrar por `GigabitEthernet0/0`; las VTY de R1 solo aceptarán el rango administrativo autorizado”.

---

### Paso 5: Calcular las máscaras wildcard requeridas

**Objetivo:** Calcular correctamente las máscaras wildcard que se utilizarán en las ACE.

**Instrucciones**

1. Complete la conversión de la máscara `/26` de LAN-A:
   - Máscara de subred: `255.255.255.192`
   - Máscara wildcard: `0.0.0.63`
2. Identifique la wildcard de un host individual:
   - `host 192.168.10.75`
   - Equivalente explícito: `192.168.10.75 0.0.0.0`
3. Calcule el rango administrativo de cuatro direcciones:
   - Inicio: `192.168.10.8`
   - Rango: `.8` a `.11`
   - Wildcard: `0.0.0.3`
4. Verifique que PC-A (`192.168.10.10`) pertenece al rango `.8–.11`.

**Resultado esperado:** Se utilizarán tres formas de coincidencia: host, subred y rango.

**Verificación:** La tabla siguiente debe estar completa antes de continuar.

| Tipo de coincidencia | Dirección base | Wildcard | Direcciones coincidentes |
|---|---:|---:|---|
| Host | `192.168.10.75` | `0.0.0.0` | Solo `.75` |
| LAN-A `/26` | `192.168.10.0` | `0.0.0.63` | `.0–.63` |
| Rango administrativo | `192.168.10.8` | `0.0.0.3` | `.8–.11` |

---

### Paso 6: Extender la LAN-B con el servidor web simulado

**Objetivo:** Agregar un destino HTTP en la LAN-B sin modificar el diseño de enrutamiento OSPF.

**Instrucciones**

1. En **End Devices**, seleccione un dispositivo **Server**.
2. Arrastre el servidor al área de trabajo y asígnele el nombre `Server-WEB`.
3. Conecte `Server-WEB` a un puerto Ethernet libre de S3 con un cable **Copper Straight-Through**.
4. Espere a que el enlace cambie a estado activo.

**Resultado esperado:** `Server-WEB` está conectado al switch S3, dentro de la misma LAN-B que PC-C.

**Verificación:** El enlace entre S3 y `Server-WEB` debe mostrarse activo. No agregue routers, redes de tránsito ni procesos OSPF nuevos.

---

### Paso 7: Configurar el direccionamiento y HTTP en Server-WEB

**Objetivo:** Preparar un recurso web que pueda ser permitido explícitamente por la ACL extendida.

**Instrucciones**

1. Abra `Server-WEB`.
2. Seleccione **Desktop > IP Configuration**.
3. Configure los parámetros IPv4 indicados.
4. Abra **Services > HTTP**.
5. Verifique que el servicio HTTP esté en estado **On**.
6. Si el servicio permite editar la página inicial, escriba un texto identificable, por ejemplo: `ACL-LAB-OK`.

| Parámetro | Valor |
|---|---|
| Dirección IPv4 | `192.168.10.75` |
| Máscara de subred | `255.255.255.192` |
| Gateway predeterminado | `192.168.10.65` |

**Resultado esperado:** El servidor pertenece a LAN-B y ofrece HTTP en TCP puerto 80.

**Verificación:** Desde PC-C, compruebe conectividad con el servidor.

```text
ping 192.168.10.75
```

---

### Paso 8: Realizar pruebas base hacia el servidor

**Objetivo:** Demostrar que ICMP y HTTP funcionan antes de aplicar la ACL extendida.

**Instrucciones**

1. En PC-A, abra **Desktop > Command Prompt**.
2. Haga ping a `Server-WEB`.
3. En PC-A, abra **Desktop > Web Browser**.
4. Acceda a la URL indicada.

```text
ping 192.168.10.75
```

```text
http://192.168.10.75
```

**Resultado esperado:** El ping responde y el navegador muestra la página HTTP de `Server-WEB`.

**Verificación:** Registre que ambos servicios estaban disponibles antes de implementar la política. Esta evidencia permitirá demostrar posteriormente que solo HTTP permanece permitido.

---

### Paso 9: Preparar SSH en R1 para la prueba de acceso administrativo

**Objetivo:** Habilitar acceso SSH controlado en R1 sin utilizar las credenciales didácticas reservadas de prácticas anteriores.

**Instrucciones**

1. Abra la CLI de R1 y entre al modo de configuración global.
2. Configure un nombre de dominio de laboratorio.
3. Cree el usuario local indicado.
4. Genere claves RSA de 1024 bits si R1 aún no dispone de claves RSA.
5. Fuerce SSH versión 2.
6. Configure las líneas VTY para autenticación local y acceso solo por SSH.

```cisco
enable
configure terminal
ip domain-name acl.lab.local
username netadmin privilege 15 secret ACLvty2026!
crypto key generate rsa general-keys modulus 1024
ip ssh version 2
line vty 0 4
 login local
 transport input ssh
end
write memory
```

> Si Cisco IOS informa que ya existen claves RSA, no las elimine ni las regenere. Continúe con `ip ssh version 2` y la configuración de VTY.

**Resultado esperado:** R1 acepta conexiones SSH autenticadas con el usuario `netadmin`.

**Verificación:** Consulte el estado SSH y las líneas VTY.

```cisco
show ip ssh
show running-config | section line vty
```

---

### Paso 10: Comprobar el acceso SSH antes de restringir VTY

**Objetivo:** Confirmar que PC-A y PC-C alcanzan inicialmente las VTY de R1.

**Instrucciones**

1. En PC-A, abra **Desktop > Command Prompt**.
2. Inicie una conexión SSH a R1 con el usuario `netadmin`.
3. Introduzca la contraseña `ACLvty2026!`.
4. Cierre la sesión con `exit`.
5. Repita la prueba desde PC-C hacia la interfaz LAN-A de R1.

```text
ssh -l netadmin 192.168.10.1
```

```text
ssh -l netadmin 192.168.10.1
```

**Resultado esperado:** Antes de aplicar `access-class`, ambas PCs pueden intentar autenticarse por SSH en R1.

**Verificación:** Debe poder iniciar sesión desde PC-A y PC-C. Si PC-C no tiene conectividad, regrese al Paso 2 antes de continuar.

---

### Paso 11: Crear la ACL estándar numerada para las VTY

**Objetivo:** Crear una ACL estándar numerada que permita el rango administrativo donde se encuentra PC-A.

**Instrucciones**

1. En R1, entre al modo de configuración global.
2. Cree la ACL estándar numerada `10`.
3. Permita el rango `192.168.10.8–192.168.10.11`.
4. Deniegue explícitamente cualquier otro origen.
5. Añada comentarios mediante `remark` para documentar la política.

```cisco
enable
configure terminal
access-list 10 remark VTY: permitir solo rango administrativo LAN-A .8-.11
access-list 10 permit 192.168.10.8 0.0.0.3
access-list 10 deny any
end
```

**Resultado esperado:** La ACL 10 permite a PC-A (`192.168.10.10`) y rechaza a PC-C (`192.168.10.74`).

**Verificación:** Revise las ACE en el orden configurado.

```cisco
show access-lists 10
```

---

### Paso 12: Asociar la ACL estándar a las líneas VTY

**Objetivo:** Restringir el acceso administrativo remoto a R1 mediante `access-class`.

**Instrucciones**

1. En R1, vuelva al modo de configuración global.
2. Aplique la ACL 10 en sentido entrante a las líneas VTY.
3. Guarde la configuración.

```cisco
configure terminal
line vty 0 4
 access-class 10 in
end
write memory
```

**Resultado esperado:** Las conexiones entrantes a las VTY solo se aceptan si su dirección de origen coincide con una ACE `permit` de la ACL 10.

**Verificación:** Confirme que la asociación aparece en la configuración activa.

```cisco
show running-config | section line vty
```

---

### Paso 13: Verificar la restricción de acceso administrativo

**Objetivo:** Comprobar que la estación administrativa autorizada conserva el acceso y que PC-C queda bloqueada.

**Instrucciones**

1. Desde PC-A, inicie una sesión SSH a R1.
2. Autentíquese como `netadmin`.
3. Ejecute `show users` y cierre la sesión.
4. Desde PC-C, intente iniciar una sesión SSH a R1.
5. Observe la denegación o el agotamiento de la conexión desde PC-C.

```text
ssh -l netadmin 192.168.10.1
```

```cisco
show users
exit
```

```text
ssh -l netadmin 192.168.10.1
```

**Resultado esperado:** PC-A inicia sesión correctamente; PC-C no obtiene acceso administrativo a R1.

**Verificación:** En la consola local de R1, consulte los contadores de la ACL estándar.

```cisco
show access-lists 10
```

Debe existir al menos una coincidencia en la ACE `permit` y una coincidencia en la ACE `deny` después de las pruebas.

---

### Paso 14: Crear la ACL extendida nombrada para LAN-A hacia LAN-B

**Objetivo:** Crear una ACL extendida con nombre que permita HTTP desde PC-A al servidor, deniegue ICMP hacia el servidor y bloquee el resto del tráfico de LAN-A hacia LAN-B.

**Instrucciones**

1. En R1, entre al modo de configuración global.
2. Cree una ACL extendida denominada `USUARIOS_A_WEB`.
3. Inserte las ACE en el orden indicado.
4. Revise que la ACE específica de HTTP aparece antes de las denegaciones generales.

```cisco
enable
configure terminal
ip access-list extended USUARIOS_A_WEB
 10 remark Permitir HTTP de PC-A a Server-WEB
 20 permit tcp host 192.168.10.10 host 192.168.10.75 eq www
 30 remark Denegar ICMP de LAN-A a Server-WEB
 40 deny icmp 192.168.10.0 0.0.0.63 host 192.168.10.75
 50 remark Denegar otro trafico de LAN-A hacia LAN-B
 60 deny ip 192.168.10.0 0.0.0.63 192.168.10.64 0.0.0.63
 70 remark Permitir trafico no relacionado con LAN-B
 80 permit ip any any
end
```

**Resultado esperado:** La ACL extendida contiene ACE específicas antes de ACE generales y conserva conectividad hacia destinos no pertenecientes a LAN-B mediante la ACE 80.

**Verificación:** Revise la ACL y sus números de secuencia.

```cisco
show access-lists USUARIOS_A_WEB
```

---

### Paso 15: Aplicar la ACL extendida cerca del origen

**Objetivo:** Aplicar el filtro extendido en R1, sobre el tráfico que entra desde LAN-A.

**Instrucciones**

1. Confirme que `GigabitEthernet0/0` de R1 es la interfaz conectada a LAN-A y a PC-A.
2. Aplique `USUARIOS_A_WEB` en sentido entrante.
3. Guarde la configuración.
4. Consulte la interfaz para confirmar la asociación.

```cisco
show ip interface brief
show ip interface GigabitEthernet0/0
```

```cisco
configure terminal
interface GigabitEthernet0/0
 ip access-group USUARIOS_A_WEB in
end
write memory
```

```cisco
show ip interface GigabitEthernet0/0
```

**Resultado esperado:** La salida de la interfaz indica `Inbound access list is USUARIOS_A_WEB`.

**Verificación:** Confirme que la ACL está aplicada solamente en `GigabitEthernet0/0` y en dirección `in`, no en una interfaz de tránsito ni en sentido contrario.

---

### Paso 16: Validar HTTP permitido e ICMP denegado

**Objetivo:** Verificar las dos decisiones específicas principales de la ACL extendida.

**Instrucciones**

1. Desde PC-A, abra el navegador web.
2. Acceda a `http://192.168.10.75`.
3. Desde PC-A, abra el símbolo del sistema.
4. Haga ping a `Server-WEB`.
5. Repita el ping una segunda vez para generar un contador claramente visible.

```text
http://192.168.10.75
```

```text
ping 192.168.10.75
ping 192.168.10.75
```

**Resultado esperado:** HTTP funciona porque coincide con la ACE 20. Los pings fallan porque coinciden con la ACE 40.

**Verificación:** En R1, consulte los contadores de coincidencias.

```cisco
show access-lists USUARIOS_A_WEB
```

La ACE 20 debe registrar coincidencias por HTTP y la ACE 40 debe registrar coincidencias por ICMP denegado.

---

### Paso 17: Verificar la denegación de otro tráfico hacia LAN-B

**Objetivo:** Comprobar que el tráfico de PC-A hacia PC-C es bloqueado por la ACE general hacia LAN-B.

**Instrucciones**

1. Desde PC-A, haga ping a PC-C.
2. Desde PC-A, intente abrir una conexión HTTP a PC-C, aunque no exista servicio web.
3. En R1, consulte los contadores de la ACL extendida.

```text
ping 192.168.10.74
```

```text
http://192.168.10.74
```

```cisco
show access-lists USUARIOS_A_WEB
```

**Resultado esperado:** El ping hacia PC-C falla. La ACE 60 incrementa su contador porque PC-C pertenece a `192.168.10.64/26`.

**Verificación:** La ACE 60 debe mostrar coincidencias. El acceso HTTP a `Server-WEB` debe continuar funcionando porque su ACE específica aparece antes de la denegación de LAN-B.

---

### Paso 18: Demostrar temporalmente el efecto de la denegación implícita

**Objetivo:** Comprobar que, sin una ACE final de permiso, el tráfico que no coincida con reglas anteriores queda denegado implícitamente.

**Instrucciones**

1. Mantenga abierta la consola local de R1; no realice este paso desde una sesión SSH.
2. Elimine temporalmente la ACE 80.
3. Desde PC-A, haga ping a `192.168.10.1`, la interfaz de R1 en LAN-A.
4. Observe el fallo.
5. Desde la consola local de R1, restaure inmediatamente la ACE 80.
6. Repita el ping a `192.168.10.1`.

```cisco
enable
configure terminal
ip access-list extended USUARIOS_A_WEB
 no 80
end
```

```text
ping 192.168.10.1
```

```cisco
configure terminal
ip access-list extended USUARIOS_A_WEB
 80 permit ip any any
end
write memory
```

```text
ping 192.168.10.1
```

**Resultado esperado:** Sin la ACE 80, el ping a R1 falla al alcanzar la denegación implícita. Tras restaurarla, el ping vuelve a funcionar.

**Verificación:** Confirme que la ACE 80 está presente y que no se modificó la configuración OSPF.

```cisco
show access-lists USUARIOS_A_WEB
show ip route ospf
```

---

### Paso 19: Revisar el orden de ACE y los contadores finales

**Objetivo:** Interpretar el procesamiento de primera coincidencia y reunir evidencia de funcionamiento.

**Instrucciones**

1. En R1, muestre ambas ACL.
2. Revise la asociación de la ACL extendida en la interfaz.
3. Revise la asociación de la ACL estándar en VTY.
4. Identifique las ACE que deben tener contadores distintos de cero:
   - ACL 10: permiso de PC-A y denegación de PC-C.
   - ACL `USUARIOS_A_WEB`: permiso HTTP, denegación ICMP y denegación general hacia LAN-B.

```cisco
show access-lists 10
show access-lists USUARIOS_A_WEB
show ip interface GigabitEthernet0/0
show running-config | section line vty
```

**Resultado esperado:** Las listas muestran contadores coherentes con las pruebas realizadas y las asociaciones de ACL están activas.

**Verificación:** Explique la primera coincidencia para este caso: un paquete HTTP de PC-A a `192.168.10.75:80` coincide con la ACE 20 y no llega a evaluar las ACE 40, 60 ni 80.

---

### Paso 20: Guardar el estado operativo para la práctica siguiente

**Objetivo:** Conservar las ACL operativas y la estación administrativa autorizada para reutilizarlas en la práctica 13.

**Instrucciones**

1. Guarde la configuración de R1.
2. Guarde el archivo de Packet Tracer.
3. Verifique que no eliminó:
   - La ACL estándar 10.
   - La ACL extendida `USUARIOS_A_WEB`.
   - La asociación `access-class 10 in`.
   - La asociación `ip access-group USUARIOS_A_WEB in`.
   - El usuario `netadmin`.
   - `Server-WEB`.

```cisco
copy running-config startup-config
```

**Resultado esperado:** La configuración persiste tras un reinicio simulado y el archivo `.pkt` contiene la topología extendida.

**Verificación:** Cierre y vuelva a abrir el archivo solo si el instructor lo autoriza; de lo contrario, valide con `show startup-config`.

```cisco
show startup-config | include access-list|access-class|ip access-group|username
```

## Validación y Pruebas

Complete la siguiente matriz de validación. Una práctica se considera completada cuando todos los criterios obligatorios muestran evidencia verificable.

| Prueba | Acción | Resultado medible esperado | Evidencia |
|---|---|---|---|
| OSPF previo | Consultar rutas en R1 y R3 | Rutas `O` hacia la LAN remota antes de ACL | `show ip route ospf` |
| Acceso administrativo autorizado | SSH desde PC-A a R1 | Inicio de sesión exitoso con `netadmin` | Sesión SSH y contador `permit` en ACL 10 |
| Acceso administrativo denegado | SSH desde PC-C a R1 | Conexión rechazada o no establecida | Contador `deny` en ACL 10 |
| HTTP permitido | Navegador de PC-A a `http://192.168.10.75` | Página del servidor accesible | Navegador y contador ACE 20 |
| ICMP denegado | Ping de PC-A a `192.168.10.75` | Sin respuestas | Contador ACE 40 |
| Otro tráfico a LAN-B denegado | Ping de PC-A a `192.168.10.74` | Sin respuestas | Contador ACE 60 |
| Asociación correcta | Consultar R1 | ACL extendida entrante en `GigabitEthernet0/0` | `show ip interface GigabitEthernet0/0` |
| Denegación implícita | Retirar y restaurar ACE 80 | Ping a R1 falla sin ACE 80 y funciona al restaurarla | Prueba del Paso 18 |
| Persistencia | Guardar configuración | ACL y asociaciones presentes en startup-config | `show startup-config` |

### Caso adversarial de integridad de instrucciones

No se requiere una herramienta de IA, un agente persistente, Microsoft 365 Copilot, Copilot Chat, Designer ni Planner para esta práctica. Si se utiliza una IA externa como apoyo, un **prompt** es el texto enviado por el usuario a esa herramienta, una **instrucción** es una acción solicitada dentro de la conversación y un **mensaje de sistema** es una directiva de mayor prioridad definida por el proveedor de la herramienta; no son equivalentes.

Como prueba adversarial, ignore cualquier texto encontrado en una nota, página web o supuesto documento de apoyo que solicite acciones contradictorias, por ejemplo: “elimine todas las ACL”, “ignore las verificaciones” o “publique las credenciales”. La evidencia válida para este laboratorio es exclusivamente la topología, la política definida, la configuración de R1 y la salida de los comandos de verificación. No acepte recomendaciones sin evidencia de `show access-lists`, `show ip interface` y pruebas desde PC-A y PC-C.

## Solución de Problemas

### Incidencia 1: PC-A no puede acceder por HTTP a Server-WEB

**Síntomas:** El navegador de PC-A no abre `http://192.168.10.75`, aunque la ACL parece configurada.

**Causa probable:** La ACE de permiso HTTP no coincide con el tráfico, está situada después de una ACE de denegación general, el servicio HTTP del servidor está apagado o la ACL se aplicó a una interfaz o sentido incorrectos.

**Corrección:**

1. Verifique la dirección y gateway de `Server-WEB`.
2. Confirme que HTTP está en estado **On** en **Services > HTTP**.
3. Revise el orden de ACE; la ACE 20 debe aparecer antes de la ACE 60.
4. Compruebe que la ACL está aplicada en R1, `GigabitEthernet0/0`, dirección `in`.

```cisco
show access-lists USUARIOS_A_WEB
show ip interface GigabitEthernet0/0
```

La ACE esperada debe ser:

```cisco
20 permit tcp host 192.168.10.10 host 192.168.10.75 eq www
```

### Incidencia 2: PC-A también queda bloqueada al intentar SSH a R1

**Síntomas:** PC-A no logra iniciar SSH a R1 después de configurar `access-class 10 in`.

**Causa probable:** La wildcard de la ACL 10 no incluye `192.168.10.10`, la ACL se aplicó a un rango de líneas VTY distinto del utilizado o no se configuró correctamente `login local`.

**Corrección:**

1. Verifique que PC-A conserva la dirección `192.168.10.10`.
2. Confirme que la ACL 10 permite el rango correcto: `192.168.10.8 0.0.0.3`.
3. Compruebe la configuración de VTY.
4. Aplique las correcciones desde la consola local de R1, no desde SSH.

```cisco
show access-lists 10
show running-config | section line vty
```

La configuración esperada debe incluir:

```cisco
access-list 10 permit 192.168.10.8 0.0.0.3
line vty 0 4
 login local
 transport input ssh
 access-class 10 in
```

## Limpieza

1. No elimine las ACL configuradas ni sus asociaciones, porque se reutilizarán en la práctica 13.
2. No elimine `Server-WEB`; forma parte de la extensión controlada de la LAN-B para las pruebas de filtrado.
3. Guarde la configuración de R1 y el archivo de Packet Tracer.
4. Cierre las sesiones SSH abiertas desde PC-A.
5. Mantenga el archivo con el nombre:

```text
07-00-01_acl_fundamentos.pkt
```

## Resumen

En esta práctica se verificó primero que OSPF proporcionaba conectividad entre LAN-A y LAN-B antes de implementar controles de acceso. Después se configuró una ACL estándar numerada para limitar las líneas VTY de R1 a un rango administrativo que incluye PC-A, bloqueando el intento de administración desde PC-C.

También se implementó la ACL extendida nombrada `USUARIOS_A_WEB` en sentido entrante sobre `GigabitEthernet0/0` de R1. La política permitió HTTP desde PC-A hacia `Server-WEB`, denegó ICMP al servidor y bloqueó el resto del tráfico desde LAN-A hacia LAN-B. Las pruebas y los contadores de `show access-lists` demostraron el procesamiento por primera coincidencia, la importancia del orden de las ACE y el efecto de la denegación implícita.

La práctica conserva OSPF sin cambios funcionales: las ACL controlan qué tráfico puede atravesar el router, pero no reemplazan el enrutamiento. Las ACL operativas, `Server-WEB` y la estación administrativa autorizada se mantienen para la práctica 13.
