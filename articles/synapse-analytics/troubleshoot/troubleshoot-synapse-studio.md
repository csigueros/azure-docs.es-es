---
title: Solución de problemas de Synapse Studio
description: Solución de problemas de Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: fd560856ab087727d73317eaef5de01950281db9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399489"
---
# <a name="synapse-studio-troubleshooting"></a>Solución de problemas de Synapse Studio

En esta guía de solución de problemas se brindan instrucciones sobre la información que se debe proporcionar al abrir una incidencia de soporte técnico sobre problemas de conectividad de red. Con la información adecuada, posiblemente podamos resolver el problema con más rapidez.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problema de conectividad del servicio del grupo de SQL sin servidor

### <a name="symptom-1"></a>Síntoma 1

La opción "Grupo de SQL sin servidor" aparece atenuada en la lista desplegable "Conectarse a".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Síntoma 2

La ejecución de la consulta con "grupo de SQL sin servidor" muestra el mensaje de error "No se pudo establecer una conexión al servidor".

![Síntoma 2](media/troubleshooting-synapse-studio/symptom2.png)
 

### <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

> [!NOTE] 
>    Los siguientes pasos de solución de problemas son para Chromium Edge y Chrome. Puede usar otros exploradores (como FireFox) con los mismos pasos de solución de problemas, pero la ventana "Herramienta del desarrollador" puede tener un diseño diferente de las capturas de pantalla que aparecen en esta guía de solución de problemas. Si es posible, NO use un Edge clásico para la solución de problemas, ya que puede mostrar información inexacta en determinadas situaciones.

Abra el panel "Información de diagnóstico", seleccione el botón "Download Diagnostic" (Descargar diagnóstico). Conserve la información descargada para los informes de errores. En su lugar, puede copiar el "Id. de sesión" y adjuntarlo al abrir la incidencia de soporte técnico.

