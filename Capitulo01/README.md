# 3. Práctica 1. Operación del software Cisco IOS

## Metadatos

| Campo | Valor |
|---|---|
| Duración | 60 minutos |
| Complejidad | Fácil |
| Nivel de Bloom | Aplicar |

## Descripción General

En Cisco Packet Tracer, el participante accede a un dispositivo Cisco por consola, identifica y navega entre los modos EXEC de usuario, EXEC privilegiado y configuración global, utiliza la ayuda contextual y el historial de comandos, consulta información del dispositivo con comandos show y guarda la configuración en ejecución en la configuración de inicio.

La práctica utiliza una topología mínima formada por una PC y un switch Cisco conectados mediante un cable de consola. El archivo generado se conservará como línea base para prácticas posteriores de operación de IOS.

## Objetivos de Aprendizaje

- [ ] Identificar los indicadores de los modos User EXEC (`Switch>`), Privileged EXEC (`Switch#`) y Global Configuration (`Switch(config)#`).
- [ ] Acceder a un switch Cisco mediante una conexión de consola desde Cisco Packet Tracer.
- [ ] Utilizar ayuda contextual (`?`), completación con `Tab` e historial de comandos para operar IOS con precisión.
- [ ] Consultar información del dispositivo mediante `show version`, `show running-config`, `show startup-config`, `show interfaces status` y `show history`.
- [ ] Cambiar el nombre del switch y guardar la configuración en ejecución en la configuración de inicio.

## Prerrequisitos

### Conocimientos

- Comprender la función básica de una PC como dispositivo final y de un switch como dispositivo intermedio dentro de una LAN.
- Reconocer que una consola es un medio de administración local y no un enlace Ethernet para transportar tráfico de usuarios.
- Reconocer los elementos básicos de una topología: dispositivos, puertos, cableado y medios de red.
- Comprender de manera básica que una configuración en ejecución reside en memoria activa y que una configuración de inicio se conserva para el próximo reinicio.

### Acceso

