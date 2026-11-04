# 1. Práctica 13. Protección del acceso administrativo e implementación del endurecimiento (hardening) de dispositivos

## Metadatos

| Campo | Valor |
|---|---|
| Duration | 60 minutos |
| Complejidad | Media |
| Nivel de Bloom | Aplicar |

## Descripción General

En Cisco Packet Tracer, el participante configura contraseñas cifradas y enable secret, cuentas de usuario locales, acceso remoto seguro mediante SSH, banners de inicio de sesión, port security en los puertos de acceso del switch y la deshabilitación de servicios y puertos no utilizados, verificando que solo el acceso autorizado sea permitido.

Esta práctica continúa la topología de la práctica 12, conservada en el archivo `07-00-01_acl_ospf.pkt`. Se endurecen los dispositivos de la red interna antes de conectarla a un borde WAN/NAT en la práctica posterior. No se modificarán los procesos OSPF ni la ACL VTY heredada que autoriza la estación de administración.

Esta ruta de aprendizaje prepara al participante para instalar, operar, configurar y verificar redes IPv4 e IPv6, administrar dispositivos de red, identificar amenazas básicas de seguridad y comprender fundamentos de programabilidad, automatización, SDN, IA y ML aplicados a operaciones de red. Los contenidos están alineados con Cisco Certified Network Associate (200-301 CCNA) v1.1.

## Objetivos de Aprendizaje

- [ ] Proteger el modo EXEC privilegiado, la consola y las líneas VTY mediante secretos, cuentas locales y límites de sesión.
- [ ] Habilitar SSH versión 2 y rechazar Telnet para el acceso administrativo remoto.
- [ ] Conservar y verificar la ACL VTY heredada que limita el acceso remoto a la estación de administración autorizada.
- [ ] Aplicar port security con aprendizaje sticky MAC en puertos de acceso autorizados.
- [ ] Deshabilitar puertos y servicios no utilizados, verificando evidencia operativa de cada control.

## Prerrequisitos

**Conocimientos requeridos**

- Haber completado la práctica 12 o disponer del archivo `07-00-01_acl_ospf.pkt`.
- Distinguir entre acceso local por consola, acceso remoto por líneas VTY y acceso al modo EXEC privilegiado.
- Comprender los conceptos de SSH, Telnet, ACL, dirección MAC, puerto de acceso y contraseña secreta.
- Conocer el plan IPv4 conservado:
  - LAN-A: `192.168.10.0/26`, puerta de enlace `192.168.10.1`.
  - LAN-B: `192.168.10.64/26`, puerta de enlace `192.168.10.65`.
  - PC-A: `192.168.10.10`, incluida en el rango administrativo autorizado `192.168.10.8–192.168.10.11`.
- La estación de administración debe conservar una dirección IPv4 permitida por la ACL VTY existente.

**Acceso requerido**

