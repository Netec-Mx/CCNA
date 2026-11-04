# 3. Práctica 9. Resolución de problemas de una red sencilla

## Metadatos

| Elemento | Valor |
|---|---|
| Duración | 50 minutos |
| Complejidad | Media |
| Nivel de Bloom | Aplicar |

## Descripción General

En Cisco Packet Tracer, el participante recibe una topología con fallas preconfiguradas, aplica un método de resolución de problemas por capas, utiliza ping, traceroute y comandos show para aislar problemas de cableado, interfaces, direccionamiento IP y puerta de enlace predeterminada, corrige la configuración y documenta la causa raíz de cada falla.

La práctica utiliza una red IPv4 pequeña compuesta por dos LAN, switches y routers. El participante debe reunir evidencia antes de realizar cambios, corregir únicamente las fallas identificadas y validar progresivamente la conectividad local, hacia el gateway y entre redes remotas.

La topología es independiente de las prácticas anteriores. No reutilice ni modifique dispositivos, configuraciones o archivos de las prácticas de VLAN, trunks o EtherChannel.

## Objetivos de Aprendizaje

- [ ] Aplicar una secuencia de diagnóstico desde la conectividad local hasta la conectividad remota.
- [ ] Utilizar `ping`, `tracert` o `traceroute`, `show ip interface brief`, `show interfaces`, `show ip route`, `show running-config` y `show cdp neighbors` para aislar fallas.
- [ ] Diferenciar síntomas de fallas físicas, administrativas, de direccionamiento IPv4 y de gateway predeterminado.
- [ ] Corregir las fallas confirmadas y validar cada corrección mediante evidencia técnica antes y después del cambio.
- [ ] Registrar en una bitácora el síntoma, la evidencia, la causa raíz, la corrección y la validación de cada falla.

## Prerrequisitos

**Conocimientos requeridos**

- Comprender la función de routers, switches, interfaces Ethernet, direcciones IPv4, máscaras de subred y gateways predeterminados.
- Reconocer que un host usa su gateway predeterminado para alcanzar redes remotas.
- Interpretar estados básicos de interfaces Cisco IOS, especialmente `up/up`, `administratively down/down` y `down/down`.
- Haber completado las Prácticas 6, 7 y 8, o demostrar dominio equivalente de `ping` y comandos `show`.
- Conocer a nivel básico el propósito de VLAN, trunks, STP y EtherChannel, aunque esta práctica se centra en una red IPv4 controlada.

**Acceso requerido**

