# SETUP GUIDE GENERAL — CCNA

**Curso:** Implementación y administración de soluciones Cisco (CCNA)  
**Repositorio:** <https://github.com/Netec-Mx/CCNA>  
**Versión del documento:** 1.0  
**Fecha de revisión documental:** 4 de noviembre de 2026  
**Revisión analizada:** `1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8`  
**Cobertura:** README principal, SETUP_GUIDE.md existente, nueve capítulos completos con catorce prácticas, directorio de recursos y metadatos del repositorio.  
**Estado:** revisión documental completada. La aceptación funcional requiere ejecutar las validaciones de esta guía en el equipo del instructor y resolver los archivos base pendientes.

# Objetivo

Preparar un entorno común para construir, abrir, configurar, simular y guardar las redes del repositorio con **Cisco Packet Tracer Desktop 8.2.2 de 64 bits**. Esta guía reúne únicamente requisitos, instalación, configuración inicial y comprobaciones del entorno.

El componente compartido por todas las actividades es Packet Tracer, con sus routers, switches, estaciones y servidores integrados. Las topologías evolucionan y algunas se crean de forma independiente; no existe una única red preconfigurada que deba utilizarse para todo el curso.

# Requisitos de Hardware

| Recurso | Requisito consolidado |
|---|---|
| Equipo | Una computadora de escritorio o portátil por participante o pareja. |
| Procesador | Arquitectura x86-64/AMD64, compatible con el instalador de 64 bits. |
| Memoria | Al menos **4 GB de RAM libres** para ejecutar el simulador. El repositorio recomienda **8 GB de RAM instalada**; comprobar que el sistema y las demás aplicaciones permiten conservar los 4 GB disponibles. |
| Almacenamiento | Al menos **2 GB libres** para la instalación, más espacio para el instalador, documentos y archivos `.pkt`. |
| Pantalla | El repositorio menciona 1366 × 768; se recomienda **1920 × 1080** para evitar recortes de ventanas, conforme a la referencia actual de requisitos de Cisco. |
| Periféricos | Teclado y mouse. |
| Conectividad | Internet para descargar materiales, instalar dependencias y autenticar la cuenta Cisco cuando se solicite. |

Los dispositivos, cables de consola y cables Ethernet del curso son objetos simulados dentro de Packet Tracer. No se requiere adquirir routers, switches, adaptadores de consola ni cableado físico.

# Sistemas Operativos Compatibles

El repositorio proporciona rutas y comandos para Windows y Linux. Se establecen las siguientes bases de preparación:

| Sistema | Base de esta guía | Consideraciones |
|---|---|---|
| Windows | **Windows 11 de 64 bits, x86-64** | Instalar el paquete Windows de Packet Tracer 8.2.2. Tener permisos de instalación y escritura en la carpeta de trabajo. |
| Linux | **Ubuntu Desktop 22.04 LTS de 64 bits, AMD64** | Instalar el paquete `.deb` de Packet Tracer 8.2.2 con sus dependencias. Se requiere sesión gráfica y acceso a `sudo` durante la instalación. |

Estas versiones de sistema operativo son una selección de preparación compatible con las referencias de Cisco, no versiones impuestas explícitamente por los capítulos. Windows 10 aparece en la compatibilidad de Cisco, pero no es la base seleccionada para una nueva instalación de este documento.

La existencia de un instalador para otra plataforma no equivale a que las instrucciones del repositorio hayan sido validadas en ella. Esta guía desarrolla las dos plataformas utilizadas en los materiales.

# Software Requerido

| Componente | Versión o criterio | Obtención y validación |
|---|---|---|
| Cisco Packet Tracer Desktop | **8.2.2, 64 bits**, uniforme para todo el grupo | Descargar desde Cisco Networking Academy. Comprobar la versión en **Help > About**. |
| Cisco IOS simulado | El integrado en cada modelo de Packet Tracer 8.2.2 | Identificarlo con `show version`. El repositorio no fija una versión exacta de IOS común a todos los dispositivos. |
| Navegador del equipo anfitrión | Versión con soporte del fabricante, con JavaScript y cookies de autenticación permitidos | Acceso a GitHub, documentación y autenticación de Cisco. No hay un navegador ni una versión concreta exigidos por el repositorio. |
| PowerShell o CMD, en Windows | Herramientas incluidas en el sistema | Crear directorios y comprobar recursos y archivos. |
| Terminal Bash y APT, en Ubuntu | Herramientas del sistema | Instalar el paquete oficial, resolver dependencias y comprobar archivos. |

