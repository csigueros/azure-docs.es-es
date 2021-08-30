---
title: Conexiones de red en el Explorador de Azure Storage | Microsoft Docs
description: Documentación sobre la conexión a la red en el Explorador de Azure Storage
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: a4afdc8897e15fcb563812bda9da378c66ca4bd9
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432435"
---
# <a name="network-connections-in-storage-explorer"></a>Conexiones de red en el Explorador de Storage

Cuando no se conecta a un emulador local, el Explorador de Storage usa la red para realizar solicitudes a los recursos de almacenamiento y a otros servicios de Azure y Microsoft.

## <a name="hostnames-accessed-by-storage-explorer"></a>Nombres de host a los que accede el Explorador de Storage

El Explorador de Storage realiza solicitudes a varios puntos de conexión mientras está en uso. En la lista siguiente se detallan los nombres de host comunes a los que el Explorador de Storage realizará solicitudes:
- Puntos de conexión de ARM:
    - `management.azure.com` (Azure global)
    - `management.chinacloudapi.cn` (Azure China)
    - `management.microsoftazure.de` (Azure Alemania)
    - `management.usgovcloudapi.net` (Azure Gobierno de EE. UU.)
- Puntos de conexión de inicio de sesión:
    - `login.microsoftonline.com` (Azure global)
    - `login.chinacloudapi.cn` (Azure China)
    - `login.microsoftonline.de` (Azure Alemania)
    - `login.microsoftonline.us` (Azure Gobierno de EE. UU.)
- Puntos de conexión de Graph:
    - `graph.windows.net` (Azure global)
    - `graph.chinacloudapi.cn` (Azure China)
    - `graph.cloudapi.de` (Azure Alemania)
    - `graph.windows.net` (Azure Gobierno de EE. UU.)
- Puntos de conexión de Azure Storage:
    - `(blob|file|queue|table|dfs).core.windows.net` (Azure global)
    - `(blob|file|queue|table|dfs).core.chinacloudapi.net` (Azure China)
    - `(blob|file|queue|table|dfs).core.cloudapi.net` (Azure Alemania)
    - `(blob|file|queue|table|dfs).core.cloudapi.net` (Azure Gobierno de EE. UU.)
- Actualización del Explorador de Storage: `storageexplorerpublish.blob.core.windows.net` 
- Reenvío de vínculos de Microsoft:
    - `aka.ms`
    - `go.microsoft.com`
- Dominios personalizados, vínculos privados o puntos de conexión específicos de instancia de Azure Stack, donde los recursos estén detrás
- Nombres de host del emulador remoto

## <a name="proxy-sources"></a>Orígenes de proxy

El Explorador de Storage tiene varias opciones para cómo y dónde puede obtener la información necesaria para conectarse al proxy. Para cambiar la opción que se usa, vaya a **Configuración** (icono de engranaje de la barra de herramientas vertical izquierda) > **Aplicación** > **Proxy**. Una vez que se encuentra en la sección de configuración del proxy, puede seleccionar cómo y dónde quiere que el Explorador de Storage obtenga la configuración del proxy:
- No utilizar proxy
- Uso de variables de entorno
- Use app proxy settings (Usar configuración de proxy de la aplicación)
- Use system proxy (preview) (Usar proxy del sistema [versión preliminar])

### <a name="do-not-use-proxy"></a>No utilizar proxy

Cuando se selecciona esta opción, el Explorador de Storage no intenta conectarse a un proxy. La opción predeterminada es No utilizar proxy.

### <a name="use-environment-variables"></a>Uso de variables de entorno

Cuando se selecciona esta opción, el Explorador de Storage busca información del proxy en las variables de entorno específicas. Estas variables son:
- `HTTP_PROXY`
- `HTTPS_PROXY`

Si ambas variables están definidas, el Explorador de Storage obtiene la información del proxy de `HTTPS_PROXY`.

El valor de estas variables de entorno debe ser una dirección URL con el formato:

