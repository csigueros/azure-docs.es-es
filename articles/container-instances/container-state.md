---
title: Estados de Azure Container Instances
description: Obtenga información sobre los estados de aprovisionamiento, los contenedores y los grupos de contenedores de Azure Container Instances.
ms.author: nickoman
author: nickomang
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 0144b6ebd82c6df4c9ec501897a4315b4554103d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018642"
---
# <a name="azure-container-instances-states"></a>Estados de Azure Container Instances

Azure Container Instances muestra varios valores de estado independientes. En este artículo, se catalogan esos valores, se muestra dónde se pueden encontrar y lo que indican.

## <a name="where-to-find-state-values"></a>Dónde buscar los valores de estado

En Azure Portal, el estado se muestra en varias ubicaciones. Todos los valores de estado son accesibles mediante la definición JSON del recurso. Este valor se puede encontrar en Información esencial en la hoja Información general, como se muestra a continuación.

:::image type="content" source="./media/container-state/provisioning-state.png" alt-text="Se muestra la hoja Información general de Azure Portal. El vínculo &quot;Vista JSON&quot; está resaltado.":::

El estado también se muestra en otras ubicaciones de Azure Portal. En la tabla siguiente, se resume dónde se pueden encontrar los valores de estado:

|Nombre|Ruta de acceso JSON|Ubicación en Azure Portal|
|-|-|-|
|Estado del grupo de contenedores|`properties.instanceView.state`|En Información esencial en la hoja Información general|
|Estado actual del contenedor|`properties.containers/initContainers[x].instanceView.currentState.state`|En la columna **Estado** de la tabla de la hoja Contenedores|
|Estado anterior del contenedor|`properties.containers/initContainers[x].instanceView.previousState.state`|Mediante la opción *Vista JSON* en Información esencial en la hoja Información general|
|Estado de aprovisionamiento|`properties.provisioningState`|Mediante la opción *Vista JSON* en Información esencial en la hoja Información general; cuerpo de la respuesta HTTP|

## <a name="container-groups"></a>Grupos de contenedores

Este valor es el estado del grupo de contenedores implementado en el back-end.

:::image type="content" source="./media/container-state/container-group-state.png" alt-text="Se muestra la hoja de información general del recurso en Azure Portal en un explorador web. El texto &quot;Estado: en ejecución&quot; está resaltado.":::

- **En ejecución**: el grupo de contenedores se está ejecutando y seguirá intentando ejecutarse hasta que se produzca una acción del usuario o una detención causada por la directiva de reinicio.

- **Detenido**: el grupo de contenedores se ha detenido y no se programará para ejecutarse sin una acción del usuario.

- **Pendiente**: el grupo de contenedores está esperando para inicializarse (terminando de ejecutar la inicialización de contenedores y montar los volúmenes de archivos de Azure si procede). El contenedor sigue intentando llegar al estado **En ejecución** a menos que se produzca una acción del usuario (detener o eliminar).

- **Correcto**: el grupo de contenedores se ha ejecutado hasta completarse correctamente. Solo se aplica a las directivas de reinicio *Nunca* y *En caso de error*.

- **Error**: el grupo de contenedores no se pudo ejecutar hasta su finalización. Solo es aplicable con una directiva de reinicio *Nunca*. Este estado indica un error de infraestructura (por ejemplo: credenciales incorrectas del recurso compartido de archivos de Azure) o un error de aplicación de usuario (por ejemplo, la aplicación hace referencia a una variable de entorno que no existe).

En la tabla siguiente, se muestran los estados aplicables a un grupo de contenedores en función de la directiva de reinicio designada:

|Valor|Nunca.|En caso de error|Siempre|
|--|--|--|--|
|En ejecución|Sí|Sí|Sí|
|Detenido|Sí|Sí|Sí|
|Pending|Sí|Sí|Sí|
|Correcto|Sí|Sí|No|
|Con error|Sí|No|No|

## <a name="containers"></a>Contenedores

Hay dos valores de estado para los contenedores: el estado actual y el estado anterior. En Azure Portal, como se muestra a continuación, solo se muestra el estado actual. Todos los valores de estado son aplicables a cualquier contenedor determinado, independientemente de la directiva de reinicio del grupo de contenedores.