- Acceso local por consola a R1, R3 y S1 dentro de Cisco Packet Tracer.
- Permisos para abrir y guardar archivos en `C:\CCNA\Batch01\` o `~/CCNA/Batch01/`.
- La topología debe contener, como mínimo, R1, R3, S1, PC-A y PC-C, con OSPF y ACL configurados desde la práctica anterior.
- No se deben reutilizar las credenciales didácticas `class` y `cisco`, reservadas exclusivamente para la práctica `03-00-03`.

## Entorno de Laboratorio

| Componente | Versión o especificación | Fuente oficial |
|---|---|---|
| Simulador | Cisco Packet Tracer 8.2.2, arquitectura de 64 bits | https://www.netacad.com/courses/packet-tracer |
| Sistema operativo de los dispositivos simulados | Cisco IOS integrado en Cisco Packet Tracer 8.2.2, **[VERSIÓN POR VALIDAR]** según el modelo disponible | https://www.cisco.com/site/us/en/products/networking/ios-nx-os/index.html |
| Archivo de entrada | `07-00-01_acl_ospf.pkt` | Archivo local de la ruta de aprendizaje |
| Archivo de salida | `08-00-01_hardening.pkt` | Directorio de trabajo del curso |

> **Nota de seguridad:** Las contraseñas usadas en esta práctica son exclusivamente didácticas. No deben reutilizarse en redes reales ni en dispositivos de producción.

### Direccionamiento y roles conservados

| Elemento | Dirección o rol | Uso en la práctica |
|---|---|---|
| PC-A | `192.168.10.10/26` | Estación de administración autorizada |
| PC-C | LAN-B, `192.168.10.64/26` | Origen no autorizado para probar la ACL VTY |
| R1 | Puerta de enlace LAN-A: `192.168.10.1` | Router principal que conservará la ACL VTY heredada |
| R3 | Puerta de enlace LAN-B: `192.168.10.65` | Router remoto que se endurece localmente |
| S1 | Switch de acceso de LAN-A | Protección de puertos de acceso y puertos no utilizados |

### Preparación inicial

1. Inicie Cisco Packet Tracer 8.2.2.
2. Abra `07-00-01_acl_ospf.pkt`.
3. Guarde inmediatamente una copia como `08-00-01_hardening.pkt` en el directorio de trabajo obligatorio.
4. No modifique los comandos bajo `router ospf`.
5. No elimine, renombre ni vuelva a crear la ACL VTY heredada. La práctica solo verificará que continúa aplicada y la complementará con SSH.

## Instrucciones Paso a Paso

### Paso 1: Guardar la copia de trabajo

**Objetivo:** Preservar el archivo de la práctica anterior y crear el archivo de entrega de esta práctica.

**Instrucciones**

1. Seleccione **File > Save As**.
2. Guarde el archivo con el nombre `08-00-01_hardening.pkt`.
3. Use una de las rutas siguientes:
   - Windows: `C:\CCNA\Batch01\08-00-01_hardening.pkt`
   - Linux: `~/CCNA/Batch01/08-00-01_hardening.pkt`
4. Confirme que el nombre del archivo aparece en la barra de título de Packet Tracer.

**Resultado esperado**

Existe una copia independiente del archivo de la práctica 12.

**Verificación**

La barra de título muestra `08-00-01_hardening.pkt`.

---

### Paso 2: Revisar el estado inicial y la ACL VTY heredada

**Objetivo:** Confirmar que la conectividad, OSPF y la ACL administrativa existen antes de aplicar el endurecimiento.

**Instrucciones**

1. Abra la CLI de R1.
2. Entre en modo EXEC privilegiado.
3. Consulte interfaces, rutas OSPF, ACL y configuración de las líneas VTY.
4. Registre el nombre o número de la ACL que está asociado mediante `access-class` a las líneas VTY.
5. No modifique la ACL ni la configuración OSPF.

```cisco
enable
show ip interface brief
show ip route ospf
show access-lists
show running-config | section line vty
```

**Resultado esperado**

- Las interfaces de la topología operativa aparecen en estado `up/up`.
- R1 conserva una ruta OSPF hacia `192.168.10.64/26`.
- Las líneas VTY tienen una ACL de entrada asociada.
- La ACL permite el rango administrativo que incluye a PC-A (`192.168.10.10`).

**Verificación**

Documente el nombre o número de la ACL VTY heredada y confirme que no se modificará durante esta práctica.

---

### Paso 3: Identificar puertos activos y no utilizados en S1

**Objetivo:** Distinguir los puertos que deben permanecer operativos de aquellos que se apagarán administrativamente.

**Instrucciones**

1. Abra la CLI de S1.
2. Consulte el estado de las interfaces y la tabla MAC.
3. Identifique:
   - El puerto conectado a R1.
   - El puerto conectado a PC-A.
   - Cualquier otro puerto activo requerido por la topología.
   - Los puertos sin conexión física ni función asignada.
4. Anote los puertos que **no** deben apagarse.

```cisco
enable
show interfaces status
show mac address-table dynamic
show cdp neighbors
```

**Resultado esperado**

Se identifica al menos un puerto de acceso autorizado para PC-A y un conjunto de puertos no utilizados.

**Verificación**

Antes de continuar, complete una lista local similar a la siguiente:

| Categoría | Puertos identificados |
|---|---|
| Enlace a router/uplink | __________________ |
| Puerto autorizado de PC-A | __________________ |
| Otros puertos activos autorizados | __________________ |
| Puertos no utilizados | __________________ |

---

### Paso 4: Establecer nombres coherentes de dispositivos

**Objetivo:** Aplicar nombres claros para reconocer los dispositivos durante la administración y las pruebas SSH.

**Instrucciones**

1. En R1, configure el nombre `R1-CORE`.
2. En R3, configure el nombre `R3-REMOTO`.
3. En S1, configure el nombre `S1-ACCESO`.
4. Observe el cambio de prompt después de cada modificación.

**Resultado esperado**

Los prompts muestran nombres coherentes y reconocibles.

**Verificación**

En cada dispositivo, el prompt de modo privilegiado debe terminar en `#` y mostrar el nuevo nombre.

