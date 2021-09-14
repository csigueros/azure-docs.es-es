---
title: Configuración de alertas en Azure Monitor para soluciones de SAP con Azure Portal
description: Aprenda a usar un método de explorador para configurar alertas en Azure Monitor para soluciones de SAP.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 08/30/2021
ms.openlocfilehash: c41674763a417f0060206b365a99fcc4bf680b67
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256145"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Configuración de alertas en Azure Monitor para soluciones de SAP con Azure Portal

En este artículo, se mostrarán los pasos para configurar alertas en Azure Monitor para soluciones de SAP. Mediante la interfaz basada en explorador, configuraremos alertas y notificaciones desde [Azure Portal](https://azure.microsoft.com/features/azure-portal).

## <a name="prerequisites"></a>Requisitos previos

Implemente el recurso Azure Monitor para soluciones de SAP con al menos un proveedor. Puede configurar proveedores para: 
- Aplicación de SAP (NetWeaver)
- SAP HANA
- Microsoft SQL Server
- Clúster (de Pacemaker) de alta disponibilidad

## <a name="sign-in-to-the-portal"></a>Iniciar sesión en el portal

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-alert-rule"></a>Crear una regla de alerta

1.  En Azure Portal, busque y seleccione el recurso de Azure Monitor para soluciones de SAP. Asegúrese de tener al menos un proveedor configurado para este recurso. 
2.  Vaya a los libros de su elección, por ejemplo, SAP HANA, y seleccione una instancia de HANA.

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="Captura de pantalla que muestra la ubicación del botón de alerta." lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  Seleccione en el botón **Alertas** para ver las **plantillas de alertas** disponibles.

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="Captura de pantalla que muestra la lista de plantillas de alertas." lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  Seleccione **Crear regla** para configurar una alerta de su elección.
5.  Escriba el **Umbral de alerta**, elija **Instancia del proveedor**, y elija o cree un **Grupo de acciones** para configurar las opciones de la notificación. Puede editar la información de frecuencia y gravedad según sus requisitos.

    >[!Tip]
    > Más información sobre los [grupos de acciones](../../../azure-monitor/alerts/action-groups.md). 
    
7.  Seleccione **Habilitar regla de alertas**.

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="Captura de pantalla que muestra la página de configuración de alertas." lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  Seleccione **Implementar regla de alertas** para finalizar la configuración de la regla de alertas. Para elegir ver la plantilla de alertas, haga clic en **Ver plantilla**.

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="Captura de pantalla que muestra el paso final de la configuración de alertas." lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  Vaya a **Reglas de alertas** para ver la regla de alertas recién creada. Cuando se desencadenen alertas, si ocurre, puede verlas en **Alertas desencadenadas**.

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="Captura de pantalla que muestra el resultado de la configuración de alertas." lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre Azure Monitor para soluciones de SAP.

> [!div class="nextstepaction"]
> [Supervisión de SAP en Azure](monitor-sap-on-azure.md)