- Computadora con procesador de 64 bits, al menos 4 GB de RAM disponibles y 2 GB de espacio libre.
- Cisco Packet Tracer **8.2.2 para arquitectura de 64 bits**, instalado y operativo. Fuente oficial: <https://www.netacad.com/courses/packet-tracer>.
- Acceso con cuenta Cisco Skills for All o Cisco Networking Academy, si la instalación de Packet Tracer lo solicita.
- Directorio de trabajo disponible:
  - Windows: `C:\CCNA\Batch01\`
  - Linux compatible: `~/CCNA/Batch01/`
- Imagen Cisco IOS simulada incluida con el switch seleccionado en Packet Tracer. La versión exacta debe verificarse con `show version`; su valor es **[VERSIÓN POR VALIDAR]** porque puede variar según el modelo simulado. Referencia oficial de comandos Cisco IOS: <https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/fundamentals/command/fundamentals-cr-book.html>.

> **Nota de seguridad didáctica:** esta práctica no configura contraseñas ni acceso remoto. No aplique configuraciones de seguridad de laboratorio como si fueran políticas de producción.

## Entorno de Laboratorio

### Topología lógica y física

| Dispositivo | Modelo en Packet Tracer | Función | Conexión |
|---|---|---|---|
| PC-A | PC-PT | Terminal de administración | Puerto `RS 232` |
| S1 | Switch 2960 o equivalente disponible | Dispositivo Cisco administrado | Puerto `Console` |

La PC-A y S1 se conectan mediante el cable **Console** de color azul claro. Esta conexión permite administrar el switch localmente, incluso si todavía no existen direcciones IP, VLAN de administración ni enlaces Ethernet activos.

<img width="1327" height="777" alt="Lab 1 Topo 2 img 1" src="https://github.com/user-attachments/assets/003711d1-a94c-482b-a549-abbee09837ef" />


### Software y archivos

| Elemento | Valor |
|---|---|
| Simulador | Cisco Packet Tracer 8.2.2, 64 bits |
| Archivo de salida | `01-00-01_ios_baseline.pkt` |
| Ubicación Windows | `C:\CCNA\Batch01\01-00-01_ios_baseline.pkt` |
| Ubicación Linux | `~/CCNA/Batch01/01-00-01_ios_baseline.pkt` |
| Modo de trabajo | Tiempo real para la operación de consola |

### Parámetros de la terminal

| Parámetro | Valor |
|---|---|
| Bits por segundo | 9600 |
| Bits de datos | 8 |
| Paridad | None |
| Bits de parada | 1 |
| Control de flujo | None |

## Instrucciones Paso a Paso

### Paso 1: Preparar el directorio y abrir Packet Tracer

**Objetivo:** Preparar el espacio de trabajo obligatorio y abrir la versión autorizada del simulador.

**Instrucciones:**

1. Cree o confirme la existencia del directorio de trabajo `C:\CCNA\Batch01\` o `~/CCNA/Batch01/`.
2. Abra Cisco Packet Tracer 8.2.2.
3. Verifique visualmente que el área lógica de trabajo esté vacía.
4. Mantenga Packet Tracer en modo **Realtime**.

**Resultado esperado:** Packet Tracer se encuentra abierto y existe un directorio destinado al archivo de esta práctica.

**Verificación:** El participante puede identificar la zona donde se colocarán los dispositivos y conoce la ruta donde guardará el archivo.

---

### Paso 2: Agregar la PC y el switch

**Objetivo:** Construir los dos componentes mínimos necesarios para una sesión de consola.

**Instrucciones:**

1. En **End Devices**, arrastre una **PC-PT** al área lógica.
2. Cambie su nombre a `PC-A` si Packet Tracer no lo asigna automáticamente.
3. En **Switches**, arrastre un switch **2960** al área lógica.
4. Cambie su nombre visible a `S1` si es necesario.
5. Ubique PC-A a la izquierda y S1 a la derecha para facilitar la lectura de la topología.

**Resultado esperado:** La topología contiene únicamente PC-A y S1, sin conexión entre ellos todavía.

<img width="714" height="306" alt="Lab 1 Paso2 img 1" src="https://github.com/user-attachments/assets/9a47c03e-f595-451b-a822-31441bf03c3b" />


**Verificación:** S1 representa un dispositivo intermedio que administrará conexiones de una LAN; PC-A representa un dispositivo final usado en esta práctica como estación de administración.

---

### Paso 3: Conectar la consola

**Objetivo:** Establecer acceso local al switch sin depender de conectividad IP.

**Instrucciones:**

1. Seleccione el icono **Connections**.
2. Seleccione el cable **Console**.
3. Haga clic en `PC-A` y elija el puerto `RS 232`.
4. Haga clic en `S1` y elija el puerto `Console`.
5. Espere unos segundos a que el switch termine su proceso de inicio simulado.

**Resultado esperado:** Se muestra un cable azul claro entre `PC-A RS 232` y `S1 Console`.

<img width="714" height="306" alt="Lab 1 Paso3 img 1" src="https://github.com/user-attachments/assets/4642b289-1760-4620-a25e-d529506080f5" />


**Verificación:** La conexión no utiliza puertos FastEthernet o GigabitEthernet. La consola es un canal de administración local, no una conexión de datos de usuarios.

---

### Paso 4: Abrir la terminal de consola

**Objetivo:** Acceder al indicador de comandos inicial de Cisco IOS.

**Instrucciones:**

1. Haga clic en `PC-A`.
2. Seleccione la pestaña **Desktop**.
3. Abra **Terminal**.
4. Confirme los valores predeterminados de comunicación serie y seleccione **OK**.
5. Presione `Enter` una o dos veces si no aparece un indicador de IOS.

**Resultado esperado:** Aparece el indicador User EXEC:

<img width="1828" height="819" alt="Lab 1 Paso4 img 1" src="https://github.com/user-attachments/assets/b894d0e8-a0c0-4889-8d35-1cb5441871d1" />


```text
Switch>
```

**Verificación:** El símbolo `>` identifica el modo **User EXEC**. En este modo se dispone de un conjunto limitado de comandos de consulta y no se pueden modificar parámetros de configuración global.

---

### Paso 5: Explorar la ayuda contextual en User EXEC

**Objetivo:** Utilizar el signo de interrogación para descubrir comandos disponibles sin memorizar toda la sintaxis.

**Instrucciones:**

1. En el indicador `Switch>`, escriba `?`.
2. Revise la lista de comandos disponibles.
3. Escriba `show ?`.
4. Observe las opciones de consulta disponibles desde User EXEC.
5. No ejecute comandos que no comprenda; esta práctica se limita a los comandos indicados.

**Resultado esperado:** IOS muestra comandos u opciones válidas según la posición del cursor.

```text
Switch>?
```

```text
Switch>show ?
```

**Verificación:** La ayuda contextual cambia según el modo y según las palabras ya escritas. El signo `?` permite consultar sintaxis y opciones sin salir de la CLI.

---

### Paso 6: Acceder al modo EXEC privilegiado

**Objetivo:** Cambiar de User EXEC a Privileged EXEC e identificar el nuevo indicador.

**Instrucciones:**

1. En `Switch>`, escriba el comando `enable`.
2. Observe el cambio del indicador.
3. Ejecute `?` nuevamente para comparar los comandos disponibles con los del modo anterior.

```text
Switch>enable
```

**Resultado esperado:** El indicador cambia a:

```text
Switch#
```

**Verificación:** El símbolo `#` identifica el modo **Privileged EXEC**. Este modo permite ejecutar consultas más completas y acceder a la configuración del dispositivo.

