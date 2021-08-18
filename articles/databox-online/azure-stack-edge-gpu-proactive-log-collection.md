---
title: Descripción de la recopilación proactiva de registros en un dispositivo de Azure Stack Edge Pro
description: Se describe cómo se realiza la recopilación proactiva de registros en un dispositivo de Azure Stack Edge Pro y cómo deshabilitarla.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: ef54d3a252baa8d84588481b2a73d1b365bb8c13
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721400"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Recopilación proactiva de registros en un dispositivo de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

La recopilación proactiva de registros reúne indicadores de estado del sistema en el dispositivo de Azure Stack Edge para ayudar a solucionar de forma eficaz cualquier problema del dispositivo. La recopilación proactiva de registros está habilitada de manera predeterminada. En este artículo se describe lo que se registra, cómo controla Microsoft los datos, y cómo deshabilitar o habilitar la recopilación proactiva de registros.

## <a name="about-proactive-log-collection"></a>Acerca de la recopilación proactiva de registros

Los equipos de ingeniería y asistencia al cliente de Microsoft usan los registros del sistema del dispositivo de Azure Stack Edge para identificar y corregir de forma eficaz las incidencias que pueden aparecer durante la operación. La recopilación proactiva de registros es un método que alerta a Microsoft de que se ha detectado una incidencia o un evento en el dispositivo de Azure Stack Edge del cliente. Consulte los [indicadores de recopilación proactiva de registros](#proactive-log-collection-indicators) para conocer los eventos de los que se realiza un seguimiento. Los registros de soporte técnico correspondientes a la incidencia se cargan automáticamente en una cuenta de Azure Storage administrada y controlada por Microsoft. Soporte técnico de Microsoft y los ingenieros de Microsoft revisan estos registros de asistencia para determinar el mejor curso de acción para resolver la incidencia con el cliente.

> [!NOTE]
> Estos registros solo se usan con fines de depuración y proporcionan soporte técnico a los clientes en caso de que surjan incidencias.


## <a name="enabling-proactive-log-collection"></a>Habilitación de la recopilación proactiva de registros

La recopilación proactiva de registros está habilitada de manera predeterminada. Puede deshabilitar la recopilación proactiva de registros cuando intente activar el dispositivo a través de la interfaz de usuario local. 

1. En la interfaz de usuario web local del dispositivo, vaya a la página **Introducción**.

2. En el icono **Activación**, seleccione **Activar**. 

    ![Página "Detalles de la nube" de la interfaz de usuario web local 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. En el panel de **activación**:

   1. Escriba la **clave de activación** que obtuvo en [Obtener la clave de activación para Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Después de la activación, la recopilación proactiva de registros está habilitada de forma predeterminada, lo que permite a Microsoft recopilar registros basados en el estado de mantenimiento del dispositivo. Estos registros se cargan en una cuenta de Azure Storage. 

      Puede deshabilitar la recopilación proactiva de registros para impedir que Microsoft recopile registros.

   1. Si desea deshabilitar la recopilación proactiva de registros en el dispositivo, seleccione **Deshabilitar**.

   1. Seleccione **Activar**.

   ![Página "Detalles de la nube" de la interfaz de usuario web local 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Indicadores de la recopilación proactiva de registros

Una vez habilitada la recopilación proactiva de registros, los registros se cargan automáticamente cuando se detecta uno de los siguientes eventos en el dispositivo:  


|Alerta/Error/Condición  |Descripción  |
|---------|---------|
|AcsUnhealthyCondition     |Los servicios compatibles con Azure son incorrectos.         |
|IOTEdgeAgentNotRunningCondition      |El Agente de IoT Edge no está en ejecución.         |
|UpdateInstallFailedEvent | No se pudo instalar la actualización.        |

 
Microsoft seguirá agregando nuevos eventos a la lista anterior. No se necesita ningún consentimiento adicional para los nuevos eventos. Consulte esta página para obtener información sobre los indicadores de la recopilación proactiva de registros más actualizados.    
 

## <a name="other-log-collection-methods"></a>Otros métodos de recopilación de registros

Además de la recopilación proactiva de registros, que recopila registros específicos correspondientes a una incidencia específica detectada, hay otras recopilaciones de registros que pueden proporcionar una explicación más detallada del estado y el comportamiento del sistema. Normalmente, estos otros registros se pueden recopilar durante una solicitud de soporte técnico, o bien puede desencadenarlos Microsoft en función de los datos de telemetría del dispositivo.

## <a name="handling-data"></a>Control de datos

Cuando se habilita la recopilación proactiva de registros, el cliente acepta que Microsoft recopile registros del dispositivo de Azure Stack Edge, tal como se describe aquí. El cliente también acepta y da su consentimiento a la carga y retención de dichos registros en una cuenta de Azure Storage administrada y controlada por Microsoft.

Microsoft solo usa los datos para solucionar problemas de estado del sistema e incidencias. Los datos no se usan para marketing, publicidad ni ningún otro propósito comercial sin el consentimiento del cliente. 

Los datos que Microsoft recopila se controlan según nuestros procedimientos de privacidad estándar. Si un cliente decide revocar su consentimiento para la recopilación proactiva de registros, los datos recopilados con consentimiento antes de la revocación no se verán afectados.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de la [Recopilación de un paquete de soporte técnico](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).