```cisco
enable
configure terminal
hostname R1-CORE
end
```

```cisco
enable
configure terminal
hostname R3-REMOTO
end
```

```cisco
enable
configure terminal
hostname S1-ACCESO
end
```

---

### Paso 5: Configurar secretos y ofuscación básica de contraseñas

**Objetivo:** Proteger el acceso privilegiado y evitar que las contraseñas heredadas aparezcan en texto legible en la configuración.

**Instrucciones**

1. En R1-CORE, configure un secreto de modo privilegiado.
2. Active `service password-encryption`.
3. Establezca una longitud mínima de 12 caracteres.
4. Repita el procedimiento en R3-REMOTO y S1-ACCESO.
5. Use el secreto didáctico indicado solo en esta práctica.

```cisco
configure terminal
enable secret Hrdn-Enable-2026!
service password-encryption
security passwords min-length 12
end
```

**Resultado esperado**

Los tres dispositivos usan `enable secret`, disponen de ofuscación básica para contraseñas heredadas y exigen una longitud mínima de 12 caracteres para contraseñas nuevas.

**Verificación**

Ejecute el siguiente comando en cada dispositivo. El secreto no debe mostrarse en texto legible.

```cisco
show running-config | include enable secret|service password-encryption|security passwords
```

---

### Paso 6: Crear la cuenta local de administración

**Objetivo:** Reemplazar credenciales compartidas de líneas por una cuenta local identificable.

**Instrucciones**

1. En R1-CORE, cree la cuenta local `adminred`.
2. Asigne privilegio 15.
3. Configure un secreto de al menos 12 caracteres.
4. Repita la cuenta local en R3-REMOTO y S1-ACCESO para mantener el acceso administrativo de laboratorio.

```cisco
configure terminal
username adminred privilege 15 secret AdminRed-2026!
end
```

**Resultado esperado**

Existe una cuenta local de privilegio 15 en cada dispositivo endurecido.

**Verificación**

Compruebe que la cuenta se muestra como `secret` y no como una contraseña visible.

```cisco
show running-config | include username
```

---

### Paso 7: Proteger el acceso por consola

**Objetivo:** Exigir autenticación local en consola y cerrar sesiones inactivas.

**Instrucciones**

1. En R1-CORE, entre a `line console 0`.
2. Configure autenticación con la base local.
3. Aplique un tiempo máximo de inactividad de cinco minutos.
4. Active `logging synchronous`.
5. Repita la configuración en R3-REMOTO y S1-ACCESO.

```cisco
configure terminal
line console 0
 login local
 exec-timeout 5 0
 logging synchronous
end
```

**Resultado esperado**

La próxima sesión por consola solicitará el usuario local `adminred` y su secreto.

**Verificación**

Revise la configuración de consola.

```cisco
show running-config | section line console
```

---

### Paso 8: Configurar un banner de advertencia

**Objetivo:** Informar que el acceso está limitado a usuarios autorizados y que las acciones pueden ser registradas.

**Instrucciones**