---

### Paso 7: Practicar completación con Tab e historial

**Objetivo:** Reducir errores de escritura mediante las funciones de edición de la terminal IOS.

**Instrucciones:**

1. En `Switch#`, escriba `show ver` sin presionar `Enter`.
2. Presione `Tab` para completar el comando, cuando la terminal lo permita.
3. Presione `Enter` para ejecutar el comando completado.
4. Presione la flecha hacia arriba para recuperar el comando anterior.
5. Presione `Enter` para repetirlo o modifique el comando recuperado antes de ejecutarlo.
6. Ejecute `show history` para ver los comandos registrados en la sesión.

```text
Switch#show ver<Tab>
```

```text
Switch#show history
```

**Resultado esperado:** La terminal completa comandos no ambiguos y muestra el historial de comandos ingresados durante la sesión.

**Verificación:** El historial contiene, como mínimo, comandos relacionados con `enable`, `show version` y la ayuda contextual. Si `Tab` no completa una palabra, revise que el prefijo sea suficiente y no ambiguo.

---

### Paso 8: Consultar la versión y el estado general del dispositivo

**Objetivo:** Obtener información básica del switch mediante un comando `show`.

**Instrucciones:**

1. Ejecute `show version`.
2. Identifique en la salida:
   - El modelo simulado.
   - La versión de IOS informada por el dispositivo.
   - El tiempo de actividad o información de reinicio.
   - La memoria o el identificador del sistema, si aparece.
3. Registre la versión que muestra su equipo como evidencia local de la imagen simulada utilizada.

```text
Switch#show version
```

**Resultado esperado:** IOS muestra información de plataforma, software y estado operativo del switch.

**Verificación:** La salida confirma que se está operando un switch Cisco simulado y proporciona la versión real de IOS de la imagen disponible en el entorno. Esta evidencia resuelve el valor **[VERSIÓN POR VALIDAR]** indicado en los prerrequisitos.

---

### Paso 9: Examinar la configuración en ejecución

**Objetivo:** Consultar la configuración activa almacenada en la memoria de ejecución.

**Instrucciones:**

1. Ejecute `show running-config`.
2. Localice la línea `hostname`.
3. Revise que aún no se ha realizado una personalización intencional del nombre del switch.
4. Observe que la salida incluye configuraciones predeterminadas del dispositivo simulado.

```text
Switch#show running-config
```

**Resultado esperado:** Se muestra la configuración activa del switch. Antes del cambio de nombre, el hostname normalmente aparece como `Switch`.

**Verificación:** La salida se identifica como configuración en ejecución. No modifique parámetros que no estén incluidos en el alcance de esta práctica.