![diagnostic-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Para empezar a solucionar problemas, vuelva a intentar la operación que realizó en Synapse Studio.

- En el caso del síntoma 1, seleccione el botón "Actualizar" a la derecha de la lista desplegable "Use database" (Usar base de datos) en la pestaña "Script SQL" y compruebe si puede ver "grupo de SQL sin servidor".
- En el caso del síntoma 2, intente ejecutar de nuevo la consulta para ver si se ejecuta correctamente.

Si el problema persiste, presione F12 en el explorador para abrir "Developer Tools" (DevTools).

En la ventana "Developer Tools", cambie al panel "Network". Seleccione el botón "Clear" en la barra de herramientas del panel "Network" si es necesario.
Asegúrese de que la opción "Disable cache" en el panel "Network" esté activada.

Vuelva a intentar la operación que realizó en Azure Synapse Studio. Puede ver los nuevos elementos que se muestran en la lista "Network" en "Developer Tools". Anote la hora actual del sistema para indicarla en la incidencia de soporte técnico.

![panel de red 1](media/troubleshooting-synapse-studio/network-panel.png)

Busque el elemento cuya columna URL coincida con el siguiente patrón:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Donde [*A*] es el nombre del área de trabajo y "-ondemand" podría ser "-sqlod" y donde [*B*] debe ser un nombre de base de datos, como "master". Debería haber como máximo dos elementos con el mismo valor de dirección URL, pero distintos valores de método: OPTIONS y POST. Compruebe si estos dos elementos tienen "200" o "20x" en la columna de estado, donde "x" podría ser un dígito cualquiera.

Si alguno de ellos tiene un valor distinto de "20x" y:

- El estado comienza con "(failed)". Amplíe la columna "Status" o coloque el puntero sobre el texto de estado para ver el texto completo. Incluya el texto o la captura de pantalla al abrir la incidencia de soporte técnico.

    ![texto de estado](media/troubleshooting-synapse-studio/status-text.png)

    - Si ve ERR_NAME_NOT_RESOLVED y creó el área de trabajo en un plazo de 10 minutos, espere 10 minutos y vuelva a intentarlo para ver si el problema aún existe.
    - Si ve ERR_INTERNET_DISCONNECTED o ERR_NETWORK_CHANGED, puede indicar que la conexión de red de su equipo tiene problemas. Compruebe la conexión de red y vuelva a intentar la operación.
    - Si ve ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR u otros códigos de error que contengan "SSL", puede indicar que la configuración de SSL local tiene problemas o que el administrador de red ha bloqueado el acceso al servidor del grupo de SQL sin servidor. Abra una incidencia de soporte técnico y adjunte el código de error en la descripción.
    - Si ve ERR_NETWORK_ACCESS_DENIED, es posible que tenga que consultar con el administrador si su directiva de firewall local ha bloqueado el acceso a un dominio *.database.windows.net o a un puerto remoto 1443.
    - Opcionalmente, intente la misma operación inmediatamente en otra máquina o entorno de red para descartar un problema de configuración de red en el equipo.

- El estado es "40x", "50x" u otro número. Seleccione los elementos para ver los detalles. Debería ver los detalles del elemento a la derecha. Busque la sección "Response Header"; a continuación, compruebe si existe un elemento denominado "access-control-allow-origin". De ser así, compruebe si tiene uno de los siguientes valores:

    - `*` (asterisco único)
    - https://web.azuresynapse.net/ (u otro valor con el que empiece el texto de la barra de direcciones del explorador)

Si el encabezado de respuesta contiene uno de los valores anteriores, significa que ya debemos haber recopilado la información de error. Puede abrir una incidencia de soporte técnico si es necesario y, opcionalmente, adjuntar la captura de pantalla de los detalles del elemento.

Si no puede ver el encabezado o este no tiene uno de los valores enumerados anteriormente, adjunte una captura de pantalla de los detalles del elemento al abrir la incidencia.

 
![detalles de elemento](media/troubleshooting-synapse-studio/item-details.png)
 
Si los pasos anteriores no resuelven el problema, es posible que tenga que abrir una incidencia de soporte técnico. Al enviar la incidencia de soporte técnico, incluya "Id. de sesión" o la "Información de diagnóstico" descargados al principio de esta guía.

Al notificar el problema, tiene la opción de realizar una captura de pantalla de la pestaña "Console" de "Developer Tools" y adjuntarla a la incidencia de soporte técnico. Desplácese por el contenido y tome más de una captura de pantalla si es necesario para capturar todo el mensaje.

![consola de herramientas para desarrolladores](media/troubleshooting-synapse-studio/developer-tool-console.png)

Si va a adjuntar capturas de pantallas, indique la hora (o un intervalo de tiempo estimado) de cuando tomó las capturas de pantallas. Nos ayudará al examinar el problema.

Algunos exploradores admiten la visualización de marcas de tiempo en la pestaña "Console". Para Chromium Edge/Chrome, abra el cuadro de diálogo "Settings" en "Developer Tools" y active "Show timestamps" en la pestaña "Preferences".

![configuración de la consola de herramientas para desarrolladores](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![mostrar marca de tiempo](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="notebook-websocket-connection-issue"></a>Problema de conexión de WebSocket del cuaderno

### <a name="symptom"></a>Síntoma
El mensaje de error muestra: la conexión del cuaderno se ha cerrado inesperadamente. Para restablecer la conexión, vuelva a ejecutar el cuaderno. Información de diagnóstico: websocket_close_error (identificador de correlación) 

![Problema de conexión de WebSocket del cuaderno](media/troubleshooting-synapse-studio/notebook-websocket-connection-issue.png)

### <a name="root-cause"></a>Causa principal: 
La ejecución del cuaderno depende del establecimiento de una conexión de WebSocket a la siguiente dirección URL 
``` 
wss://{workspace}.dev.azuresynapse.net/jupyterApi/versions/1/sparkPools/{spark-pool}/api/kernels/{kernel-id}/channels 
``` 

+ **{workspace}** es el nombre del área de trabajo de Synapse, 
+ **{spark-pool}** es el nombre del grupo de Spark en el que se está trabajando actualmente, 
+ **{kernel-id}** es un GUID que se usa para distinguir sesiones de cuaderno. 

Al configurar la conexión de WebSocket, Synapse Studio va a incluir un token de acceso (token de portador JWT) en el encabezado Sec-WebSocket-Protocol de la solicitud de WebSocket. 

A veces, la solicitud de WebSocket podría bloquearse, o el token JWT del encabezado de solicitud podría estar censurado en el entorno de red. Esto hace que Synapse Notebook no pueda establecer la conexión con el servidor ni ejecutar el cuaderno. 

### <a name="action"></a>Acción: 

Si es posible, intente cambiar el entorno de red, por ejemplo, dentro o fuera de la red corporativa, o acceda a Synapse Notebook en otra estación de trabajo. 

+ Si puede ejecutar el cuaderno en la misma estación de trabajo pero en otro entorno de red, trabaje con el administrador de red para averiguar si se ha bloqueado la conexión de WebSocket. 

+ Si puede ejecutar el cuaderno en otra estación de trabajo pero en el mismo entorno de red, asegúrese de que no ha instalado ningún complemento del explorador que pueda bloquear la solicitud de WebSocket. 

De lo contrario, póngase en contacto con el administrador de red y asegúrese de que las solicitudes de WebSocket salientes con el siguiente patrón de dirección URL estén permitidas y su encabezado de solicitud no esté censurado: 

``` 
wss://{workspace}.dev.azuresynapse.net/{path} 
``` 
+ **{workspace}** es el nombre del área de trabajo de Synapse; 

+ **{path}** indica cualquier subruta de acceso (es decir, se incluye el carácter de barra diagonal) del URI. 

Este patrón de dirección URL es más flexible que el que se muestra en la sección "Causa principal", ya que permite agregar nuevas características dependientes de WebSocket a Synapse sin ningún problema de conectividad potencial en el futuro. 


## <a name="next-steps"></a>Pasos siguientes
Si los pasos anteriores no le ayudan a resolver el problema, [cree una incidencia de soporte técnico](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
