---
title: Uso de los comandos de la CLI de Defender para IoT
description: En este artículo se describen los comandos de la CLI de Defender para IoT de sensores y consolas de administración locales.
ms.date: 05/20/2021
ms.topic: article
ms.openlocfilehash: bac3e84d77436e9dfc500baa9f729ba847c49bd4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018425"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Uso de los comandos de la CLI de Defender para IoT

En este artículo se describen los comandos de la CLI de sensores y consolas de administración locales. Los comandos son accesibles para los siguientes usuarios:

- Administrador
- CyberX 
- Soporte técnico

Para empezar a trabajar en la CLI, conéctese mediante un terminal. Por ejemplo, nombre de terminal `Putty` y usuario `Support`. 

## <a name="create-local-alert-exclusion-rules"></a>Creación de reglas de exclusión de alertas locales

Puede crear una regla de exclusión de alerta local si especifica el siguiente comando en la CLI:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Se pueden usar los siguientes atributos con las reglas de exclusión de alertas:

| Atributo | Descripción |
|--|--|
| [-h] | Imprime la información de ayuda del comando. |
| -n NAME | Nombre de la regla que se va a crear. |
| [-ts TIMES] | Intervalo de tiempo durante el que la regla está activa. Se debe especificar como:<br />`xx:yy-xx:yy`<br />Puede definir más de un período de tiempo si coloca una coma entre ellos. Por ejemplo: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir DIRECTION] | Dirección en la que se aplica la regla. Se debe especificar como:<br />`both | src | dst` |
| [-dev DEVICES] | Dirección IP y tipo de dirección de los dispositivos que se van a excluir en virtud de la regla, especificados como:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTS] | Nombre de la alerta que la regla va a excluir:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Anexión de reglas de exclusión de alertas locales

Para anexar reglas de exclusión de alertas locales, especifique el siguiente comando en la CLI:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Los atributos que se usan aquí son los mismos que los que se explican en la sección Creación de reglas de exclusión de alertas locales. La diferencia en el uso es que aquí los atributos se aplican a las reglas existentes.

## <a name="show-local-alert-exclusion-rules"></a>Visualización de reglas de exclusión de alertas locales

Escriba el siguiente comando para presentar la lista de reglas de exclusión existente:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Eliminación de reglas de exclusión de alertas locales

Puede eliminar una regla de exclusión de alertas existente si especifica el siguiente comando:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Se puede usar el siguiente atributo con las reglas de exclusión de alertas:

| Atributo | Descripción|
| --------- | ---------------------------------- |
| -n NAME | Nombre de la regla que se va a eliminar. |

## <a name="sync-time-from-the-ntp-server"></a>Hora de sincronización desde el servidor NTP

Puede habilitar o deshabilitar una sincronización de hora desde un servidor NTP especificado.

### <a name="enable-ntp-sync"></a>Habilitación de sincronización NTP

Escriba el siguiente comando para recuperar periódicamente la hora del servidor NTP especificado:

```azurecli-interactive
ntp enable IP
```

El atributo que se puede definir en el comando es la dirección IP del servidor NTP.

### <a name="disable-ntp-sync"></a>Deshabilitación de sincronización NTP

Especifique el siguiente comando para deshabilitar la sincronización de hora con el servidor NTP especificado:

```azurecli-interactive
ntp disable IP
```

El atributo que se puede definir en el comando es la dirección IP del servidor NTP.

## <a name="network-configuration"></a>Network configuration (Configuración de red)

En la tabla siguiente se describen los comandos disponibles para configurar las opciones de red de Azure Defender para IoT:

