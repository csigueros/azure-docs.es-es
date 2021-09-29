---
title: Solución de problemas de la puerta de enlace de administración de datos
description: Ofrece sugerencias para solucionar problemas relacionados con la puerta de enlace de administración de datos.
author: nabhishek
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: e136ec96cc2a698f0b0e794830403dcbc284341c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554247"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Solución de problemas con Data Management Gateway
En este artículo se ofrece información sobre la solución de problemas con Data Management Gateway.

> [!NOTE]
> Este artículo se aplica a la versión 1 de Azure Data Factory. Si usa la versión actual del servicio Data Factory, que se encuentra, consulte el artículo sobre [IR autohospedado en Data Factory](../create-self-hosted-integration-runtime.md).

Para más información sobre la puerta de enlace, vea el artículo [Puerta de enlace de administración de datos](data-factory-data-management-gateway.md). En el artículo [Mover datos entre implementaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) puede ver un tutorial sobre cómo mover datos entre una base de datos de SQL Server a Microsoft Azure Blob Storage usando la puerta de enlace.

## <a name="failed-to-install-or-register-gateway"></a>Error al instalar o registrar la puerta de enlace
### <a name="1-problem"></a>1. Problema
Se muestra este mensaje de error al instalar y registrar una puerta de enlace (en concreto, al descargar el archivo de instalación de la puerta de enlace).

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Causa
La máquina donde intenta instalar la puerta de enlace no puede descargar la versión más reciente del archivo de instalación de la puerta de enlace del Centro de descarga debido a un problema de red.