> [!NOTE]
> Los valores JSON de `currentState` y `previousState` contienen información adicional, como el código de salida o el motivo, que no se muestra en ningún otro lugar de Azure Portal.

:::image type="content" source="./media/container-state/container-state.png" alt-text="Se muestra la hoja Contenedores de Azure Portal. Se muestra una tabla y se resalta &quot;En ejecución&quot; en la columna &quot;Estado&quot;.":::

- **En ejecución**: el contenedor se está ejecutando.

- **En espera**: el contenedor está esperando a ejecutarse. Este estado indica que los inicialización de los contenedores sigue en ejecución o que el contenedor está en un bucle de bloqueos.

- **Finalizado**: el contenedor ha finalizado, acompañado de un valor de código de salida.

## <a name="provisioning"></a>Aprovisionamiento

Este valor es el estado de la última operación realizada en un grupo de contenedores. Por lo general, esta operación es PUT (create), pero también puede ser POST (start/restart/stop) o DELETE (delete).

> [!IMPORTANT]
> Además, los usuarios no deben crear dependencias sobre estados de aprovisionamiento que no son terminales. Las dependencias sobre los estados **Correcto**  y **Error** son aceptables.

Además de la vista JSON, el estado de aprovisionamiento también se puede encontrar en el [cuerpo de la respuesta de la llamada HTTP](/rest/api/container-instances/containergroups/createorupdate#response).

### <a name="create-start-and-restart-operations"></a>Operaciones create, start y restart

> [!IMPORTANT]
> Las operaciones PUT (create) son asincrónicas. El valor devuelto del cuerpo de respuesta de la operación PUT no es el estado final. La manera recomendada de supervisar el estado de la implementación es realizar llamadas GET posteriores con el elemento resourceId del grupo de contenedores o la operación asincrónica (devuelto en los encabezados de la respuesta PUT).

Estos estados son aplicables a los eventos PUT (create) y POST (start/restart).

- **Pendiente**: el grupo de contenedores está a la espera de la configuración de la infraestructura, como una asignación de nodos, el aprovisionamiento de la red virtual o cualquier otra cosa necesaria antes de extraer la imagen del usuario.

- **Creando**: ha finalizado la configuración de la infraestructura. El grupo de contenedores se está preparando y recibiendo los recursos que necesita (montaje de volúmenes de archivos de Azure, obtención de la dirección IP de entrada, etc.).

- **Correcto**: el grupo de contenedores ha logrado poner sus contenedores en estado de ejecución y ha recibido todos los recursos que necesita.

- **Incorrecto**: el estado del grupo de contenedores es incorrecto. Para un estado inesperado, como cuando un nodo está fuera de servicio, se desencadena automáticamente un trabajo para mover el grupo de contenedores para su reparación.

- **En reparación**: se esta moviendo el grupo de contenedores para reparar un estado incorrecto.

- **Error**: el grupo de contenedores no pudo alcanzar el estado de aprovisionamiento **Correcto**. El error se puede producir por muchas razones (perfil de red inaccesible, poca capacidad en la región designada, consumo completo de la cuota de usuario, agotamiento del tiempo de espera después de 30 minutos, etc.). Puede encontrar más información sobre el error en el elemento `events` (Eventos) en la vista JSON.
    > [!NOTE]
    > Un estado de error no significa que el recurso se quite o deje de intentar llevarse a cabo correctamente. El estado del grupo de contenedores indica el estado actual del grupo. Si desea asegurarse de que el grupo de contenedores no se ejecute después de un estado de aprovisionamiento con **errores**, tendrá que detenerlo o eliminarlo.

### <a name="stop-and-delete-operations"></a>Operaciones stop y delete

Estos valores son aplicables a los eventos POST (stop) y DELETE (delete).

- **Correcto**: la operación para detener o eliminar el grupo de contenedores se completó correctamente.

- **Error**: el grupo de contenedores no pudo alcanzar el estado de aprovisionamiento **Correcto**, lo que significa que el evento stop o delete no se ha completado. Puede encontrar más información sobre el error en el elemento `events` (Eventos) en la vista JSON.