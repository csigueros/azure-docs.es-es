---
title: Conexión de datos de Proofpoint On Demand Email Security con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de Proofpoint On Demand Email Security para extraer registros de POD Email Security y exportalos a Azure Sentinel. Vea los datos de POD Email Security en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: e29af6c670146d9154f8231f47cf00a6ca37b737
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253125"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Conexión de la solución de Proofpoint On Demand Email Security (POD) con Azure Sentinel

> [!IMPORTANT]
> El conector de Proofpoint On Demand Email Security está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de Proofpoint On Demand Email Security con Azure Sentinel. El conector de datos de POD le permite conectar fácilmente los datos de registro de POD con Azure Sentinel, de modo que pueda verlos en los libros, usarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación.  La integración entre Proofpoint On Demand Email Security y Azure Sentinel usa Websocket API.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener permisos de lectura para las claves compartidas del área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Debe tener permisos de lectura y escritura para Azure Functions con el fin de crear una aplicación de funciones. [Obtenga más información sobre Azure Functions](../azure-functions/index.yml).

- Debe tener las credenciales de Websocket API siguientes: ProofpointClusterID, ProofpointToken. [Obtenga más información sobre Websocket API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Configuración y conexión de Proofpoint On Demand Email Security

Proofpoint On Demand Email Security puede integrar y exportar los registros directamente a Azure Sentinel.

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione **Proofpoint On Demand Email Security (versión preliminar)** y, a continuación, seleccione la página **Abrir conector**.

1. Siga los pasos que se describen en la sección **Configuración** de la página de conectores.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en la opción **Registros** de *Registros personalizados* de las tablas siguientes:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 60 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar Proofpoint On Demand Email Security con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.