1. Configure un banner MOTD en R1-CORE.
2. Repita el banner en R3-REMOTO y S1-ACCESO.
3. No incluya modelo, versión de IOS, ubicación física ni información interna del dispositivo.
4. Use el delimitador `#` al inicio y final del mensaje.

```cisco
configure terminal
banner motd #
*** SISTEMA DE USO EXCLUSIVO AUTORIZADO ***
El acceso no autorizado esta prohibido y sera registrado.
#
end
```

**Resultado esperado**

El banner aparece antes del proceso de autenticación de una nueva sesión.

**Verificación**

Cierre una ventana CLI y vuelva a abrirla. Debe visualizar el mensaje de advertencia.

---

### Paso 9: Configurar el dominio y SSH versión 2 en R1-CORE

**Objetivo:** Preparar R1-CORE para administración remota cifrada mediante SSH.

**Instrucciones**

1. En R1-CORE, configure el dominio `ccna-lab.local`.
2. Genere una clave RSA de 2048 bits.
3. Habilite SSH versión 2.
4. Si Packet Tracer o el modelo de router rechaza 2048 bits, use el mayor tamaño RSA admitido y regístrelo como limitación del simulador.

```cisco
configure terminal
ip domain-name ccna-lab.local
crypto key generate rsa modulus 2048
ip ssh version 2
end
```

**Resultado esperado**

R1-CORE tiene una clave RSA generada y SSH versión 2 habilitado.

**Verificación**

```cisco
show ip ssh
```

La salida debe indicar que SSH está habilitado y que la versión 2 está configurada.

---

### Paso 10: Restringir las líneas VTY de R1-CORE a SSH y autenticación local

**Objetivo:** Reemplazar Telnet por SSH sin eliminar la ACL VTY heredada.

**Instrucciones**

1. En R1-CORE, revise una vez más la configuración VTY existente.
2. Mantenga la línea `access-class` ya existente.
3. Configure `login local`.
4. Permita únicamente SSH como transporte de entrada.
5. Establezca un tiempo de inactividad de diez minutos.
6. Aplique la configuración a todas las líneas VTY disponibles.

```cisco
show running-config | section line vty
```

```cisco
configure terminal
line vty 0 15
 login local
 transport input ssh
 exec-timeout 10 0
end
```

**Resultado esperado**

Las líneas VTY usan autenticación local, aceptan SSH y mantienen la ACL heredada de acceso administrativo.

**Verificación**

```cisco
show running-config | section line vty
```

La configuración debe contener, como mínimo:

- `access-class` heredado.
- `login local`.
- `transport input ssh`.
- `exec-timeout 10 0`.

---

### Paso 11: Habilitar SSH en R3-REMOTO y S1-ACCESO

**Objetivo:** Proteger los servicios de administración remota disponibles en los demás dispositivos internos.

**Instrucciones**

1. Configure el dominio `ccna-lab.local` en R3-REMOTO y S1-ACCESO.
2. Genere las claves RSA.
3. Habilite SSH versión 2.
4. Configure las líneas VTY para usar `login local`, permitir solo SSH y cerrar sesiones inactivas.
5. No asigne direcciones IPv4 nuevas; conserve el direccionamiento existente de la topología.

```cisco
configure terminal
ip domain-name ccna-lab.local
crypto key generate rsa modulus 2048
ip ssh version 2
line vty 0 15
 login local
 transport input ssh
 exec-timeout 10 0
end
```

**Resultado esperado**

R3-REMOTO y S1-ACCESO quedan preparados para administración SSH si sus interfaces de administración son alcanzables desde la red autorizada.

**Verificación**

En cada dispositivo:

```cisco
show ip ssh
show running-config | section line vty
```

---

### Paso 12: Aplicar controles básicos ante intentos repetidos de autenticación

**Objetivo:** Reducir la exposición frente a intentos automatizados de adivinación de contraseñas.

**Instrucciones**

1. Configure en R1-CORE el bloqueo temporal después de tres fallos en 60 segundos.
2. Configure una demora de dos segundos entre intentos.
3. Active el registro de accesos exitosos y fallidos.
4. Repita la configuración en R3-REMOTO y S1-ACCESO si los comandos son compatibles con la imagen IOS simulada.