**Control de versión:** 8.2.2 es la versión fijada por el curso, no una afirmación de que sea la versión más reciente de Packet Tracer. No sustituirla automáticamente por una versión nueva. Cisco advierte que los archivos guardados con versiones posteriores no son compatibles hacia versiones anteriores. Si se decide actualizar el curso, validar primero todos los archivos base y uniformar la versión del grupo.

# Herramientas y Dependencias

## Catálogo de dispositivos del simulador

Comprobar que Packet Tracer permite agregar los siguientes objetos:

| Objeto | Capacidad necesaria |
|---|---|
| Switch Cisco Catalyst 2960 | Puertos FastEthernet y GigabitEthernet; consola; VLAN, SVI, trunk 802.1Q, STP, LACP y port security dentro de las funciones simuladas. |
| Router Cisco ISR 1941 | Dos interfaces GigabitEthernet para las topologías iniciales que indican ese modelo. |
| Router Cisco 2911 | Tres interfaces GigabitEthernet para las topologías que utilizan `G0/0`, `G0/1` y `G0/2`; el escenario WAN especifica expresamente este modelo. |
| PC-PT | FastEthernet, RS232, Terminal, Command Prompt, navegador web y configuración IPv4/IPv6. |
| Server-PT | FastEthernet y servicios HTTP, DNS y DHCP integrados. |
| Conexiones | Cable Console, Copper Straight-Through y Copper Cross-Over; conexión automática cuando el material lo permita. |

El modelo 1941 no dispone de las tres interfaces GigabitEthernet requeridas por las topologías de enrutamiento posteriores. Para las topologías nuevas que exijan tres interfaces, seleccionar **2911** desde su construcción inicial. No reemplazar un router de un archivo heredado sin preservar y validar su configuración.

Las imágenes IOS, la CLI, la terminal de consola, el cliente SSH, el navegador de las PCs y el análisis de PDU están incluidos en Packet Tracer. No requieren descargas independientes. Los números de versión de IEEE, RFC y SSH que aparecen en los materiales describen funciones o protocolos; no son paquetes adicionales que deban instalarse.

## Dependencias del sistema

En Ubuntu, permitir que APT instale las bibliotecas declaradas por el paquete oficial. No copiar bibliotecas de otras distribuciones ni forzar la instalación ignorando dependencias. Mantener disponibles las fuentes Unicode y los controladores gráficos del sistema.

La descarga del repositorio puede realizarse con **Download ZIP** desde GitHub; Git no es obligatorio. No se necesitan lenguajes de programación, SDK, IDE, herramientas de captura externas, bases de datos ni plataformas cloud para preparar este repositorio.

# Instalación y Configuración

## 1. Preparar los accesos

