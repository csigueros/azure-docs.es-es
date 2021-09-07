---
title: Administración de la configuración de telemetría Microsoft Azure Backup Server (MABS)
description: En este artículo se ofrece información sobre cómo administrar la configuración de telemetría en MABS.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722644"
---
# <a name="manage-telemetry-settings"></a>Configuración de la administración de telemetría

>[!NOTE]
>Esta característica es aplicable a MABS V3 UR2 y versiones posteriores.

En este artículo se ofrece información sobre cómo administrar la configuración de telemetría (Datos de diagnóstico y uso) en Microsoft Azure Backup Server (MABS).

De forma predeterminada, MABS envía datos de diagnóstico y conectividad a Microsoft. Microsoft utiliza estos datos para proporcionar y mejorar la calidad, seguridad e integridad de los productos y servicios de Microsoft.

Los administradores pueden desactivar esta característica en cualquier momento. Para más información sobre los datos recopilados, consulte la [siguiente sección](#telemetry-data-collected).

## <a name="turn-onoff-telemetry-from-console"></a>Activación y desactivación de la telemetría desde la consola

1. En la consola de Microsoft Azure Backup Server, vaya a **Administración** y haga clic en **Opciones** en el panel superior.
1. En el cuadro de diálogo **Opciones**, seleccione **Configuración de diagnóstico y uso de datos**.

    ![opciones de telemetría de consola](./media/telemetry/telemetry-options.png)

1. Seleccione la preferencia de uso compartido de datos de diagnóstico y uso de las opciones mostradas y, a continuación, haga clic en **Aceptar**.

    >[!NOTE]
    >Se recomienda leer la [Declaración de privacidad](https://privacy.microsoft.com/privacystatement) antes de seleccionar la opción.
    >- Para activar la telemetría, seleccione **Yes I am willing to send data to Microsoft** (Sí, estoy dispuesto a enviar datos a Microsoft).
    >- Para desactivar la telemetría, seleccione **No I prefer not to send data to Microsoft** (No, prefiero no enviar datos a Microsoft).

## <a name="telemetry-data-collected"></a>Recopilación de datos de telemetría

| Datos relacionados con: | Datos recopilados* |
| --- | --- |
| **Configuración** | Versión de MABS instalada. <br/><br/>Versión del paquete acumulativo de actualizaciones de MABS instalado. <br/><br/> Identificador único de la máquina. <br/><br/> Sistema operativo donde está instalado MABS. <br/><br/> Identificador único de suscripción a la nube.<br/><br/> Versión del agente de MARS<br/><br/> Si el almacenamiento en capas está habilitado. <br/><br/> Tamaño del almacenamiento utilizado. |
| **Carga de trabajo protegida** | Identificador único de carga de trabajo. <br/><br/>Tamaño de la carga de trabajo de la que se realiza una copia de seguridad. <br/><br/>Tipo de carga de trabajo y su número de versión. <br/><br/>Si la carga de trabajo está protegida actualmente por MABS. <br/><br/>Identificador único del grupo de protección en el que está protegida la carga de trabajo.<br/><br/> Ubicación en la que se realiza la copia de seguridad de la carga de trabajo en disco, cinta o nube.|
| **Trabajos** | Estado del trabajo de copia de seguridad/restauración. <br/><br/> Tamaño de los datos de los que se ha hecho una copia de seguridad o se han restaurado. <br/><br/>Mensaje de error, si se produce un error en el trabajo de copia de seguridad/restauración.<br/><br/> Tiempo necesario para el trabajo de restauración.<br/><br/>Detalles de la carga de trabajo para la que se ejecutó el trabajo de copia de seguridad/restauración. |
| **Estado de cambio de telemetría** | El estado cambia los detalles de la configuración de telemetría, si está habilitada o deshabilitada, y en qué momento. |
| **Error de bloqueo de la consola de MABS** | Los detalles del error cuando se bloquea una consola de MABS.|

## <a name="next-steps"></a>Pasos siguientes

[Protección de cargas de trabajo](./back-up-hyper-v-virtual-machines-mabs.md)