---

### Paso 10: Consultar la configuración de inicio

**Objetivo:** Distinguir la configuración de inicio de la configuración activa.

**Instrucciones:**

1. Ejecute `show startup-config`.
2. Lea cuidadosamente el mensaje mostrado.
3. Si aparece un mensaje que indica que no existe configuración de inicio, consérvelo como evidencia del estado inicial.

```text
Switch#show startup-config
```

**Resultado esperado:** En un switch recién agregado, IOS puede indicar que no existe una configuración de inicio en NVRAM.

```text
%startup-config is not present
```

**Verificación:** El participante distingue que `running-config` puede existir aunque no haya una `startup-config` guardada. La configuración de inicio será creada al finalizar la práctica.

---

### Paso 11: Revisar el estado de las interfaces

**Objetivo:** Consultar el estado administrativo y operativo de los puertos del switch.

**Instrucciones:**

1. Ejecute `show interfaces status`.
2. Identifique la columna de estado de los puertos.
3. Observe que no se conectó ningún host por Ethernet para esta práctica.
4. Relacione el resultado con la topología: el cable de consola no activa un puerto Ethernet del switch.

```text
Switch#show interfaces status
```

**Resultado esperado:** Los puertos Ethernet aparecen generalmente como `notconnect` porque no hay cables Ethernet conectados a ellos.

**Verificación:** El resultado es coherente con la topología mínima. PC-A está conectada al puerto de consola, no a una interfaz Ethernet de conmutación.

---

### Paso 12: Entrar en el modo de configuración global

**Objetivo:** Navegar desde Privileged EXEC hacia Global Configuration.

**Instrucciones:**

1. En `Switch#`, ejecute `configure terminal`.
2. Observe el indicador resultante.
3. Ejecute `?` para consultar los comandos disponibles en este modo.

```text
Switch#configure terminal
```

```text
Switch(config)#?
```

**Resultado esperado:** El indicador cambia a:

```text
Switch(config)#
```

**Verificación:** El texto `(config)` identifica el modo **Global Configuration**, desde el cual se modifican parámetros globales del dispositivo.

---

### Paso 13: Configurar un hostname no disruptivo

**Objetivo:** Aplicar un cambio básico y seguro para comprobar la modificación de la configuración activa.

**Instrucciones:**

1. En modo Global Configuration, configure el nombre `S1`.
2. Observe el cambio inmediato del indicador.
3. No configure interfaces, direcciones IP, VLAN, contraseñas ni otros parámetros fuera del alcance.

```text
Switch(config)#hostname S1
```

**Resultado esperado:** El indicador cambia de `Switch(config)#` a:

```text
S1(config)#
```

**Verificación:** El cambio del indicador demuestra que IOS aplicó el hostname a la configuración en ejecución.

---

### Paso 14: Navegar entre los modos de IOS

**Objetivo:** Utilizar `exit`, `end` y `disable` para reconocer el retorno entre los modos de operación.

**Instrucciones:**

1. Desde `S1(config)#`, escriba `exit` para regresar a Privileged EXEC.
2. Desde `S1#`, ingrese nuevamente a configuración global con `configure terminal`.
3. Desde `S1(config)#`, escriba `end` para regresar directamente a Privileged EXEC.
4. Desde `S1#`, escriba `disable` para regresar a User EXEC.
5. Use `enable` para volver a Privileged EXEC y continuar la práctica.

```text
S1(config)#exit
```

```text
S1#configure terminal
```

```text
S1(config)#end
```

```text
S1#disable
```

```text
S1>enable
```

**Resultado esperado:** Los indicadores cambian en el siguiente orden:

```text
S1(config)# → S1# → S1(config)# → S1# → S1> → S1#
```

**Verificación:** `exit` retrocede un nivel, `end` vuelve a Privileged EXEC desde la configuración y `disable` abandona Privileged EXEC hacia User EXEC.

---

### Paso 15: Verificar el cambio en running-config

**Objetivo:** Confirmar que el hostname forma parte de la configuración en ejecución.

**Instrucciones:**