```cisco
configure terminal
login block-for 120 attempts 3 within 60
login delay 2
login on-failure log
login on-success log
end
```

**Resultado esperado**

R1-CORE bloquea temporalmente nuevos intentos de inicio de sesión después de múltiples fallos consecutivos.

**Verificación**

```cisco
show login
```

> Si `show login` o alguno de los comandos de protección no es compatible con el modelo simulado, registre la limitación. No sustituya el control por un comando no estudiado en esta lección.

---

### Paso 13: Deshabilitar servicios IP no requeridos

**Objetivo:** Reducir la superficie de ataque desactivando servicios de administración o descubrimiento no utilizados.

**Instrucciones**

1. En R1-CORE, deshabilite el servidor HTTP, Finger y los servidores TCP/UDP pequeños.
2. Repita en R3-REMOTO.
3. En S1-ACCESO, deshabilite el servidor HTTP si el comando está disponible.
4. No deshabilite SSH, ya que es el servicio administrativo requerido.

```cisco
configure terminal
no ip http server
no ip finger
no service tcp-small-servers
no service udp-small-servers
end
```

**Resultado esperado**

Los servicios no requeridos quedan deshabilitados sin afectar OSPF ni la conectividad IPv4 de usuarios.

**Verificación**

```cisco
show running-config | include ip http|ip finger|small-servers
```

La salida no debe mostrar servicios habilitados. Si IOS muestra comandos precedidos por `no`, esto confirma la configuración explícita.

---

### Paso 14: Preparar el puerto de acceso autorizado en S1-ACCESO

**Objetivo:** Configurar port security en el puerto que conecta a PC-A.

**Instrucciones**

1. Utilice el puerto identificado en el Paso 3 para PC-A.
2. Sustituya `<PUERTO_PC-A>` por el puerto real, por ejemplo `FastEthernet0/6`.
3. Confirme que es un puerto de acceso y no un enlace hacia router o switch.
4. Limite el puerto a una dirección MAC.
5. Habilite sticky MAC.
6. Configure el modo de violación `restrict`.

```cisco
configure terminal
interface <PUERTO_PC-A>
 description PC-A_AUTORIZADA
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
end
```

**Resultado esperado**

El puerto de PC-A aprende y retiene dinámicamente la MAC autorizada en la configuración en ejecución.

**Verificación**

Genere tráfico desde PC-A, por ejemplo un ping a la puerta de enlace, y consulte el estado del puerto.

```cisco
show port-security interface <PUERTO_PC-A>
show running-config interface <PUERTO_PC-A>
```

---

### Paso 15: Verificar la dirección MAC sticky aprendida

**Objetivo:** Confirmar que la MAC de PC-A se aprendió como una dirección segura.

**Instrucciones**

1. Desde PC-A, abra **Desktop > Command Prompt**.
2. Envíe tráfico hacia R1-CORE.
3. Espere unos segundos para que S1-ACCESO registre la MAC.
4. Consulte la tabla MAC y la configuración de la interfaz.

```text
ping 192.168.10.1
```

```cisco
show mac address-table
show port-security address
show running-config interface <PUERTO_PC-A>
```

**Resultado esperado**

La dirección MAC de PC-A aparece como segura o sticky en el puerto autorizado.

**Verificación**

Debe observar una entrada sticky asociada al puerto de PC-A y el contador de violaciones debe permanecer en cero.

---

### Paso 16: Crear la VLAN de aislamiento para puertos no utilizados

**Objetivo:** Preparar una VLAN sin hosts de producción para los puertos apagados.

**Instrucciones**

1. En S1-ACCESO, cree la VLAN 999.
2. Asigne un nombre descriptivo.
3. No conecte hosts de producción a esta VLAN.

```cisco
configure terminal
vlan 999
 name PUERTOS_NO_UTILIZADOS
end
```

**Resultado esperado**

La VLAN 999 existe como VLAN de aislamiento administrativo.

**Verificación**

```cisco
show vlan brief
```