`(http|https)://(username:password@)<hostname>:<port>`

Solo se requieren el protocolo (`http|https`) y el nombre de host. Si tiene un nombre de usuario, no tiene que proporcionar una contraseña.

### <a name="use-app-proxy-settings"></a>Use app proxy settings (Usar configuración de proxy de la aplicación)

Cuando se selecciona esta opción, el Explorador de Storage usa la configuración del proxy de aplicación. Estas opciones incluyen:
- Protocolo
- Nombre de host
- Port
- Usar/no usar credenciales
- Credenciales

Todas las configuraciones que no sean credenciales se pueden administrar desde:
- **Configuración** (icono de engranaje de la barra de herramientas vertical izquierda) > **Aplicación** > **Proxy** > **Utilizar credenciales**.
- El cuadro de diálogo Configuración del proxy (**Editar** > **Configurar proxy**).

Para establecer las credenciales, debe ir al cuadro de diálogo Configuración del proxy (**Editar** > **Configurar proxy**).

### <a name="use-system-proxy-preview"></a>Use system proxy (preview) (Usar proxy del sistema [versión preliminar])

Cuando se selecciona esta opción, el Explorador de Storage usa la configuración del proxy del sistema operativo. Más concretamente, las llamadas de red se realizan mediante la pila de red Chromium. La pila de red Chromium es mucho más sólida que la pila de red NodeJS usada normalmente por el Explorador de Storage. Este es un fragmento de código de la [documentación de Chromium](https://www.chromium.org/developers/design-documents/network-settings) sobre lo que puede hacer:

> La pila de red Chromium usa la configuración de red del sistema para que los usuarios y administradores puedan controlar fácilmente la configuración de red de todas las aplicaciones. La configuración de red incluye:
> - Configuración del proxy
> - Configuración de SSL/TLS
> - Configuración de comprobación de revocación de certificados
> - Almacenes de certificados y claves privadas

Si el servidor proxy requiere credenciales y esas credenciales no están configuradas en el sistema operativo, deberá habilitar el uso de las credenciales y establecerlas en el Explorador de Storage. Puede alternar el uso de credenciales desde:
- **Configuración** (icono de engranaje de la barra de herramientas vertical izquierda) > **Aplicación** > **Proxy** > **Utilizar credenciales**.
- El cuadro de diálogo Configuración del proxy (**Editar** > **Configurar proxy**).

Para establecer las credenciales, debe ir al cuadro de diálogo Configuración del proxy (**Editar** > **Configurar proxy**).

Esta opción está en versión preliminar porque no todas las características admiten actualmente el proxy del sistema. Consulte las [características que admiten el proxy del sistema](#features-that-support-system-proxy) para obtener una lista completa de características. Cuando el proxy del sistema está habilitado, las características que no lo admiten no intentarán conectarse a un proxy.

Si se encuentra con problemas al usar el proxy del sistema con una característica compatible, [abra una incidencia en GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

## <a name="proxy-server-authentication"></a>Autenticación del servidor proxy

Si ha configurado el Explorador de Storage para obtener la configuración de proxy de las **variables de entorno** o para usar la **configuración de proxy de la aplicación**, solo se admiten los servidores proxy que usan la autenticación básica.

Si ha configurado el Explorador de Storage para usar el **proxy del sistema**, se admiten los servidores proxy que usan cualquiera de los métodos de autenticación siguientes:
- Básico
- Digest
- NTLM
- Negotiate

## <a name="which-proxy-source-should-i-choose"></a>¿Qué origen de proxy debo elegir?

Si solo usa [características que admiten el proxy del sistema](#features-that-support-system-proxy), primero debe intentar usar el [**proxy del sistema**](#use-system-proxy-preview). Si se encuentra con problemas al usar el proxy del sistema con una característica compatible, [abra una incidencia en GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

Si usa características que no admiten el proxy del sistema, la [**configuración de la aplicación**](#use-app-proxy-settings) probablemente sea la siguiente mejor opción. La experiencia basada en la interfaz gráfica de usuario para configurar el proxy contribuye a reducir la posibilidad de especificar la información del proxy correctamente. Sin embargo, si ya tiene configuradas variables de entorno de proxy, podría ser mejor usar [**variables de entorno**](#use-environment-variables).

## <a name="azcopy-proxy-usage"></a>Uso del proxy de AzCopy

El Explorador de Storage usa AzCopy para la mayoría de las operaciones de transferencia de datos. AzCopy se escribe con un conjunto de tecnologías diferentes a las del Explorador de Storage y, por tanto, tiene un conjunto de funcionalidades de proxy ligeramente diferentes.

Si el Explorador de Storage está configurado para **no usar el proxy** o para usar el **proxy del sistema**, se le pedirá a AzCopy que use sus propias características de proxy de detección automática para determinar si debe realizar solicitudes a un proxy y cómo hacerlas. Si ha configurado el Explorador de Storage para obtener la configuración de proxy de las **variables de entorno** o para usar la **configuración de proxy de la aplicación**, el Explorador de Storage indicará a AzCopy que use la misma configuración de proxy.

Si no quiere que AzCopy use proxy sin importar cuál sea, puede deshabilitar el uso del proxy; para ello, vaya a **Configuración** (icono de engranaje de la barra de herramientas vertical izquierda) > **Transferencias** > **AzCopy** > **Disable AzCopy Proxy Usage** (Deshabilitar uso de proxy de AzCopy).

Actualmente, AzCopy solo admite servidores proxy que usan la **autenticación básica**.

## <a name="ssl-certificates"></a>Certificados SSL

De forma predeterminada, el Explorador de Storage usa la pila de red NodeJS. NodeJS incluye una lista predefinida de certificados SSL de confianza. Algunas tecnologías de red, como servidores proxy o software antivirus, insertan sus propios certificados SSL en el tráfico de red. Estos certificados no suelen estar presentes en la lista de certificados de NodeJS. NodeJS no confiará en las respuestas que contengan este certificado. Cuando NodeJS no confíe en una respuesta, el Explorador de Storage recibirá un error.

Tiene varias opciones para resolver estos errores:
- Usar el [**proxy del sistema**](#use-system-proxy-preview) como origen de proxy.
- Importar una copia de los certificados SSL que provocan los errores.
- Deshabilitar el certificado SSL. (**no se recomienda**)

## <a name="features-that-support-system-proxy"></a>Características que admiten el proxy del sistema

En la siguiente lista se muestran las características que admiten el **proxy del sistema**:

- Comprobación y descarga de actualizaciones
- Enumeración de suscripciones
- Características de la cuenta de Storage
    - Lista
- Características de blob
    - Contenedores
        - Crear
        - Lista
        - Administración de directivas de acceso almacenadas
        - Cambio del nivel de acceso público
        - Arrendamiento
        - Propiedades
        - Eliminar
    - Blobs
        - Lista
        - Estadísticas
        - Deshacer eliminación
    - Blobs de ADLS Gen2
        - Lista
        - Estadísticas
        - Administración de listas ACL (solo visualización y modificación de entidades existentes)
        - Propagación de listas ACL
        - Move
        - Cambiar nombre
        - Crear carpeta
- Características de cola
    - Queues
        - Crear
        - Lista
        - Administración de directivas de acceso
        - Propiedades
        - Eliminar
        - Borrar
    - Mensajes de la cola
        - Lista
        - Move
        - Sumar
        - Quitar de la cola
- Características de recursos compartidos de archivos
    - Archivos y carpetas
        - Nueva carpeta
        - Propiedades
- Características de disco
    - Enumeración de grupos de recursos
    - Enumeración de discos
    - Carga de discos
    - Descarga de discos
    - Copia de discos
    - Creación de instantáneas
    - Eliminación de discos

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de proxy](./storage-explorer-troubleshooting.md#proxy-issues)
* [Solución de problemas de certificados](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