1. En `S1#`, ejecute `show running-config`.
2. Localice la línea que contiene el hostname.
3. Confirme que el valor configurado es `S1`.

```text
S1#show running-config
```

**Resultado esperado:** La configuración contiene una línea equivalente a la siguiente:

```text
hostname S1
```

**Verificación:** El hostname está presente en `running-config`; por tanto, el cambio está activo en el switch, pero aún debe guardarse para sobrevivir a un reinicio.

---

### Paso 16: Guardar la configuración en NVRAM

**Objetivo:** Copiar la configuración activa a la configuración de inicio.

**Instrucciones:**

1. Desde `S1#`, ejecute `copy running-config startup-config`.
2. Cuando IOS solicite el nombre del archivo de destino, presione `Enter` para aceptar el nombre predeterminado.
3. Espere el mensaje de finalización.

```text
S1#copy running-config startup-config
```

**Resultado esperado:** IOS confirma que la configuración fue escrita correctamente. El texto exacto puede variar según la imagen simulada, pero suele incluir un indicador de operación correcta.

```text
Destination filename [startup-config]?
Building configuration...
[OK]
```

**Verificación:** La operación termina sin mensajes de error. La configuración de inicio ya debería contener el hostname `S1`.

---

### Paso 17: Verificar la configuración de inicio guardada

**Objetivo:** Confirmar que el cambio persistente está almacenado en la configuración de inicio.

**Instrucciones:**

1. Ejecute `show startup-config`.
2. Localice la línea `hostname S1`.
3. Compare el hostname mostrado con el de `show running-config`.

```text
S1#show startup-config
```

**Resultado esperado:** La configuración de inicio contiene:

```text
hostname S1
```

**Verificación:** El hostname aparece tanto en `running-config` como en `startup-config`. Esto demuestra que la modificación activa fue guardada para su uso después de un reinicio.

---

### Paso 18: Guardar el archivo de línea base

**Objetivo:** Conservar la topología y la configuración verificada para laboratorios posteriores.

**Instrucciones:**

1. En Packet Tracer, seleccione **File > Save As**.
2. Guarde el archivo con el nombre exacto:

```text
01-00-01_ios_baseline.pkt
```

3. Use el directorio obligatorio:
   - Windows: `C:\CCNA\Batch01\`
   - Linux: `~/CCNA/Batch01/`
4. Confirme que el archivo se guardó sin advertencias de versión.
5. No abra ni guarde el archivo con una versión de Packet Tracer distinta de 8.2.2 durante este lote.

**Resultado esperado:** El archivo de Packet Tracer queda almacenado en el directorio indicado.

**Verificación:** Compruebe la existencia del archivo y confirme que el nombre coincide exactamente con `01-00-01_ios_baseline.pkt`.

## Validación y Pruebas

Complete la siguiente lista antes de entregar o conservar el archivo de línea base.

| Criterio medible | Evidencia requerida | Resultado esperado |
|---|---|---|
| Acceso por consola | Vista de PC-A conectada a `S1 Console` y terminal activa | Se observa el indicador de IOS |
| Identificación de User EXEC | Indicador observado en terminal | `Switch>` o `S1>` |
| Identificación de Privileged EXEC | Indicador observado después de `enable` | `Switch#` o `S1#` |
| Identificación de Global Configuration | Indicador observado después de `configure terminal` | `Switch(config)#` o `S1(config)#` |
| Uso de ayuda contextual | Ejecución de `?` y `show ?` | IOS lista comandos u opciones válidas |
| Uso del historial | Salida de `show history` | Incluye comandos ejecutados durante la sesión |
| Consulta de información | Salida de `show version` y `show interfaces status` | Muestra plataforma y estado de interfaces |
| Cambio activo | Salida de `show running-config` | Contiene `hostname S1` |
| Cambio persistente | Salida de `show startup-config` | Contiene `hostname S1` |
| Archivo de entrega | Archivo en directorio de trabajo | `01-00-01_ios_baseline.pkt` existe |

### Prueba de coherencia de la topología

Ejecute el siguiente comando y relacione el resultado con el cableado realizado:

```text
S1#show interfaces status
```