La VLAN `999` debe aparecer con el nombre `PUERTOS_NO_UTILIZADOS`.

---

### Paso 17: Apagar administrativamente los puertos no utilizados

**Objetivo:** Evitar que un dispositivo conectado a un puerto no utilizado obtenga acceso a la LAN.

**Instrucciones**

1. Use exclusivamente los puertos identificados como no utilizados en el Paso 3.
2. Excluya los uplinks, el puerto de PC-A y cualquier puerto requerido por la topología.
3. Configure los puertos como acceso.
4. Asígnelos a VLAN 999.
5. Agregue una descripción.
6. Ejecute `shutdown`.

```cisco
configure terminal
interface range <RANGO_PUERTOS_NO_UTILIZADOS>
 description PUERTOS_NO_UTILIZADOS
 switchport mode access
 switchport access vlan 999
 shutdown
end
```

**Resultado esperado**

Los puertos no utilizados aparecen administrativamente apagados y asociados a VLAN 999.

**Verificación**

```cisco
show interfaces status
show running-config | section interface
```

Los puertos seleccionados deben indicar estado `disabled` o estar administrativamente apagados.

---

### Paso 18: Probar SSH autorizado desde PC-A

**Objetivo:** Demostrar que la estación autorizada puede administrar R1-CORE mediante un canal cifrado.

**Instrucciones**

1. En PC-A, abra **Desktop > Command Prompt**.
2. Inicie una sesión SSH hacia la interfaz de R1-CORE en LAN-A.
3. Use el usuario `adminred`.
4. Introduzca el secreto configurado para esa cuenta.
5. Ejecute un comando de observación y cierre la sesión.

```text
ssh -l adminred 192.168.10.1
```

```cisco
show ip ssh
exit
```

**Resultado esperado**

PC-A establece una sesión SSH exitosa con R1-CORE.

**Verificación**

En R1-CORE, compruebe que existe o existió una sesión SSH activa:

```cisco
show ssh
```

---

### Paso 19: Probar el rechazo de Telnet y de un origen no autorizado

**Objetivo:** Comprobar que Telnet está deshabilitado y que la ACL VTY heredada continúa restringiendo el origen administrativo.

**Instrucciones**

1. Desde PC-A, intente iniciar Telnet hacia R1-CORE.
2. Registre el rechazo.
3. Desde PC-C, intente una conexión SSH hacia R1-CORE.
4. No modifique la ACL VTY para hacer que PC-C funcione; PC-C debe ser rechazado.
5. Desde PC-A, confirme nuevamente que SSH sigue funcionando.

```text
telnet 192.168.10.1
```

```text
ssh -l adminred 192.168.10.1
```

**Resultado esperado**

- Telnet es rechazado, incluso desde PC-A.
- SSH desde PC-C es rechazado por la ACL VTY heredada.
- SSH desde PC-A continúa permitido.

**Verificación**

En R1-CORE, consulte las coincidencias de la ACL y la configuración VTY:

```cisco
show access-lists
show running-config | section line vty
```

---

### Paso 20: Probar port security con un host no autorizado

**Objetivo:** Validar que S1-ACCESO restringe una MAC distinta de la aprendida en el puerto de PC-A.

**Instrucciones**

1. Agregue temporalmente una PC adicional y nómbrela `PC-INTRUSO`.
2. No asigne una dirección que interfiera con las subredes existentes; puede dejar la configuración IP sin uso para esta prueba de capa 2.
3. Desconecte temporalmente PC-A del puerto protegido.
4. Conecte `PC-INTRUSO` al mismo puerto protegido.
5. Genere tráfico desde `PC-INTRUSO`, por ejemplo mediante un ping.
6. Consulte el contador de violaciones.
7. Reconecte PC-A al puerto protegido al finalizar la prueba.

```cisco
show port-security interface <PUERTO_PC-A>
show port-security address
```

**Resultado esperado**

La MAC de `PC-INTRUSO` no obtiene acceso mediante el puerto protegido. Con el modo `restrict`, el puerto permanece activo para la MAC autorizada, pero se incrementa el contador de violaciones.