|Nombre|Get-Help|Descripción|
|-----------|-------|-----------|
|Ping|`ping IP`| Hace ping a una dirección de fuera de la plataforma Defender para IoT.|
|Blink|`network blink`| Busca una conexión haciendo que las luces de la interfaz parpadeen. |
|Volver a configurar la red |`network edit-settings`| Permite cambiar los parámetros de configuración de red. |
|Mostrar la configuración de red |`network list`|Muestra los parámetros del adaptador de red. |
|Validar la configuración de red |`network validate` |Presenta la configuración de red de salida. <br /> <br />Por ejemplo: <br /> <br />Configuración de red actual: <br /> interfaz: eth0 <br /> ip: 10.100.100.1 <br />subred: 255.255.255.0 <br />puerta de enlace predeterminada: 10.100.100.254 <br />dns: 10.100.100.254 <br />interfaces de supervisión: eth1|
|Importación de un certificado |`certificate import FILE` |Importa el certificado HTTPS. Debe especificar la ruta de acceso completa, que lleva a un archivo \*.crt. |
|Mostrar la fecha |`date` |Devuelve la fecha actual del host en formato GMT. |

## <a name="network-capture-filter-configuration"></a>Configuración del filtro de captura de red

El comando `network capture-filter` permite a los administradores eliminar el tráfico de red que no es necesario analizar. Puede filtrar el tráfico mediante una lista de inclusión o exclusión.

```azurecli-interactive
network capture-filter
```

Después de escribir el comando, se le pregunta lo siguiente:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Seleccione `Y` para abrir un archivo de Nano, donde puede agregar dispositivos, canales, puertos y subconjuntos de acuerdo con la siguiente sintaxis:

| Atributo | Descripción |
|--|--|
| 1.1.1.1 | Incluye todo el tráfico de este dispositivo. |
| 1.1.1.1,2.2.2.2 | Incluye todo el tráfico de este canal. |
| 1.1.1,2.2.2 | Incluye todo el tráfico de esta subred. |

Separe los argumentos quitando una fila.

Cuando se incluye un dispositivo, un canal o una subred, el sensor procesa todo el tráfico válido de ese argumento, incluidos puertos y tráfico que normalmente no se procesarían.

Luego se le pregunta lo siguiente:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Seleccione `Y` para abrir un archivo de Nano, donde puede agregar dispositivos, canales, puertos y subconjuntos de acuerdo con la siguiente sintaxis:

| Atributo | Descripción |
|--|--|
| 1.1.1.1 | Excluye todo el tráfico de este dispositivo. |
| 1.1.1.1,2.2.2.2 | Excluye todo el tráfico de este canal, lo que significa todo el tráfico entre dos dispositivos. |
| 1.1.1.1,2.2.2.2,443 | Excluye todo el tráfico de este canal por puerto. |
| 1.1.1 | Excluye todo el tráfico de esta subred. |
| 1.1.1,2.2.2 | Excluye todo el tráfico entre subredes. |

Separe los argumentos quitando una fila.

Cuando se excluye un dispositivo, un canal o una subred, el sensor excluye todo el tráfico válido de ese argumento.

### <a name="ports"></a>Puertos

Incluya o excluya puertos UDP y TCP en todo el tráfico.

>`502`: puerto único

>`502,443`: ambos puertos

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Componentes

Se le pregunta lo siguiente:

>`In which component do you wish to apply this capture filter?`

Las opciones son: `all`, `dissector`, `collector`, `statistics-collector`, `rpc-parser` o `smb-parser`.

En la mayoría de los casos de uso, seleccione `all`.

### <a name="custom-base-capture-filter"></a>Filtro de captura base personalizado

El filtro de captura base es la base de referencia de los componentes. Por ejemplo, el filtro determina qué puertos hay disponibles para el componente.

Seleccione `Y` para todas las opciones siguientes. Todos los filtros se agregan a la base de referencia una vez establecidos los cambios. Si realiza un cambio, sobrescribe la base de referencia existente.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Si decide excluir una subred como 1.1.1:

- `internal` excluye solo esa subred.

- `all-connected` excluye esa subred y todo el tráfico hacia y desde ella.

Se recomienda seleccionar `internal`.

> [!NOTE]
> Las opciones se usan para todos los filtros de la herramienta y no dependen de la sesión. Es decir, nunca se puede elegir `internal`  para algunos filtros y `all-connected`  para otros.

