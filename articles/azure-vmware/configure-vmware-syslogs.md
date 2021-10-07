---
title: Configuración de syslogs de VMware para Azure VMware Solution
description: Aprenda a configurar opciones de diagnóstico para recopilar syslogs de VMware para la nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699550"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Configuración de syslogs de VMware para Azure VMware Solution

La configuración de diagnóstico se usa para configurar la exportación de streaming de registros y métricas de la plataforma para un recurso al destino de su elección. Puede crear hasta cinco configuraciones de diagnóstico diferentes para enviar diferentes registros y métricas a destinos independientes. 

En este tema, definirá una configuración de diagnóstico para recopilar syslogs de VMware para la nube privada de Azure VMware Solution. Almacenará el syslog en una cuenta de almacenamiento para ver los registros de vCenter y analizarlos con fines de diagnóstico. 

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de disponer de una nube privada de Azure VMware Solution con acceso a las interfaces de vCenter y NSX-T Manager. 

## <a name="configure-diagnostic-settings"></a>Configuración de valores de diagnóstico

1. En la nube privada de Azure VMware Solution, seleccione **Configuración de diagnóstico** y, a continuación, **Agregar configuración de diagnóstico**.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Captura de pantalla que muestra dónde configurar syslogs de VMware." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Seleccione las opciones **vmwaresyslog**, **Allmetrics** y **Archivar en una cuenta de almacenamiento**.

   >[!IMPORTANT]
   >La opción **Enviar al área de trabajo de Log Analytics** no funciona actualmente.
 
1. Seleccione la cuenta de almacenamiento donde desea almacenar los registros y, a continuación, seleccione **Guardar**.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Captura de pantalla que muestra las opciones que se seleccionan para almacenar los syslogs." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. Vaya a su almacenamiento y cuenta y compruebe que se ha creado la opción **Insight logs vmwarelog** y selecciónela. 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Captura de pantalla que muestra la opción Insight logs vmwarelog creada y disponible." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. Vaya a la ubicación y descargue el archivo json para ver los registros.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Captura de pantalla que muestra la ruta de acceso al archivo json." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