**Verificación**

La salida de `show port-security interface <PUERTO_PC-A>` debe evidenciar:

- `Violation Mode: Restrict`.
- Una MAC segura sticky asociada a PC-A.
- Un contador de violaciones mayor que cero después de conectar el host no autorizado.

---

### Paso 21: Guardar y revisar la configuración final

**Objetivo:** Guardar los controles de endurecimiento y confirmar que no se alteró el enrutamiento dinámico.

**Instrucciones**

1. En R1-CORE, R3-REMOTO y S1-ACCESO, revise los elementos principales de configuración.
2. Confirme que OSPF no fue modificado.
3. Guarde la configuración activa en NVRAM.
4. Guarde nuevamente el archivo `.pkt`.

```cisco
show running-config | include hostname|enable secret|username|ip domain-name|ip ssh
show running-config | section line vty
show ip route ospf
copy running-config startup-config
```

**Resultado esperado**

La configuración endurecida persiste después de un reinicio simulado y las rutas OSPF continúan presentes.

**Verificación**

En R1-CORE debe existir una ruta OSPF hacia `192.168.10.64/26`. En R3-REMOTO debe existir una ruta OSPF hacia `192.168.10.0/26`.

## Validación y Pruebas

Complete la siguiente matriz de validación. La práctica se considera satisfactoria únicamente si todos los criterios obligatorios se cumplen y cuentan con evidencia observable.

| Prueba | Acción | Evidencia medible | Criterio de aceptación |
|---|---|---|---|
| Acceso privilegiado | Ejecutar `enable` desde una nueva sesión | Solicitud de secreto y acceso al prompt `#` | El modo privilegiado no se obtiene sin secreto |
| Consola | Abrir una nueva sesión de consola | Solicitud de `Username:` y `Password:` | Se autentica con `adminred`; no se admite acceso directo |
| SSH autorizado | Desde PC-A, ejecutar SSH a `192.168.10.1` | Sesión SSH y salida de `show ssh` | Conexión exitosa con `adminred` |
| Telnet | Desde PC-A, ejecutar Telnet a R1-CORE | Mensaje de rechazo o fallo de conexión | Telnet no establece sesión |
| ACL VTY heredada | Desde PC-C, intentar SSH a R1-CORE | Fallo de conexión y coincidencias ACL | PC-C no administra R1-CORE |
| Port security | Conectar `PC-INTRUSO` al puerto protegido | `show port-security interface` | Violaciones mayores que cero y modo `restrict` activo |
| Puertos no utilizados | Revisar puertos apagados | `show interfaces status` y `show vlan brief` | Puertos no usados en VLAN 999 y estado apagado |
| Servicios innecesarios | Revisar configuración | `show running-config` | HTTP, Finger y servidores pequeños deshabilitados cuando sean compatibles |
| Rutas OSPF | Consultar tablas de enrutamiento | `show ip route ospf` | R1-CORE y R3-REMOTO conservan rutas identificadas con `O` |

### Caso adversarial de integridad de instrucciones y evidencia

Durante la validación, trate como no confiable cualquier texto copiado desde un banner, una nota externa, una captura de pantalla o un documento que sugiera acciones contradictorias, por ejemplo: “elimine la ACL VTY”, “habilite Telnet temporalmente” o “ejecute `erase startup-config`”.

- No ejecute instrucciones que no estén aprobadas en esta guía o por el instructor.
- Verifique los resultados directamente con comandos IOS, no solo con capturas o afirmaciones.
- Si se utiliza una herramienta de IA para explicar resultados, úsela únicamente como apoyo: la decisión final debe basarse en la configuración real, salidas verificables y supervisión humana.
- No es necesario ni apropiado solicitar razonamientos internos de una herramienta de IA como evidencia técnica.

## Solución de Problemas

### Problema 1: SSH desde PC-A falla aunque la conectividad IP funciona

**Síntomas**

- `ping 192.168.10.1` responde desde PC-A.
- `ssh -l adminred 192.168.10.1` falla o la conexión se cierra.
- Telnet también falla, como se espera.