1. Disponer de una cuenta personal Cisco Networking Academy y comprobar el inicio de sesión en <https://www.netacad.com/>.
2. Acceder al [Resource Hub oficial](https://www.netacad.com/resources/lab-downloads) y localizar el instalador **8.2.2 de 64 bits** correspondiente al sistema operativo.
3. Si el portal requiere una inscripción, utilizar el curso oficial [Getting Started with Cisco Packet Tracer](https://www.netacad.com/courses/getting-started-cisco-packet-tracer/1000).
4. Si el portal solo ofrece versiones recientes, obtener del instructor un medio de instalación autorizado de 8.2.2. La disponibilidad actual de ese instalador detrás del inicio de sesión no se confirmó en esta revisión.
5. Consultar el aviso oficial [Login guidance for Cisco Packet Tracer](https://www.netacad.com/articles/news/upcoming-login-change-for-cisco-packet-tracer), aplicable a 8.2.2, si el acceso antiguo de NetAcad o Skills for All presenta errores. El repositorio conserva denominaciones anteriores de ambas plataformas.

No guardar contraseñas Cisco en esta guía, en notas de la topología ni en archivos del repositorio. En equipos compartidos, evitar mantener una sesión personal persistente.

## 2. Instalar en Windows

1. Ejecutar el instalador oficial de Packet Tracer 8.2.2 para Windows de 64 bits.
2. Aceptar la licencia y completar la instalación con los permisos necesarios.
3. Registrar la ruta de instalación elegida y abrir **Cisco Packet Tracer 8.2.2** desde el menú Inicio.
4. Completar la autenticación solicitada y confirmar **8.2.2** en **Help > About**.
5. Si hay más de una versión instalada, comprobar que los `.pkt` se abren con 8.2.2; utilizar **File > Open** desde esa aplicación para evitar una asociación incorrecta.

La carpeta de trabajo del curso es `C:\CCNA\Batch01\`. Debe permitir guardar archivos con el usuario habitual; no ejecutar el simulador como administrador para el trabajo cotidiano.

En PowerShell:

```powershell
New-Item -ItemType Directory -Force -Path 'C:\CCNA\Batch01' | Out-Null
Set-Location -LiteralPath 'C:\CCNA\Batch01'
'OK' | Set-Content -LiteralPath 'C:\CCNA\Batch01\_prueba_escritura.txt'
Get-Content -LiteralPath 'C:\CCNA\Batch01\_prueba_escritura.txt'
Remove-Item -LiteralPath 'C:\CCNA\Batch01\_prueba_escritura.txt'
```

**Resultado esperado:** la carpeta existe, se imprime `OK` y el archivo temporal se elimina sin errores de permisos. Si la creación en `C:\` requiere elevación, el administrador debe crear la carpeta y dar al participante permisos de modificación sobre ella.

## 3. Instalar en Ubuntu Desktop 22.04 LTS

Guardar el paquete oficial de la versión requerida en Descargas. Para utilizar los comandos siguientes sin depender del nombre publicado del instalador, renombrar **ese archivo `.deb`** como `CiscoPacketTracer_8.2.2_amd64.deb` y ubicarlo en `$HOME/Downloads/`. Esta es una convención local de esta guía.

```bash
sudo apt-get update
sudo apt-get install "$HOME/Downloads/CiscoPacketTracer_8.2.2_amd64.deb"
```

Aceptar la licencia cuando se solicite. Si la carpeta de descargas tiene otro nombre, adaptar la ruta al archivo real. El procedimiento de instalación con APT y el comando de inicio están documentados por Cisco.

Crear el directorio del curso y comprobar escritura:

```bash
mkdir -p "$HOME/CCNA/Batch01"
cd "$HOME/CCNA/Batch01"
printf '%s\n' 'OK' > "$HOME/CCNA/Batch01/_prueba_escritura.txt"
cat "$HOME/CCNA/Batch01/_prueba_escritura.txt"
rm "$HOME/CCNA/Batch01/_prueba_escritura.txt"
packettracer
```

**Resultado esperado:** se imprime `OK` y se abre la aplicación gráfica. Autenticarse y confirmar **8.2.2** en **Help > About**. No iniciar Packet Tracer con `sudo`.

## 4. Descargar los materiales completos

1. Abrir <https://github.com/Netec-Mx/CCNA>.
2. Seleccionar **Code > Download ZIP** y extraer el contenido.
3. Conservar localmente `README.md`, `SETUP_GUIDE.md`, `Capitulo01` a `Capitulo09` y `assets`.
4. Guardar los archivos de simulación en el directorio común, separados de la copia de documentación descargada.

El enlace a `Netec-Mx/261103-CCNA` que aparece en el SETUP_GUIDE original es inconsistente con el repositorio analizado. Para esta preparación, utilizar **Netec-Mx/CCNA**.

# Configuración Base para los Laboratorios

## Ajustes comunes del simulador

- Trabajar inicialmente en el espacio **Logical** y modo **Realtime**.
- Confirmar el acceso a **Simulation**, **Event List** y detalles de PDU. Los filtros de protocolos se ajustarán cuando los materiales lo requieran.
- Ante la pregunta de iniciar Multiuser, seleccionar **No** para esta preparación individual.
- Mantener visibles los nombres de dispositivos, interfaces y estados de los enlaces.
- En dispositivos nuevos, esperar el arranque de IOS; si se ofrece el diálogo inicial de configuración, utilizar la CLI indicada en los materiales.
- No precargar todas las VLAN, rutas, ACL, cuentas o reglas NAT en una plantilla universal: los estados iniciales y el direccionamiento cambian entre archivos.

## Consola simulada

Conectar **RS232 de PC-PT** al puerto **Console** del router o switch mediante el cable **Console**. En la PC, abrir **Desktop > Terminal** y utilizar:

| Parámetro | Valor |
|---|---|
| Velocidad | 9600 bit/s |
| Bits de datos | 8 |
| Paridad | None |
| Bits de parada | 1 |
| Control de flujo | None |

La CLI también puede abrirse desde la pestaña **CLI** del dispositivo. La terminal de PC-PT es necesaria para comprobar la conexión de consola solicitada por los materiales iniciales.

## Archivos base y continuidad

**El repositorio analizado no contiene archivos `.pkt` ni `.pka`.** Sus README describen archivos que se generan durante el curso y un archivo inicial que debe proporcionar el instructor. Descargar el ZIP de GitHub no cubre por sí solo todos los prerrequisitos.

Antes del curso, el instructor debe preparar lo siguiente:

| Dependencia | Preparación necesaria |
|---|---|
| `P9_Troubleshooting_Inicial.pkt` | Entregar el archivo inicial indicado por el material, creado y abierto correctamente con 8.2.2. No sustituirlo por una topología vacía ni corregir sus fallas intencionales durante el setup. |
| Bases heredadas | Conservar los `.pkt` producidos durante el curso y sus copias de respaldo. Si se permite comenzar desde una etapa intermedia, entregar la base correspondiente ya validada. |
| `06-00-02_ospf_base.pkt` | Tratarlo como nombre alternativo del archivo generado `06-00-02_ospf_intro.pkt`; comprobar contenido y estado de OSPF antes de usarlo. |
| `07-00-01_acl_ospf.pkt` | El archivo de salida documentado previamente se llama `07-00-01_acl_fundamentos.pkt`. Tras comprobar que es la base correcta, crear una copia con el nombre esperado por el material posterior, preservando el original. |
| `08-00-01_hardening_base.pkt` | El archivo generado anteriormente se llama `08-00-01_hardening.pkt`. La base para WAN requiere además la comprobación de interfaces descrita a continuación; copiar o renombrar el archivo no resuelve por sí solo la topología. |

Al iniciar una actividad que reutiliza una base, abrir la fuente, utilizar **File > Save As** y trabajar sobre una copia con el nombre indicado. Conservar las ramas independientes: por ejemplo, el archivo de servicios de transporte/aplicación no sustituye al archivo IPv4 original que otra actividad reutiliza.

## Compatibilidad de nombres, modelos e interfaces

| Diferencia detectada | Criterio de preparación |
|---|---|
| `S1/S2` frente a `SW1/SW2` | En el material de EtherChannel, representan los switches heredados. Documentar la equivalencia; no agregar switches nuevos por el cambio de nombre. |
| `R1`, `R3`, `S1` frente a `R1-CORE`, `R3-REMOTO`, `S1-ACCESO` | Son cambios de hostname previstos en el endurecimiento. Conservar las configuraciones y distinguir la etiqueta visual del hostname IOS. |
| IOS marcado como `[VERSIÓN POR VALIDAR]` | Registrar el valor real de `show version` para cada modelo. No inventar una versión ni instalar un IOS externo para completar ese campo. |
| Router con dos puertos frente a escenarios con `G0/2` | Usar 2911 en las topologías nuevas que requieran tres puertos. Verificar las interfaces reales antes de copiar comandos. |

**Bloqueo de continuidad WAN que debe resolver el instructor:** la topología de enrutamiento heredada ocupa `R1 G0/2` en el enlace alternativo R1–R3. El material WAN asigna esa misma interfaz al ISP, supone un enlace interno hacia R3 por `G0/1` y pide conservar el enrutamiento y el endurecimiento anteriores. No puede darse por disponible `G0/2` en una copia directa de la topología triangular.

El instructor debe proporcionar una **base WAN adaptada y comprobada**, con la interfaz hacia el ISP disponible y la conectividad interna, OSPF, usuarios, ACL VTY y SSH conservados. Documentar el mapa real de interfaces y cualquier ajuste frente al README. La adaptación exacta no puede deducirse de un `.pkt` del repositorio porque ese archivo no está incluido. No reutilizar un puerto ocupado ni cambiar su dirección como parte de la instalación del entorno.

## Direccionamiento, servicios y accesos

- Las direcciones IP corresponden a los dispositivos simulados. Mantener el direccionamiento de cada archivo; no asignar las IP del curso a la tarjeta de red de Windows o Ubuntu.
- El curso utiliza IPv4 e IPv6 en el simulador. No se necesita habilitar IPv6 adicionalmente en el equipo anfitrión para simularlo.
- HTTP, DNS y DHCP se ejecutan en Server-PT o, en el caso de DHCP del borde, en el router simulado. Están sujetos al estado previsto en cada archivo base.
- `www.ccna.lab`, `acl.lab.local` y `ccna-lab.local` son nombres internos de los escenarios. No requieren registro de dominio, cambios en el archivo `hosts` ni DNS público.
- Los accesos administrativos usan consola y, posteriormente, SSH versión 2. El cliente SSH de **Desktop > Command Prompt** de PC-PT es suficiente.
- Las cuentas locales cambian según el archivo: aparece `netadmin` en la base de ACL y `adminred` durante el endurecimiento. Confirmar el usuario efectivo de la base recibida; sus credenciales son distintas de la cuenta del portal Cisco.
- El material utiliza RSA de 1024 bits en una etapa y solicita 2048 bits posteriormente. Preparar un modelo que admita SSHv2 y comprobar el tamaño aceptado; 2048 bits es el objetivo de la etapa de endurecimiento. Si el simulador limita el tamaño, registrar la limitación. No regenerar claves en bases heredadas como una acción genérica de setup.
- Entregar las credenciales de los archivos base mediante el canal definido por el instructor. Conservar acceso de consola para verificar autenticación y evitar bloqueos por ACL. No publicar secretos en evidencias.

Los archivos `.pkt` almacenan topología y estado de los dispositivos. Para conservar cambios IOS, ejecutar `copy running-config startup-config` en cada equipo modificado y después guardar el `.pkt` desde Packet Tracer. Son dos guardados diferentes.

# Servicios Externos Requeridos

| Servicio | Acceso necesario | Uso |
|---|---|---|
| GitHub público | Lectura del repositorio; no se necesita token para descargar el ZIP público | Documentación y recursos del curso. |
| Cisco Networking Academy | Cuenta con acceso al portal y a la descarga correspondiente | Instalador y autenticación de Packet Tracer. |
| Documentación de Cisco | Lectura de páginas y documentos oficiales | Requisitos, instalación y consulta de comandos. |
| Repositorios de Ubuntu | Acceso durante la instalación, si se usa Linux | Bibliotecas requeridas por el paquete `.deb`. |

La red del aula debe permitir navegación HTTPS y las redirecciones de autenticación de Cisco. Si existe proxy institucional, comprobar en la estación real la descarga y el inicio de sesión. No desactivar globalmente el firewall para resolver un fallo de acceso.

El ISP, los servidores externos y la conectividad a Internet representados en las topologías son **simulados**. No requieren contratar un proveedor, publicar servicios reales ni abrir puertos entrantes en el equipo anfitrión. Los puertos de HTTP, DNS, DHCP y SSH de los ejercicios pertenecen a la red simulada.

# Creación del Entorno de Simulación

1. Iniciar Packet Tracer 8.2.2 y confirmar autenticación, versión y funcionamiento de la interfaz gráfica.
2. Comprobar el catálogo de dispositivos indicado en esta guía, incluyendo 1941, 2911, 2960, PC-PT y Server-PT.
3. Crear un archivo temporal de validación, separado de los archivos del curso, en `C:\CCNA\Batch01\_validacion_setup.pkt` o `$HOME/CCNA/Batch01/_validacion_setup.pkt`.
4. Verificar en ese archivo la terminal de consola, la CLI de los modelos requeridos y el acceso a las pestañas de servicios del servidor.
5. Guardar, cerrar y volver a abrir el archivo con 8.2.2. Confirmar que mantiene dispositivos y conexiones.

El entorno se crea dentro del simulador; no requiere máquinas virtuales ni un hipervisor.

# Validación del Entorno

Estas comprobaciones deben ejecutarse en la computadora que se utilizará durante el curso. Los resultados siguientes son **criterios esperados**, no resultados medidos durante la revisión documental.

| Verificación | Criterio de aceptación |
|---|---|
| Plataforma | Sistema de 64 bits y recursos suficientes según esta guía. |
| Aplicación | Help > About muestra **8.2.2** y se puede crear y abrir un `.pkt`. |
| Autenticación | La aplicación permite acceder al espacio de trabajo con la cuenta Cisco del participante. |
| Escritura | Se puede crear, guardar y reabrir un `.pkt` en la carpeta común sin elevar privilegios. |
| Consola | PC-PT > Terminal, con 9600/8/N/1 y sin control de flujo, muestra la CLI del dispositivo conectado. |
| Modelos | Los switches y routers requeridos están disponibles; el 2911 muestra `G0/0`, `G0/1` y `G0/2`. |
| Servicios integrados | Server-PT permite acceder a Services > HTTP, DNS y DHCP; PC-PT dispone de Command Prompt y Web Browser. |
| Simulación | Simulation permite visualizar eventos y abrir detalles de una PDU. |
| Persistencia | Una configuración de prueba guardada en startup-config y en el `.pkt` permanece al reabrir la copia de validación. |
| Recursos del instructor | El archivo inicial de diagnóstico está disponible y abre en 8.2.2; las bases heredadas y la adaptación WAN están identificadas. |

Si se recibe un archivo inicial diseñado con fallas, validar su apertura y los recursos esperados. La conectividad completa no es un criterio de aceptación para una topología de diagnóstico que debe comenzar con errores.

# Comandos de Verificación

## Windows — PowerShell

```powershell
# Sistema y arquitectura
Get-CimInstance Win32_OperatingSystem |
    Select-Object Caption, OSArchitecture,
        @{Name='RAMLibreGB';Expression={[math]::Round($_.FreePhysicalMemory / 1MB, 2)}}

# Memoria instalada
Get-CimInstance Win32_ComputerSystem |
    Select-Object @{Name='RAMInstaladaGB';Expression={[math]::Round($_.TotalPhysicalMemory / 1GB, 2)}}

# Espacio libre y carpeta de trabajo
Get-PSDrive -Name C | Select-Object Name,
    @{Name='LibreGB';Expression={[math]::Round($_.Free / 1GB, 2)}}
Test-Path -LiteralPath 'C:\CCNA\Batch01' -PathType Container

# Archivos iniciales y de simulación
Test-Path -LiteralPath 'C:\CCNA\Batch01\P9_Troubleshooting_Inicial.pkt' -PathType Leaf
Get-ChildItem -LiteralPath 'C:\CCNA\Batch01' -Filter '*.pkt' |
    Select-Object Name, Length, LastWriteTime

# Conectividad HTTPS del anfitrión al portal de Cisco
Test-NetConnection -ComputerName www.netacad.com -Port 443
```

**Resultados esperados:** arquitectura de 64 bits; al menos 4 GB de RAM libre y 2 GB de disco libre; `True` para la carpeta y para el archivo inicial una vez entregado; archivos `.pkt` con tamaño mayor que cero; `TcpTestSucceeded : True` cuando existe salida TCP directa. En una red que exige proxy, comprobar además el acceso desde el navegador: la prueba TCP no valida el inicio de sesión ni reproduce necesariamente el recorrido del proxy.

## Ubuntu — Bash

```bash
cat /etc/os-release
uname -m
free -h
df -h "$HOME"
command -v packettracer
dpkg-query -W -f='${Package} ${Version} ${Architecture}\n' packettracer
ls -ld "$HOME/CCNA/Batch01"
ls -lh "$HOME/CCNA/Batch01/P9_Troubleshooting_Inicial.pkt"
find "$HOME/CCNA/Batch01" -maxdepth 1 -type f -name '*.pkt' -size +0c -print
```

**Resultados esperados:** Ubuntu 22.04 LTS; `x86_64`; memoria disponible y almacenamiento suficientes; ruta del lanzador; paquete `packettracer` instalado, con arquitectura `amd64` y versión correspondiente a 8.2.2; directorio accesible y archivo inicial presente después de su entrega. El número de paquete puede incluir información de compilación: confirmar siempre la versión del producto en **Help > About**.

## Cisco IOS — CLI de dispositivos simulados

Estos comandos se ejecutan dentro del router o switch de Packet Tracer, no en PowerShell ni en Bash:

```text
enable
show version
show ip interface brief
show running-config
show startup-config
```

**Resultados esperados:** `show version` identifica el modelo y el IOS integrado; la lista de interfaces coincide con el equipo seleccionado. En un router nuevo puede aparecer `administratively down`, y puede no existir aún startup-config: ambos estados son normales antes de configurar y guardar.

En un switch 2960 de prueba:

```text
show interfaces status
show vlan brief
show mac address-table
```

**Resultados esperados:** interfaces disponibles y VLAN predeterminadas; la tabla MAC puede estar vacía hasta que exista tráfico. No exigir VLAN del curso ni enlaces activos en una topología recién creada.

Para comprobar persistencia **solo en el equipo temporal de validación**:

```text
configure terminal
hostname SETUP-CHECK
end
copy running-config startup-config
show startup-config
```

A la pregunta del nombre de destino, aceptar el predeterminado con Enter. Debe aparecer la confirmación de guardado y el hostname `SETUP-CHECK` en startup-config. Guardar también `_validacion_setup.pkt`, cerrarlo y reabrirlo para confirmar la conservación del estado. No aplicar este cambio de hostname a una base del curso.

## PC-PT — Command Prompt de una base configurada

```text
ipconfig
ping 192.168.10.1
```

Esta comprobación de conectividad aplica **únicamente** cuando la base recibida identifica `192.168.10.1` como gateway y debe estar operativa. Verificar previamente dirección y máscara en **Desktop > IP Configuration**. El primer intento puede requerir resolución ARP; repetir antes de diagnosticar una falla.

Para una base que ya tenga SSH y administración habilitados:

```text
ssh -l netadmin 192.168.10.1
```

Usar `adminred` en lugar de `netadmin` si ese es el usuario efectivo de la base de endurecimiento. La contraseña se introduce cuando se solicite. No ejecutar esta validación como requisito en una topología nueva que todavía no debe tener SSH configurado.

# Troubleshooting

| Síntoma de preparación | Comprobación y corrección |
|---|---|
| El portal solo muestra una versión nueva | Obtener 8.2.2 por el canal autorizado del instructor. Una migración exige validar todas las bases antes de adoptarla para el grupo. |
| Falla el acceso NetAcad/Skills for All | Verificar acceso al portal en el navegador, fecha y hora del sistema y el aviso oficial de cambio de autenticación. Confirmar el retorno del navegador a la aplicación. |
| Ubuntu informa dependencias incumplidas | Confirmar Ubuntu 22.04 AMD64 y el paquete correcto. Repetir la instalación con APT y revisar el paquete faltante y la disponibilidad de repositorios; no usar `--force-depends`. |
| El comando `packettracer` no existe | Comprobar que la instalación terminó, revisar `dpkg-query` y buscar el acceso de la aplicación en el menú gráfico. |
| La aplicación abre, pero las ventanas se recortan | Revisar resolución, escala de pantalla, fuentes y controladores. Preferir 1920 × 1080. |
| No se puede guardar en la carpeta del curso | Repetir la prueba de escritura. Corregir permisos sobre esa carpeta y espacio libre. |
| Falta `P9_Troubleshooting_Inicial.pkt` | Solicitar al instructor el recurso inicial: no forma parte de los archivos versionados del repositorio revisado. |
| Un archivo base tiene un nombre diferente | Aplicar la equivalencia documentada después de validar su contenido. Conservar el original y trabajar sobre una copia. |
| El archivo muestra “Invalid File” | Comparar versiones del programa que lo guardó y del que lo abre. Recuperar una copia compatible con 8.2.2; cambiar la extensión no convierte el archivo. |
| No aparece `G0/2` | Verificar `show version` y `show ip interface brief`. El 1941 no satisface una topología que exige tres GigabitEthernet; usar el modelo previsto en la creación de esa topología. |
| `G0/2` ya está conectado al abrir la base WAN | No sobrescribir su configuración. Utilizar la base WAN adaptada y validada por el instructor. |
| No aparece el prompt de consola | Verificar cable Console, RS232, puerto Console, encendido y parámetros 9600/8/N/1 sin control de flujo; presionar Enter. |
| Un comando IOS devuelve `% Invalid input` | Confirmar modo de CLI, interfaz y modelo; utilizar `?` para comprobar sintaxis admitida. Packet Tracer implementa un subconjunto de IOS. |
| Una interfaz nueva figura apagada o un puerto está ámbar | Distinguir equipo sin configurar de falla de instalación. Revisar conexiones y esperar convergencia antes de exigir conectividad. |
| SSH falla en una base preparada | Mantener acceso por consola; confirmar el usuario vigente, estado con `show ip ssh`, claves, VTY y autorización de la PC por la ACL. No eliminar ACL ni cuentas para desbloquear el setup. |
| Se pierden cambios al reabrir | Guardar startup-config en cada dispositivo modificado y después el `.pkt`. Recuperar el respaldo correcto si se abrió otra rama. |

# Checklist Final

- [ ] Equipo x86-64 con al menos 4 GB de RAM libre y 2 GB de disco libre para la aplicación.
- [ ] Windows 11 x64 o Ubuntu Desktop 22.04 LTS AMD64 preparado.
- [ ] Packet Tracer **8.2.2 de 64 bits** instalado y comprobado en Help > About.
- [ ] Cuenta Cisco y autenticación verificadas en la computadora del participante.
- [ ] Nueve capítulos y documentación general disponibles localmente.
- [ ] Carpeta `C:\CCNA\Batch01\` o `$HOME/CCNA/Batch01/` accesible con el usuario habitual.
- [ ] Modelos 2960, 1941, 2911, PC-PT y Server-PT disponibles.
- [ ] Consola, CLI, servicios integrados y modo Simulation comprobados.
- [ ] Archivo de validación guardado, cerrado y reabierto con éxito.
- [ ] `P9_Troubleshooting_Inicial.pkt` entregado por el instructor y abierto en 8.2.2.
- [ ] Equivalencias de nombres y archivos heredados documentadas para el grupo.
- [ ] Base WAN adaptada por el instructor, con puerto disponible y conectividad interna preservada.
- [ ] Credenciales de las bases disponibles por el canal definido; secretos excluidos de documentos y evidencias.
- [ ] Copias originales y respaldos preservados; comprobados ambos niveles de guardado.

# Resumen

La preparación común consiste en una computadora de 64 bits, **Packet Tracer 8.2.2**, una cuenta Cisco, acceso de escritura al directorio del curso y los dispositivos integrados del simulador. La disponibilidad del archivo inicial de diagnóstico y de una base WAN compatible debe cerrarse antes de declarar listo el entorno completo.

**Trazabilidad de la revisión:** se revisaron el README general, SETUP_GUIDE.md, los nueve README de capítulos y el inventario completo de archivos versionados en la revisión identificada. No hay archivos `.pkt`, `.pka`, imágenes IOS externas ni instaladores en ese inventario. Las recomendaciones de sistema operativo y pantalla están diferenciadas de los requisitos explícitos del curso. No se ejecutó Packet Tracer durante esta revisión.

**Fuentes del repositorio — revisión fija:**

- [Árbol completo y documentación general](https://github.com/Netec-Mx/CCNA/tree/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8).
- Capítulos [01](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo01/README.md), [02](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo02/README.md), [03](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo03/README.md), [04](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo04/README.md), [05](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo05/README.md), [06](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo06/README.md), [07](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo07/README.md), [08](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo08/README.md) y [09](https://github.com/Netec-Mx/CCNA/blob/1b727a3633a24bcbf30cd9bac94f70b6b0fb02c8/Capitulo09/README.md).

**Referencias oficiales para preparación y compatibilidad:**

- [Cisco — descarga e instalación de Packet Tracer](https://www.netacad.com/skillsforall/files/Cisco_Packet_Tracer_Download_and_Installation_Instructions.pdf).
- [Cisco — requisitos de sistema de Packet Tracer](https://tutorials.ptnetacad.net/help/default/systemRequirements.htm). Referencia de ayuda vigente; no es una ficha exclusiva de 8.2.2.
- [Cisco — FAQ y compatibilidad de archivos](https://tutorials.ptnetacad.net/help/default/faqTroubleShoot.htm).
- [Cisco — aviso de autenticación para Packet Tracer 8.2.2 y 9.0.0](https://www.netacad.com/articles/news/upcoming-login-change-for-cisco-packet-tracer).

La validación funcional y los archivos iniciales del instructor deben corresponder a la misma versión del simulador utilizada por el grupo.