### <a name="comments"></a>Comentarios

Puede ver los filtros de  ```/var/cyberx/properties/cybershark.properties```:

- **statistics-collector**: `bpf_filter property` de  ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector**: propiedad `override.capture_filter`  de  ```/var/cyberx/properties/cybershark.properties```

- **rpc-parser**: propiedad `override.capture_filter` de  ```/var/cyberx/properties/rpc-parser.properties```

- **smb-parser**: propiedad  `override.capture_filter`  de  ```/var/cyberx/properties/smb-parser.properties```

- **collector**: propiedad `general.bpf_filter`  de  ```/var/cyberx/properties/collector.properties```

Puede restaurar la configuración predeterminada si escribe el código siguiente para el usuario de CyberX:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definición de hosts de cliente y servidor

Si Defender para IoT no ha detectado automáticamente los hosts de cliente y servidor, escriba el siguiente comando para establecerlos:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Puede usar los atributos siguientes con el comando `directions`:

| Atributo | Descripción |
|--|--|
| [-h] | Imprime información de ayuda del comando. |
| [--identifier IDENTIFIER] | Identificador del servidor. |
| [--port PORT] | Puerto del servidor. |
| [--remove] | Quita un host de cliente o servidor de la lista. |
| [--add] | Agrega un host de cliente o servidor a la lista. |
| [--tcp] | Use TCP al comunicarse con este host. |
| [--udp] | Use UDP al comunicarse con este host. |

## <a name="system-actions"></a>Acciones del sistema
En la tabla siguiente se describen los comandos disponibles para realizar varias acciones del sistema en Defender para IoT:

|Nombre|Código|Descripción|
|----|----|-----------|
|Mostrar la fecha|`date`|Devuelve la fecha actual del host en formato GMT.|
|Reiniciar el host|`system reboot`|Reinicia el dispositivo host.|
|Apagar el host|`system shutdown`|Apaga el host.|
|Copia de seguridad del sistema|`system backup`|Inicia una copia de seguridad inmediata (una copia de seguridad no programada).|
|Restaurar el sistema a partir de una copia de seguridad|`system restore`|Restaura a partir de la copia de seguridad más reciente.|
|Enumerar los archivos de copia de seguridad|`system backup-list`|Enumera los archivos de copia de seguridad disponibles.|
|Mostrar el estado de todos los servicios de la plataforma Defender para IoT|`system sanity`|Comprueba el rendimiento del sistema mediante la visualización del estado actual de todos los servicios de la plataforma Defender para IoT.|
|Mostrar la versión de software|`system version`|Muestra la versión del software que se está ejecutando en ese momento en el sistema.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Implementación de certificados SSL y TLS en dispositivos

Escriba el siguiente comando para importar certificados de empresa SSL y TLS en la CLI:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Para usar la herramienta, debe cargar los archivos de certificado en el dispositivo. Puede hacerlo mediante herramientas como WinSCP o Wget. 

El comando admite las marcas de entrada siguientes:

| Marca | Descripción |
|--|--|
| -H | Muestra la sintaxis de la ayuda de la línea de comandos. |
| --crt | Ruta de acceso al archivo de certificado (extensión .crt). |
| --key | Archivo \*.key. La longitud de la clave debe ser de 2048 bits como mínimo. |
| --chain | Ruta de acceso al archivo de cadena de certificados (opcional). |
| --pass | Frase de contraseña usada para cifrar el certificado (opcional). |
| --passphrase-set | El valor predeterminado es **False**, **unused**. <br />Establezca en **True** para usar la frase de contraseña anterior proporcionada con el certificado previo (opcional). | 

Cuando use la herramienta:

- Compruebe que los archivos de certificado se puedan leer en el dispositivo. 

- Confirme con TI el dominio del dispositivo (tal como aparece en el certificado) con el servidor DNS y la dirección IP correspondiente. 
    
## <a name="see-also"></a>Consulte también

[API del sensor y la consola de administración de Defender para IoT](references-work-with-defender-for-iot-apis.md)