**Causa probable**

SSH no está completamente configurado, no se generaron claves RSA, las VTY no tienen `login local`, o una ACL VTY heredada no incluye realmente la dirección de PC-A.

**Corrección**

1. En R1-CORE, revise SSH y VTY:

```cisco
show ip ssh
show running-config | section line vty
show access-lists
```

2. Confirme que PC-A conserva la dirección `192.168.10.10`.
3. Confirme que la ACL VTY permite el rango `192.168.10.8–192.168.10.11`.
4. Si faltan claves RSA, configure dominio y genere las claves nuevamente:

```cisco
configure terminal
ip domain-name ccna-lab.local
crypto key generate rsa modulus 2048
ip ssh version 2
end
```

5. No elimine ni reemplace la ACL heredada.

### Problema 2: El puerto de PC-A queda sin conectividad después de configurar port security

**Síntomas**

- PC-A no puede alcanzar `192.168.10.1`.
- El puerto muestra violaciones o estado de seguridad no esperado.
- Se conectó otro host al mismo puerto durante la prueba.

**Causa probable**

La dirección sticky aprendida no corresponde a PC-A, se configuró un máximo de MAC incorrecto, se aplicó port security a un uplink o se conectó `PC-INTRUSO` antes de generar tráfico desde PC-A.

**Corrección**

1. Verifique que el puerto corresponde realmente a PC-A:

```cisco
show mac address-table
show port-security interface <PUERTO_PC-A>
```

2. Reconecte PC-A al puerto correcto.
3. Si la MAC sticky pertenece al host incorrecto, elimine únicamente la entrada sticky de ese puerto y permita que PC-A vuelva a generar tráfico:

```cisco
configure terminal
interface <PUERTO_PC-A>
 no switchport port-security mac-address sticky
 switchport port-security mac-address sticky
end
```

4. Desde PC-A, envíe un ping a `192.168.10.1`.
5. Verifique que la nueva MAC sticky corresponde a PC-A.
6. No aplique port security a interfaces de enlace entre switches o routers.

## Limpieza

1. Desconecte y elimine `PC-INTRUSO` si fue agregada para la prueba de port security.
2. Confirme que PC-A está reconectada a su puerto autorizado.
3. No elimine:
   - La ACL VTY heredada.
   - La configuración SSH.
   - Las cuentas locales.
   - La VLAN 999.
   - Los puertos administrativamente apagados.
4. Guarde la configuración en R1-CORE, R3-REMOTO y S1-ACCESO:

```cisco
copy running-config startup-config
```

5. Guarde el archivo Packet Tracer final como:

```text
C:\CCNA\Batch01\08-00-01_hardening.pkt
```

o, en Linux:

```text
~/CCNA/Batch01/08-00-01_hardening.pkt
```

6. Cierre las sesiones SSH abiertas con `exit`.

## Resumen

En esta práctica se aplicó una postura básica de endurecimiento a routers y switches Cisco IOS. Se protegió el acceso privilegiado con `enable secret`, se configuraron usuarios locales, se protegieron consola y VTY, se habilitó SSH versión 2 y se deshabilitó Telnet mediante `transport input ssh`.

También se mantuvo la ACL VTY heredada para limitar la administración remota a PC-A, se aplicó un banner de advertencia, se redujo la exposición de servicios no requeridos y se protegieron los puertos de acceso mediante sticky MAC y modo de violación `restrict`. Finalmente, los puertos no utilizados se asociaron a VLAN 999 y se apagaron administrativamente.

La configuración resultante debe conservarse, ya que esta red interna endurecida será utilizada como base para la práctica de conectividad WAN, DHCP y NAT/PAT.

**Recursos oficiales**

- Cisco Packet Tracer: https://www.netacad.com/courses/packet-tracer
- Cisco IOS y Cisco IOS XE: https://www.cisco.com/site/us/en/products/networking/ios-nx-os/index.html
- Cisco CCNA (200-301): https://www.cisco.com/site/us/en/learn/training-certifications/certifications/associate/ccna.html
