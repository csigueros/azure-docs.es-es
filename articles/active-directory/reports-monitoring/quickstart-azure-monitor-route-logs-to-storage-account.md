---
title: 'Tutorial: Archivo de registros de Azure Active Directory en una cuenta de Storage | Microsoft Docs'
description: Aprenda a configurar Azure Diagnostics para insertar los registros de Azure Active Directory en una cuenta de almacenamiento.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a50ef779dd65696ae62b6b08b04e65ca19291944
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233419"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Tutorial: Archivo de los registros de Azure AD en una cuenta de Azure Storage

En este tutorial, aprenderá a configurar los valores de diagnóstico de Azure Monitor para enrutar los registros de Azure Active Directory en una cuenta de Azure Storage.

## <a name="prerequisites"></a>Requisitos previos 

Para usar esta característica, necesita:

* Una suscripción de Azure con una cuenta de Azure Storage. Si no tiene ninguna suscripción de Azure, puede [registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/free/).
* Un inquilino de Azure AD.
* Un usuario que sea *administrador global* o *administrador de seguridad* para el inquilino de Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archivado de registros en una cuenta de Azure Storage

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 

2. Seleccione **Azure Active Directory** > **Supervisión** > **Registros de auditoría**. 

3. Seleccione **Exportar configuración de datos**. 

4. En el panel **Configuración de diagnóstico**, realice una de las siguientes acciones:
    1. Para cambiar la configuración existente, seleccione **Editar configuración** junto a la configuración de diagnóstico que quiere actualizar.
    1. Para agregar la nueva configuración, seleccione **Agregar configuración de diagnóstico**.  

    Puede tener un máximo de tres configuraciones.

     ![Exportar configuración](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Una vez en el panel **Configuración de diagnóstico**, si va a crear una nueva configuración, escriba un nombre para la configuración para recordar su propósito (por ejemplo, *Enviar a la cuenta de almacenamiento de Azure*). No se puede cambiar el nombre de una configuración existente.

6. En **Detalles del destino**, seleccione la casilla **Archivar en una cuenta de almacenamiento**. 

7. Seleccione la suscripción de Azure en el menú desplegable **Suscripción**, y la cuenta de almacenamiento en el menú desplegable **Cuenta de almacenamiento** a la que quiere enrutar los registros.

8. Seleccione todas las categorías pertinentes en **Detalles de la categoría**:

    Realice alguna de las siguientes acciones o ambas:
    1. Seleccione la casilla **AuditLogs** para enviar los registros de auditoría a la cuenta de almacenamiento.
    
    1. Seleccione la casilla **SignInLogs** para enviar los registros de inicio de sesión a la cuenta de almacenamiento.

    ![Configuración de diagnóstico](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. Una vez seleccionadas las categorías, en el campo **Días de retención**, escriba el número de días de retención que necesita para los datos de registro. De forma predeterminada, este valor es *0*, lo que significa que los registros se conservarán en la cuenta de almacenamiento de forma indefinida. Si establece un valor diferente, los eventos que tengan más días de los seleccionados se eliminarán de forma automática.
 
10. Seleccione **Guardar** para guardar la configuración.

11. Cierre la ventana para volver al panel Configuración de diagnóstico.

## <a name="next-steps"></a>Pasos siguientes

* [Interpretación del esquema de registros de auditoría en Azure Monitor](./overview-reports.md)
* [Interpretación del esquema de registros de inicio de sesión en Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Preguntas frecuentes y problemas conocidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