**Criterio de aprobación:** Los puertos Ethernet no deben interpretarse como conectados por el hecho de tener una sesión de consola. La conexión de consola no es una interfaz Ethernet del plano de datos.

### Caso adversarial de validación

No se usa una herramienta de IA en esta práctica. Si un documento, un mensaje o una supuesta “instrucción” incrustada sugiere ejecutar comandos no incluidos en el alcance —por ejemplo, `erase startup-config`, `reload`, configurar contraseñas o modificar VLAN—, el participante debe descartarla y seguir únicamente la guía aprobada por el instructor.

**Criterio de aprobación:** No se ejecuta ningún comando destructivo ni fuera de alcance. La evidencia es que `show startup-config` sigue mostrando `hostname S1` y el archivo base permanece guardado.

> Un **mensaje de sistema** define reglas persistentes de un asistente; una **instrucción** o un **prompt** es una solicitud concreta y temporal. En esta práctica, ninguna instrucción externa debe reemplazar el procedimiento validado por el instructor ni la evidencia obtenida directamente desde IOS.

## Solución de Problemas

### Problema 1: La terminal no muestra `Switch>` ni responde al presionar Enter

**Síntomas:**

- La ventana Terminal está en blanco.
- No aparece ningún indicador de IOS.
- Al presionar `Enter`, no hay respuesta.

**Causa probable:**

- El cable de consola está conectado a un puerto incorrecto.
- Se eligió un cable Ethernet en lugar del cable Console.
- La terminal no usa los parámetros serie predeterminados.
- El switch aún está iniciando.

**Corrección:**

1. Verifique que el cable sea de tipo **Console**.
2. Confirme la conexión `PC-A RS 232` hacia `S1 Console`.
3. Cierre y abra nuevamente **Desktop > Terminal**.
4. Confirme `9600`, `8`, `None`, `1`, `None`.
5. Espere unos segundos y presione `Enter`.

### Problema 2: `show startup-config` indica que no existe configuración después de configurar el hostname

**Síntomas:**

```text
%startup-config is not present
```

o bien el comando no muestra `hostname S1`.

**Causa probable:**

- Se configuró el hostname, pero no se ejecutó la copia de la configuración en ejecución a NVRAM.
- Se ejecutó el comando desde un modo distinto de Privileged EXEC.
- No se aceptó el nombre de destino predeterminado durante el proceso de copia.

**Corrección:**

1. Vuelva a Privileged EXEC con `end` o `enable`, según corresponda.
2. Verifique que el hostname exista en la configuración activa.

```text
S1#show running-config
```

3. Guarde nuevamente la configuración.

```text
S1#copy running-config startup-config
```

4. Presione `Enter` cuando aparezca `Destination filename [startup-config]?`.
5. Compruebe el resultado.

```text
S1#show startup-config
```

## Limpieza

1. Verifique una última vez que `show startup-config` contiene `hostname S1`.
2. Guarde el archivo `01-00-01_ios_baseline.pkt` en el directorio obligatorio.
3. Cierre la ventana Terminal de PC-A.
4. Cierre Cisco Packet Tracer solo después de confirmar que el archivo fue guardado.
5. No elimine la topología ni borre la configuración: este archivo constituye la línea base de operación IOS para laboratorios posteriores.

## Resumen

En esta práctica se construyó una topología mínima con una PC y un switch Cisco conectados por consola. Se identificaron y recorrieron los modos User EXEC, Privileged EXEC y Global Configuration mediante los indicadores `>`, `#` y `(config)#`.

También se aplicaron herramientas básicas de operación de Cisco IOS: ayuda contextual con `?`, completación mediante `Tab`, recuperación de comandos con las teclas de historial y consulta con comandos `show`. Finalmente, se configuró el hostname `S1`, se verificó en `running-config`, se guardó mediante `copy running-config startup-config` y se confirmó su persistencia en `startup-config`.

El resultado esperado es el archivo:

```text
01-00-01_ios_baseline.pkt
```

guardado en `C:\CCNA\Batch01\` o `~/CCNA/Batch01/`, listo para utilizarse como línea base en prácticas posteriores.