- Cisco Packet Tracer 8.2.2 instalado y funcional.
- Archivo proporcionado por el instructor: `P9_Troubleshooting_Inicial.pkt`.
- Permiso de escritura en `C:\CCNA\Batch01\` o, en Linux compatible, `~/CCNA/Batch01/`.
- Acceso a la CLI de PCs, switches y routers dentro de Packet Tracer.

> **Regla de trabajo:** no modifique la topología de forma aleatoria, no agregue dispositivos y no cambie direcciones sin evidencia. Cada cambio debe estar justificado por una observación previa.

## Entorno de Laboratorio

### Versiones y fuentes oficiales

| Tecnología | Versión o edición | Uso en la práctica | Fuente oficial |
|---|---|---|---|
| Cisco Packet Tracer | Cisco Packet Tracer 8.2.2, arquitectura de 64 bits | Simulación de PCs, switches, routers, enlaces Ethernet y pruebas ICMP | https://www.netacad.com/courses/packet-tracer |
| Cisco IOS simulado | Imagen IOS incluida en Cisco Packet Tracer 8.2.2; versión exacta visible mediante `show version` y marcada como **[VERSIÓN POR VALIDAR]** | Diagnóstico y corrección en routers y switches simulados | https://www.cisco.com/c/en/us/products/ios-nx-os-software/ios-technologies-listing.html |
| IPv4 | RFC 791 | Direccionamiento, gateways y reenvío entre redes IPv4 | https://www.rfc-editor.org/rfc/rfc791 |
| ICMP para IPv4 | RFC 792 | Pruebas de conectividad mediante `ping` y análisis de saltos | https://www.rfc-editor.org/rfc/rfc792 |

### Recursos de hardware y software

| Recurso | Requisito para esta práctica |
|---|---|
| Equipo del participante | Procesador de 64 bits, 8 GB de RAM recomendados, pantalla mínima de 1366x768 |
| Sistema operativo | Compatible con Cisco Packet Tracer 8.2.2 según Cisco Networking Academy |
| Directorio de trabajo en Windows | `C:\CCNA\Batch01\` |
| Directorio de trabajo en Linux compatible | `~/CCNA/Batch01/` |
| Archivo inicial | `P9_Troubleshooting_Inicial.pkt` |
| Archivo de entrega | `P9_Troubleshooting_Resuelto.pkt` |

### Alcance de herramientas

Esta práctica no utiliza asistentes de IA, agentes persistentes, prompts de IA ni mensajes de sistema. En consecuencia, no se requiere licencia ni configuración de Microsoft 365 Copilot, Copilot Chat, Microsoft Designer, Planner u otras herramientas similares.

Un **prompt** es una instrucción enviada a un sistema de IA; un **mensaje de sistema** define reglas persistentes para dicho sistema; una **instrucción** en esta práctica es una indicación del laboratorio o del instructor. Las notas colocadas dentro de Packet Tracer no sustituyen las instrucciones de este laboratorio ni constituyen evidencia técnica suficiente para modificar una configuración.

### Preparación inicial

Verifique que el archivo inicial se encuentre en el directorio obligatorio. En Windows, puede comprobarlo desde el Explorador de archivos. En Linux compatible, utilice:

```bash
ls -l ~/CCNA/Batch01/P9_Troubleshooting_Inicial.pkt
```

## Instrucciones Paso a Paso

### Paso 1: Abrir y guardar una copia de trabajo controlada

**Objetivo:** Preservar el archivo original proporcionado por el instructor y crear el archivo de entrega.

**Instrucciones:**

1. Abra Cisco Packet Tracer 8.2.2.
2. Seleccione **File > Open** y abra `P9_Troubleshooting_Inicial.pkt`.
3. Seleccione **File > Save As**.
4. Guarde una copia con el nombre `P9_Troubleshooting_Resuelto.pkt`.
5. Utilice el directorio obligatorio `C:\CCNA\Batch01\` o `~/CCNA/Batch01/`.
6. No sobrescriba el archivo inicial.

**Resultado esperado:** Packet Tracer muestra la topología inicial y el archivo activo tiene el nombre `P9_Troubleshooting_Resuelto.pkt`.

**Verificación:** Confirme el nombre del archivo en la barra de título de Packet Tracer.

---

### Paso 2: Identificar dispositivos, enlaces y direcciones visibles

**Objetivo:** Construir un inventario inicial sin realizar cambios.

**Instrucciones:**

1. Observe los nombres de routers, switches y PCs de la topología.
2. Identifique qué PCs pertenecen a cada LAN.
3. Registre las etiquetas de direcciones IPv4, máscaras, interfaces y enlaces que estén visibles.
4. Identifique el enlace entre routers y los enlaces de acceso hacia cada LAN.
5. Anote las interfaces de router conectadas a cada switch y al router remoto.

Use una tabla como referencia en su bitácora:

| Elemento | LAN o enlace | Dirección esperada según etiquetas | Estado inicial observado |
|---|---|---|---|
| PC de LAN izquierda | LAN-A o equivalente | Según topología | Pendiente |
| Gateway de LAN izquierda | Router local | Según topología | Pendiente |
| Enlace entre routers | Tránsito | Según topología | Pendiente |
| Gateway de LAN derecha | Router remoto | Según topología | Pendiente |
| PC de LAN derecha | LAN-B o equivalente | Según topología | Pendiente |

**Resultado esperado:** Dispone de un inventario básico de dispositivos, interfaces y direccionamiento visible.

**Verificación:** Puede señalar el camino previsto entre una PC de una LAN y una PC de la otra LAN.

---

### Paso 3: Registrar la evidencia inicial desde una PC

**Objetivo:** Establecer una línea base de conectividad antes de corregir fallas.

**Instrucciones:**

1. Abra una PC de la primera LAN.
2. Seleccione la ficha **Desktop > Command Prompt**.
3. Registre su configuración IPv4.
4. Realice un `ping` a su gateway predeterminado.
5. Realice un `ping` a una PC de la LAN remota.
6. Ejecute `tracert` hacia la PC remota.

Sustituya los valores entre corchetes por las direcciones observadas en la topología.

```text
ipconfig
```

```text
ping [gateway-local]
```

```text
ping [host-remoto]
```

```text
tracert [host-remoto]
```

**Resultado esperado:** Al menos una prueba puede fallar debido a las fallas preconfiguradas. Registre literalmente los mensajes y resultados observados.

**Verificación:** Guarde en la bitácora la dirección IPv4, máscara, gateway de la PC y el resultado de las tres pruebas.

---

### Paso 4: Inspeccionar el estado de interfaces del primer router

**Objetivo:** Detectar fallas de capa física o administrativa antes de modificar el direccionamiento.

**Instrucciones:**

1. Abra la CLI del router conectado a la primera LAN.
2. Entre al modo EXEC privilegiado.
3. Consulte el resumen de interfaces IPv4.
4. Identifique interfaces en estado distinto de `up/up`.
5. Compare las interfaces mostradas con los enlaces físicos de la topología.

```ios
enable
show ip interface brief
```

**Resultado esperado:** Las interfaces que participan en la conectividad esperada deben aparecer como `up/up`. Una interfaz `administratively down` indica que fue deshabilitada mediante configuración.

**Verificación:** Registre el nombre de cada interfaz, su dirección IPv4 y sus columnas `Status` y `Protocol`.

---

### Paso 5: Revisar en detalle una interfaz sospechosa

**Objetivo:** Diferenciar una interfaz administrativamente deshabilitada de una falla de enlace físico.

**Instrucciones:**

1. Seleccione una interfaz cuyo estado no sea `up/up`.
2. Consulte su información detallada.
3. Observe si el resultado indica apagado administrativo, falta de enlace o errores físicos.
4. Consulte la configuración activa del router para confirmar si la interfaz contiene `shutdown`, una dirección incorrecta o una máscara inesperada.

```ios
show interfaces [interfaz-sospechosa]
```

```ios
show running-config
```

**Resultado esperado:** La evidencia permite clasificar la falla sin modificar todavía otros elementos.

**Verificación:** Registre una de estas conclusiones: enlace físico no activo, interfaz administrativamente apagada, dirección IPv4 incorrecta, máscara incorrecta o configuración aparentemente correcta.

---

### Paso 6: Verificar la conectividad de acceso en switches cuando aplique

**Objetivo:** Confirmar que los switches reconocen los vecinos esperados y que los puertos conectados están operativos.

**Instrucciones:**

1. Abra la CLI del switch conectado a la LAN donde se observó la falla.
2. Consulte los vecinos CDP.
3. Consulte el estado de la interfaz conectada al router o a la PC afectada.
4. Revise las VLAN solamente si la topología utiliza más de una VLAN o si la evidencia indica una inconsistencia de acceso.

```ios
enable
show cdp neighbors
```

```ios
show interfaces [puerto-del-switch]
```

```ios
show vlan brief
```

**Resultado esperado:** El switch muestra el vecino Cisco esperado en el enlace correspondiente. Los puertos de acceso utilizados deben estar activos y asociados a la VLAN prevista por la topología.

**Verificación:** Registre el puerto del switch, el vecino detectado y el estado operacional del enlace.

---

### Paso 7: Validar la configuración IPv4 local de cada PC

**Objetivo:** Determinar si una PC tiene una dirección, máscara o gateway incompatible con su LAN.

**Instrucciones:**

1. Abra la configuración de cada PC mediante **Desktop > IP Configuration**.
2. Compare dirección IPv4, máscara y gateway con las etiquetas de la topología y con la interfaz LAN del router.
3. No cambie valores todavía si no ha identificado una incompatibilidad verificable.
4. Registre la información en la bitácora.

Puede usar también la consola de cada PC:

```text
ipconfig
```

**Resultado esperado:** Las PCs de una misma LAN utilizan direcciones del mismo prefijo IPv4 y comparten el gateway correspondiente a la interfaz del router local.

**Verificación:** Para cada PC, indique si la dirección IPv4, máscara y gateway coinciden o no con el diseño visible.

---

### Paso 8: Verificar direccionamiento y estado en ambos routers

**Objetivo:** Confirmar que las interfaces LAN y de tránsito tienen configuraciones coherentes.

**Instrucciones:**

1. En cada router, ejecute `show ip interface brief`.
2. Compare las direcciones IPv4 de las interfaces LAN con las direcciones de sus PCs.
3. Compare las direcciones del enlace de tránsito para confirmar que ambas pertenecen a la misma subred.
4. Identifique cualquier dirección o máscara incompatible.

```ios
show ip interface brief
```

```ios
show running-config | section interface
```

**Resultado esperado:** Las interfaces conectadas al mismo segmento usan direcciones válidas de la misma subred y se encuentran activas cuando el enlace físico está conectado.

**Verificación:** Registre las interfaces que requieren corrección y la evidencia concreta que justifica el cambio.

---

### Paso 9: Corregir una interfaz administrativamente deshabilitada

**Objetivo:** Restaurar la operación de una interfaz solo cuando la evidencia confirme un apagado administrativo.

**Instrucciones:**

1. En el dispositivo afectado, entre al modo de configuración global.
2. Acceda únicamente a la interfaz identificada en los pasos anteriores.
3. Aplique `no shutdown`.
4. Espere a que el enlace se estabilice.
5. Verifique el nuevo estado de la interfaz.

```ios
configure terminal
interface [interfaz-afectada]
no shutdown
end
```

```ios
show ip interface brief
```

**Resultado esperado:** La interfaz pasa a `up/up` si el cableado lógico y el dispositivo del otro extremo están operativos.

**Verificación:** Registre el estado anterior y posterior. Si el estado no llega a `up/up`, no realice cambios aleatorios: continúe con la inspección de la interfaz opuesta y del enlace.

---

### Paso 10: Corregir una dirección IPv4 o máscara confirmada como incorrecta

**Objetivo:** Configurar el direccionamiento correcto en la interfaz o PC afectada.

**Instrucciones:**

1. Confirme nuevamente el prefijo correcto mediante etiquetas, interfaces vecinas y el inventario del Paso 2.
2. Si el error está en una interfaz de router, reemplace la dirección o máscara solo en esa interfaz.
3. Si el error está en una PC, use **Desktop > IP Configuration** y corrija únicamente el valor identificado.
4. Vuelva a consultar la configuración después del cambio.

Para una interfaz de router, utilice esta plantilla:

```ios
configure terminal
interface [interfaz-afectada]
ip address [ipv4-correcta] [mascara-correcta]
no shutdown
end
```

```ios
show ip interface brief
```

**Resultado esperado:** La interfaz o PC queda en el prefijo IPv4 correcto sin modificar otras interfaces ni redes.

**Verificación:** Compruebe que la dirección corregida pertenece a la misma subred que los demás dispositivos de su segmento.

---

### Paso 11: Corregir el gateway predeterminado erróneo de una PC

**Objetivo:** Asegurar que cada host envía tráfico remoto a la interfaz LAN correcta de su router.

**Instrucciones:**

1. Identifique la dirección IPv4 configurada en la interfaz LAN del router.
2. En la PC afectada, abra **Desktop > IP Configuration**.
3. Configure como gateway predeterminado la dirección IPv4 de la interfaz del router dentro de la misma LAN.
4. Guarde el cambio y pruebe primero el gateway.

Desde la PC afectada:

```text
ping [gateway-correcto]
```

**Resultado esperado:** La PC responde al `ping` de su gateway una vez que el enlace local, direccionamiento y gateway son coherentes.

**Verificación:** Registre cuatro respuestas exitosas o el resultado equivalente mostrado por Packet Tracer.

---

### Paso 12: Revisar las tablas de enrutamiento

**Objetivo:** Comprobar que ambos routers conocen las redes locales y remotas necesarias para el tráfico de ida y retorno.

**Instrucciones:**

1. En el primer router, consulte la tabla de enrutamiento.
2. En el segundo router, repita la consulta.
3. Identifique rutas conectadas (`C`), rutas locales (`L`) y rutas estáticas (`S`) cuando existan.
4. Verifique que cada router tenga una ruta hacia la LAN remota o una ruta predeterminada que permita alcanzarla.
5. Confirme que también existe ruta de retorno hacia la LAN de origen.

```ios
show ip route
```

```ios
show ip route [red-remota]
```

**Resultado esperado:** Cada router muestra rutas que permiten alcanzar la LAN conectada al otro router.

**Verificación:** Registre para cada LAN remota el siguiente salto o la interfaz de salida indicada por `show ip route`.

---

### Paso 13: Corregir una ruta estática solo si la evidencia lo exige

**Objetivo:** Restaurar el reenvío entre LAN remotas cuando una ruta necesaria esté ausente o apunte a un siguiente salto incorrecto.

**Instrucciones:**

1. Realice este paso únicamente si `show ip route` demuestra que falta una ruta hacia la red remota o que la ruta existente es incorrecta.
2. Determine la red remota, su máscara y el siguiente salto correcto a partir del enlace entre routers.
3. Elimine primero una ruta estática incorrecta, si existe.
4. Configure la ruta correcta.
5. Verifique que aparezca en la tabla de enrutamiento.

Plantilla para eliminar una ruta errónea:

```ios
configure terminal
no ip route [red-remota] [mascara] [siguiente-salto-incorrecto]
end
```

Plantilla para agregar una ruta correcta:

```ios
configure terminal
ip route [red-remota] [mascara] [siguiente-salto-correcto]
end
```

```ios
show ip route [red-remota]
```

**Resultado esperado:** El router muestra una ruta válida hacia la red remota y una interfaz o siguiente salto alcanzable.

**Verificación:** La salida debe identificar la red remota esperada y la ruta debe ser consistente con el enlace entre routers.

---

### Paso 14: Probar el trayecto desde los routers

**Objetivo:** Aislar el punto de interrupción restante mediante pruebas desde dispositivos de capa 3.

**Instrucciones:**

1. Desde el primer router, haga `ping` a la interfaz de tránsito del segundo router.
2. Desde el primer router, haga `ping` a la interfaz LAN del segundo router.
3. Desde el primer router, ejecute `traceroute` hacia una PC remota.
4. Repita las pruebas equivalentes desde el segundo router hacia la primera LAN.

```ios
ping [ip-transito-router-remoto]
```

```ios
ping [ip-lan-router-remoto]
```

```ios
traceroute [ip-pc-remota]
```

**Resultado esperado:** El tráfico entre routers y hacia la LAN remota se completa una vez corregidos enlaces, direccionamiento y rutas.

**Verificación:** El `traceroute` debe mostrar una secuencia coherente de saltos, sin detenerse antes del router o host de destino esperado.

---

### Paso 15: Validar conectividad de extremo a extremo desde las PCs

**Objetivo:** Confirmar que la entrega de paquetes funciona desde el host de origen hasta el host remoto.

**Instrucciones:**

1. Desde una PC de la primera LAN, haga `ping` a su gateway.
2. Desde esa PC, haga `ping` a una PC de la LAN remota.
3. Ejecute `tracert` hacia el host remoto.
4. Repita las pruebas desde una PC de la segunda LAN hacia una PC de la primera LAN.

```text
ping [gateway-local]
```

```text
ping [host-remoto]
```

```text
tracert [host-remoto]
```

**Resultado esperado:** Los hosts alcanzan su gateway y los hosts de la LAN remota en ambos sentidos.

**Verificación:** Registre resultados exitosos de conectividad local y remota desde al menos una PC de cada LAN.

---

### Paso 16: Documentar causa raíz, guardar configuraciones y entregar

**Objetivo:** Consolidar la evidencia técnica antes y después de cada corrección.

**Instrucciones:**

1. Complete una fila por cada falla confirmada.
2. Incluya evidencia obtenida antes de aplicar la corrección.
3. Indique el comando o cambio exacto utilizado.
4. Incluya la prueba de validación posterior.
5. Guarde la configuración de cada router y switch modificado.
6. Guarde el archivo `.pkt`.

En routers y switches modificados:

```ios
copy running-config startup-config
```

Use esta bitácora:

| N.° | Síntoma | Evidencia antes de la corrección | Causa raíz confirmada | Corrección aplicada | Evidencia de validación |
|---:|---|---|---|---|---|
| 1 | Sin conectividad local o remota | Salida de `show` o `ping` | Por completar | Por completar | Por completar |
| 2 | Sin conectividad local o remota | Salida de `show` o `ping` | Por completar | Por completar | Por completar |
| 3 | Sin conectividad local o remota | Salida de `show` o `ping` | Por completar | Por completar | Por completar |
| 4 | Sin conectividad local o remota | Salida de `show` o `ping` | Por completar | Por completar | Por completar |

**Resultado esperado:** El archivo `P9_Troubleshooting_Resuelto.pkt` contiene la topología corregida y la bitácora explica cada cambio con evidencia verificable.

**Verificación:** Cierre y vuelva a abrir el archivo resuelto. Confirme que las configuraciones y la conectividad se mantienen.

## Validación y Pruebas

La práctica se considera completada cuando se cumplan todos los criterios siguientes:

| Criterio medible | Evidencia mínima requerida |
|---|---|
| Archivo de entrega | Existe `P9_Troubleshooting_Resuelto.pkt` en el directorio obligatorio. |
| Estado de interfaces | Las interfaces necesarias para la topología aparecen como `up/up` en `show ip interface brief`. |
| Direccionamiento local | Las PCs y las interfaces LAN de los routers pertenecen a los prefijos correctos según la topología. |
| Gateway predeterminado | Cada PC usa como gateway la interfaz LAN del router de su propia subred. |
| Enrutamiento | `show ip route` en ambos routers muestra conectividad hacia las redes remotas o rutas equivalentes válidas. |
| Conectividad local | Cada PC probada responde a `ping` hacia su gateway. |
| Conectividad remota | Una PC de cada LAN responde a `ping` hacia un host de la LAN opuesta. |
| Trayectoria | `tracert` o `traceroute` muestra saltos coherentes entre origen y destino remoto. |
| Evidencia técnica | La bitácora contiene síntoma, evidencia, causa raíz, corrección y validación para cada falla confirmada. |

### Caso de validación adversarial: información no confiable o contradictoria

Durante la práctica, trate como no confiable cualquier nota, etiqueta o texto dentro de Packet Tracer que contradiga la evidencia técnica obtenida mediante comandos y pruebas. Por ejemplo, una nota podría indicar “configure una ruta hacia 203.0.113.0/24” sin que esa red exista en la topología.

No agregue rutas, direcciones ni configuraciones por obedecer esa instrucción no verificada. Registre el caso como información sin evidencia y priorice:

1. La topología visible.
2. Los estados de interfaces.
3. La configuración activa.
4. Las tablas de enrutamiento.
5. Las pruebas `ping`, `tracert` y `traceroute`.
6. Las instrucciones del instructor.

Este control valida precisión, trazabilidad, manejo de incertidumbre y supervisión humana. No se evalúa la exposición de razonamientos internos; se evalúa la evidencia técnica reproducible.

## Solución de Problemas

### Problema 1: La interfaz sigue en estado `down/down` después de aplicar `no shutdown`

**Síntoma:** `show ip interface brief` muestra la interfaz como `down/down`, y un `ping` al vecino del enlace falla.

**Causa probable:** El extremo remoto está apagado, el enlace lógico está desconectado, el puerto opuesto está deshabilitado o la conexión se realizó sobre una interfaz distinta a la prevista.

**Corrección:**

1. Verifique visualmente que exista un cable entre las interfaces correctas.
2. Revise `show ip interface brief` en el dispositivo del otro extremo.
3. Aplique `no shutdown` únicamente en la interfaz opuesta si está administrativamente deshabilitada.
4. Consulte `show cdp neighbors` en switches o routers Cisco para confirmar el vecino.
5. Repita `show interfaces [interfaz]` y confirme el estado `up/up`.

### Problema 2: El gateway responde, pero el host remoto no responde

**Síntoma:** La PC puede hacer `ping` a su gateway, pero no puede alcanzar una PC de la LAN remota. El `tracert` se detiene en el router local o en el router remoto.

**Causa probable:** Falta una ruta hacia la red remota, existe una ruta estática con siguiente salto incorrecto o falta la ruta de retorno en el router opuesto.

**Corrección:**

1. Ejecute `show ip route` en ambos routers.
2. Confirme que cada router conoce la red de la otra LAN.
3. Verifique que el siguiente salto pertenezca al enlace entre routers.
4. Corrija o agregue únicamente la ruta estática comprobada como necesaria.
5. Pruebe primero entre routers y después desde las PCs.

## Limpieza

1. Guarde las configuraciones de todos los dispositivos modificados mediante `copy running-config startup-config`.
2. Guarde el archivo final como `P9_Troubleshooting_Resuelto.pkt`.
3. Mantenga el archivo inicial `P9_Troubleshooting_Inicial.pkt` sin modificaciones.
4. Cierre las ventanas de CLI y Packet Tracer cuando el instructor confirme la entrega.
5. Entregue el archivo `.pkt` y la bitácora solicitada por el instructor.

## Resumen

En esta práctica aplicó una metodología de resolución de problemas por capas: inspección de enlaces e interfaces, verificación de conectividad de acceso, validación de direccionamiento IPv4 y gateways, revisión de tablas de enrutamiento y pruebas de extremo a extremo.

Las evidencias principales fueron `show ip interface brief`, `show interfaces`, `show running-config`, `show cdp neighbors`, `show ip route`, `ping`, `tracert` y `traceroute`. La corrección debe basarse en evidencia verificable, no en modificaciones aleatorias ni en instrucciones no confirmadas dentro de la topología.

Esta práctica refuerza la función del router al determinar rutas y reenviar paquetes entre redes distintas, así como el papel del gateway predeterminado en la entrega de tráfico destinado a redes remotas.