#### <a name="resolution"></a>Resolución
Compruebe si la configuración del firewall o del servidor proxy bloquea la conexión de red desde el equipo al [Centro de descarga](https://download.microsoft.com/) y actualice la configuración según corresponda.

Como alternativa, puede descargar el archivo de instalación para la puerta de enlace más reciente desde el [centro de descarga](https://www.microsoft.com/download/details.aspx?id=39717) en otros equipos que pueden tener acceso al centro de descarga. A continuación, puede copiar el archivo del instalador en el equipo de host de puerta de enlace y ejecutarlo manualmente para instalar y actualizar la puerta de enlace.

### <a name="2-problem"></a>2. Problema
Se muestra este error al hacer clic en **Instalar directamente en este equipo** en Azure Portal para intentar instalar una puerta de enlace.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Causa
Ya hay instalada una puerta de enlace en la máquina.

#### <a name="resolution"></a>Resolución
Desinstale la puerta de enlace existente en la máquina y vuelva a hacer clic en el vínculo **Instalar directamente en este equipo**.

### <a name="3-problem"></a>3. Problema
Puede que vea este error al registrar una nueva puerta de enlace.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Causa
Puede que vea este mensaje por uno de los motivos siguientes:

* El formato de la clave de la puerta de enlace no es válido.
* Se ha invalidado la clave de la puerta de enlace.
* Se ha vuelto a generar la clave de la puerta de enlace desde el portal.  

#### <a name="resolution"></a>Resolución
Compruebe si usa la clave de la puerta de enlace correcta en el portal. Si es necesario, vuelva a generar una clave y úsela para registrar la puerta de enlace.

### <a name="4-problem"></a>4. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png" alt-text="Contenido o formato de la clave no son válidos":::

#### <a name="cause"></a>Causa
El contenido o el formato de la clave de la puerta de enlace de entrada no son correctos. Uno de los motivos puede ser que haya copiado solo una parte de la clave desde el portal o que esté usando una clave no válida.

#### <a name="resolution"></a>Resolución
Genere una clave de puerta de enlace en el portal y use el botón Copiar para copiar la clave completa. A continuación, péguela en esta ventana para registrar la puerta de enlace.

### <a name="5-problem"></a>5. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png" alt-text="Captura de pantalla que resalta el mensaje de error que indica que la clave de puerta de enlace no es válida o está vacía.":::

#### <a name="cause"></a>Causa
La clave de la puerta de enlace se ha vuelto a generar o la puerta de enlace se ha eliminado en Azure Portal. También puede ocurrir que el programa de instalación de Data Management Gateway no sea el más reciente.

#### <a name="resolution"></a>Resolución
Compruebe si el programa de instalación de Data Management Gateway es la versión más reciente. Puede encontrar la versión más reciente en el [Centro de descarga de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Si se trata de la versión más reciente o la actual y existe una puerta de enlace en el Portal, vuelva a generar una clave de la puerta de enlace en Azure Portal, use el botón Copiar para copiar la clave entera y, después, péguela en esta ventana para registrar la puerta de enlace. En caso contrario, vuelva a crear la puerta de enlace y empiece de nuevo.

### <a name="6-problem"></a>6. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace.

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png" alt-text="La clave de la puerta de enlace no es válida o está vacía":::

#### <a name="cause"></a>Causa
Este error puede producirse porque se ha eliminado la puerta de enlace o porque se ha vuelto generar la clave de la puerta de enlace asociada.

#### <a name="resolution"></a>Resolución
Si se ha eliminado la puerta de enlace, vuelva a crear la puerta de enlace desde el portal, haga clic en **Registrar**, copie la clave desde el portal, péguela y, después, intente registrar la puerta de enlace.

Si la puerta de enlace aún existe, pero su clave se ha vuelto a generar, use la nueva clave para registrar la puerta de enlace. Si no tiene la clave, vuelva a generarla desde el portal.

### <a name="7-problem"></a>7. Problema
Al registrar una puerta de enlace, es posible que tenga que especificar la ruta de acceso y la contraseña de un certificado.

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/specify-certificate.png" alt-text="Captura de pantalla que muestra dónde se escribe la ruta de acceso y la contraseña del certificado.":::

#### <a name="cause"></a>Causa
La puerta de enlace se ha registrado en otras máquinas anteriormente. Durante el registro inicial de una puerta de enlace se ha asociado un certificado de cifrado con la puerta de enlace. El certificado puede ser generado automáticamente por la puerta de enlace o puede proporcionarlo el usuario.  Este certificado se usa para cifrar las credenciales del almacén de datos (servicio vinculado).  

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/export-certificate.png" alt-text="Exportar un certificado":::

Al restaurar la puerta de enlace en un equipo host distinto, el asistente para registro pide este certificado para descifrar las credenciales que se han cifrado anteriormente con este certificado.  Sin este certificado, las credenciales no se pueden descifrar con la nueva puerta de enlace y las ejecuciones de actividades de copia posteriores asociadas con esta nueva puerta de enlace producirán errores.  

#### <a name="resolution"></a>Resolución
Si ha exportado el certificado de credenciales desde la máquina de la puerta de enlace original con el botón e **Exportar** de la pestaña **Configuración** del administrador de configuración de la puerta de enlace de administración de datos, use el certificado aquí.

No puede omitir esta fase al recuperar una puerta de enlace. Si falta el certificado, necesitará eliminar la puerta de enlace del portal y volver a crear una puerta de enlace.  Además, actualice todos los servicios vinculados relacionados con la puerta de enlace volviendo a escribir sus credenciales.

### <a name="8-problem"></a>8. Problema
Puede aparecer el siguiente mensaje de error.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Causa
Este error se produce cuando la puerta de enlace se encuentra en un entorno que necesita un proxy HTTP para acceder a recursos de Internet, o bien cuando se cambia la contraseña de autenticación del proxy, pero no se actualiza en la puerta de enlace.

#### <a name="resolution"></a>Resolución
Siga las instrucciones de la sección Consideraciones sobre el servidor proxy en este artículo y establezca la configuración del proxy con el Administrador de configuración de Data Management Gateway.

## <a name="gateway-is-online-with-limited-functionality"></a>La puerta de enlace está en línea con funcionalidad limitada
### <a name="1-problem"></a>1. Problema
El estado de la puerta de enlace es en línea con funciones limitadas.

#### <a name="cause"></a>Causa
El estado de la puerta de enlace es "en línea con funciones limitadas" por uno de los motivos siguientes:

* La puerta de enlace no se puede conectar al servicio en la nube mediante Azure Service Bus.
* El servicio en la nube no se puede conectar a la puerta de enlace mediante Service Bus.

Cuando la puerta de enlace está en línea con funcionalidad limitada, no podrá usar el Asistente para copia de Data Factory para crear canalizaciones de datos entre almacenes de datos locales. Como alternativa, puede usar Data Factory Editor en el portal, Visual Studio o Azure PowerShell.

#### <a name="resolution"></a>Resolución
La solución a este problema (en línea con funciones limitadas) depende de si la puerta de enlace se puede conectar al servicio en la nube o viceversa. En las secciones siguientes se describen estas soluciones.

### <a name="2-problem"></a>2. Problema
Verá este error.

`Error: Gateway cannot connect to cloud service through service bus`

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png" alt-text="La puerta de enlace no se puede conectar al servicio en la nube":::

#### <a name="cause"></a>Causa
La puerta de enlace no se puede conectar al servicio en la nube mediante Service Bus.

#### <a name="resolution"></a>Resolución
Siga estos pasos para poner en línea la puerta de enlace:

1. Permita reglas de salida de direcciones IP en la máquina de la puerta de enlace y en el firewall corporativo. Puede buscar las direcciones IP desde el registro de eventos de Windows (Id. == 401): Intento de obtener acceso a un socket de una manera no permitida por los permisos de acceso XX.XX.XX.XX:9350.
1. Configure los valores de proxy en la puerta de enlace. Para más información, consulte la sección Consideraciones sobre el servidor proxy.
1. Habilite los puertos de salida 5671 y 9350-9354 en el Firewall de Windows de la máquina de la puerta de enlace y en el firewall corporativo. Para más información, consulte la sección Puertos y firewall. Este paso es opcional, pero se recomienda por motivos de rendimiento.

### <a name="3-problem"></a>3. Problema
Verá este error.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Causa
Un error transitorio en la conectividad de red.

#### <a name="resolution"></a>Resolución
Siga estos pasos para poner en línea la puerta de enlace:

1. Espere un par de minutos; la conectividad se recuperará automáticamente cuando desaparezca el error.
1. Si el error persiste, reinicie el servicio de puerta de enlace.

## <a name="failed-to-author-linked-service"></a>Error al crear el servicio vinculado
### <a name="problem"></a>Problema
Puede que vea este error al intentar usar el Administrador de credenciales en el portal para especificar las credenciales de un nuevo servicio vinculado o para actualizar las credenciales de un servicio vinculado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Cuando aparece este error, la página de configuración del Administrador de configuración de Puerta de enlace de administración de datos tiene un aspecto similar al de la siguiente captura de pantalla.

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png" alt-text="No se puede establecer conexión con la base de datos":::

#### <a name="cause"></a>Causa
Es posible que se haya perdido el certificado TLS/SSL en la máquina de la puerta de enlace. El equipo de la puerta de enlace no puede cargar el certificado usado actualmente para el cifrado TLS. También puede ver un mensaje de error en el registro de eventos que es similar al siguiente mensaje.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Resolución
Siga estos pasos para solucionar el problema:

1. Inicie el Administrador de configuración de la puerta de enlace de administración de datos.
2. Cambie a la pestaña **Configuración** .  
3. Haga clic en el botón **Cambiar** para cambiar el certificado TLS/SSL.

   :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png" alt-text="Botón Cambiar certificado":::
4. Seleccione un nuevo certificado como certificado TLS/SSL. Puede usar cualquier certificado TLS/SSL que haya generado por su cuenta o que haya generado una organización.

   :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png" alt-text="Especificar certificado":::

## <a name="copy-activity-fails"></a>Error en la actividad de copia
### <a name="problem"></a>Problema
Puede que vea el error "UserErrorFailedToConnectToSqlserver" después de configurar una canalización en el portal.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Causa
Esto puede ocurrir por diferentes motivos y la mitigación varía según la causa.

#### <a name="resolution"></a>Resolución
Permita las conexiones TCP de salida a través del puerto TCP/1433 en el lado cliente de Puerta de enlace de administración de datos antes de conectar a una base de datos SQL.

Si la base de datos de destino está en Azure SQL Database, compruebe también la configuración del firewall de SQL Server para Azure.

Vea la sección siguiente para realizar una prueba de conexión en el almacén de datos local.

## <a name="data-store-connection-or-driver-related-errors"></a>Errores relacionados con la conexión al almacén de datos o con los controladores
Si ve errores relacionados con los controladores o la conexión al almacén de datos, complete los siguientes pasos:

1. Inicie el Administrador de configuración de la puerta de enlace de administración de datos en la máquina de la puerta de enlace.
2. Cambie a la pestaña **Diagnósticos** .
3. En **Probar conexión**, agregue los valores de grupo de puerta de enlace.
4. Haga clic en **Probar** para ver si puede conectarse al origen de datos local desde la máquina de la puerta de enlace utilizando las credenciales y la información de conexión. Si la conexión de prueba sigue sin funcionar después de instalar un controlador, reinicie la puerta de enlace para recoger los cambios más recientes.

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png" alt-text="Prueba de conexión en la pestaña Diagnósticos":::

## <a name="gateway-logs"></a>Registros de puerta de enlace
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registros de puerta de enlace a Microsoft
Cuando se ponga en contacto con el equipo de soporte técnico de Microsoft para solucionar problemas de la puerta de enlace, es posible que se le pida que comparta los registros de la puerta de enlace. Con la versión de la puerta de enlace, puede compartir registros de puerta de enlace necesarios con dos clics de botón en el Administrador de configuración de la puerta de enlace de administración de datos.    

1. Cambie a la pestaña **Diagnóstico** del Administrador de configuración de la puerta de enlace de administración de datos.

    :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png" alt-text="Pestaña Diagnóstico de la puerta de enlace de administración de datos":::
2. Haga clic en **Enviar registros** para ver el siguiente cuadro de diálogo.

    :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png" alt-text="Puerta de enlace de administración de datos: Enviar registros":::
3. (Opcional) Haga clic en **Ver registros** para revisar los registros en el Visor de eventos.
4. (Opcional) Haga clic en **Privacidad** para revisar la declaración de privacidad de servicios web de Microsoft.
5. Una vez que esté satisfecho con lo que va a cargar, haga clic en **Enviar registros** para enviar realmente los registros de los últimos siete días a Microsoft para solucionar problemas. Debería ver el estado de la operación de envío de registros tal y como aparece en la siguiente captura.

    :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png" alt-text="Captura de pantalla que muestra dónde ver el estado de la operación de envío de registros.":::
6. Una vez que finalice la operación, verá un cuadro de diálogo como el que se muestra en la siguiente captura de pantalla.

    :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png" alt-text="Puerta de enlace de administración de datos: Estado del envío de registros":::
7. Guarde el **identificador de informe** y envíelo al equipo de soporte técnico de Microsoft. El identificador de informe sirve para encontrar los registros de la puerta de enlace que ha cargado para solucionar problemas.  Este identificador del informe también se guarda en el Visor de eventos.  Para encontrarlo, examine el identificador de evento "25" y compruebe la fecha y la hora.

    :::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png" alt-text="Puerta de enlace de administración de datos: Identificador de informe del envío de registros":::    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archivar registros de puerta de enlace en el equipo host de puerta de enlace
Hay veces en las que existen problemas con la puerta de enlace y no es posible compartir los registros de puerta de enlace de forma directa:

* Cuando se instala y registra manualmente la puerta de enlace.
* Cuando se trata de registrar la puerta de enlace con una clave regenerada en el Administrador de configuración de la puerta de enlace de administración de datos.
* Cuando se trata de enviar registros, pero el servicio host de la puerta de enlace de administración de datos no se puede conectar.

Para estos escenarios, puede guardar los registros de puerta de enlace como un archivo ZIP y compartirlo cuando pueda ponerse en contacto con el equipo de soporte técnico de Microsoft. Por ejemplo, si recibe el siguiente error al registrar la puerta de enlace tal y como se muestra en la siguiente captura de pantalla.   

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png" alt-text="Puerta de enlace de administración de datos: Error de registro":::

Haga clic en el vínculo **Archivar registros de puerta de enlace** para archivar y guardar los registros y, luego, comparta el archivo ZIP con el equipo de soporte técnico de Microsoft.

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png" alt-text="Puerta de enlace de administración de datos: Archivar registros":::

### <a name="locate-gateway-logs"></a>Buscar registros de puerta de enlace
Puede obtener información detallada de los registros de la puerta de enlace en los registros de eventos de Windows.

1. Inicie el **Visor de eventos** de Windows.
2. Busque los registros en la carpeta **Registros de aplicaciones y servicios** > **Puerta de enlace de administración de datos**.

   Al solucionar problemas relacionados con la puerta de enlace, busque eventos de error en el Visor de eventos.

:::image type="content" source="media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png" alt-text="Puerta de enlace de administración de datos: Registros en el Visor de eventos":::
