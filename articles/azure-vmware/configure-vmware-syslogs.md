---
title: Configuración de syslogs de VMware para Azure VMware Solution
description: Aprenda a configurar opciones de diagnóstico para recopilar syslogs de VMware para la nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 48422f6c0769953cee20c373ade8b8056ff0fee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892784"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Configuración de syslogs de VMware para Azure VMware Solution

La configuración de diagnóstico se usa para configurar la exportación de streaming de registros y métricas de la plataforma para un recurso al destino de su elección. Puede crear hasta cinco configuraciones de diagnóstico diferentes para enviar diferentes registros y métricas a destinos independientes. 

En este artículo, definirá una configuración de diagnóstico para recopilar syslogs de VMware para la nube privada de Azure VMware Solution. Almacenará el syslog en una cuenta de almacenamiento para ver los registros de vCenter y analizarlos con fines de diagnóstico. 

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de disponer de una nube privada de Azure VMware Solution con acceso a las interfaces de vCenter y NSX-T Manager. 

## <a name="configure-diagnostic-settings"></a>Configuración de valores de diagnóstico

1. En la nube privada de Azure VMware Solution, seleccione **Configuración de diagnóstico** y, luego, **Add diagnostic settings** (Agregar configuración de diagnóstico).
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Captura de pantalla que muestra dónde configurar syslogs de VMware." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Seleccione **vmwaresyslog** y **Todas las métricas** y elija una de las siguientes opciones que se presentan.

   >[!IMPORTANT]
   >La opción **Send to log analytics workspace** (Enviar al área de trabajo de Log Analytics) no está disponible actualmente.
 
   ### <a name="archive-to-storage-account"></a>Archivado en la cuenta de almacenamiento

    1. En **Diagnostic setting** (Configuración de diagnóstico), elija la cuenta de almacenamiento en la que quiere almacenar los registros y seleccione **Guardar**.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Captura de pantalla que muestra las opciones que se seleccionan para almacenar los syslogs." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

    1. Vaya a sus **Cuentas de almacenamiento**, compruebe que se ha creado la opción **Insight logs vmwarelog** y selecciónela. 
 
       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Captura de pantalla que muestra la opción Insight logs vmwarelog creada y disponible." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


    1. Examine **Insight logs vmwarelog** para buscar y descargar el archivo JSON para ver los registros.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Captura de pantalla que muestra la ruta de acceso al archivo json." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

   ### <a name="stream-to-microsoft-azure-event-hubs"></a>Transmisión a Microsoft Azure Event Hubs

    1. En **Diagnostic setting** (Configuración de diagnóstico), en Detalles del destino, seleccione **Transmitir a un centro de eventos**. 
    1. En el menú desplegable **Espacio de nombres del centro de eventos**, elija dónde quiere enviar los registros y seleccione **Guardar**.
    
       :::image type="content" source="media/diagnostic-settings/stream-event-hub.png" alt-text="Captura de pantalla en la que se muestra la ruta de acceso para enviar los registros." lightbox="media/diagnostic-settings/stream-event-hub.png"::: 




