---
title: Conexión de datos de Google Workspace (G Suite) con Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de datos de Google Workspace (G Suite) para ingerir eventos de actividad de Google Workspace en Azure Sentinel. Vea los datos de Google Workspace en los libros, cree alertas y mejore la investigación.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 2ed49208a79436d3dd1bb2679129b0eafab39cca
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253523"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Conexión de Google Workspace con Azure Sentinel

> [!IMPORTANT]
> El conector Google Workspace está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de datos de [Google Workspace (anteriormente, G Suite)](https://workspace.google.com/) proporciona la capacidad de ingerir eventos de la actividad de Google Workspace en Azure Sentinel a través de la API REST. El conector proporciona visibilidad sobre estos [eventos](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) en el centro de operaciones de seguridad, lo que le ayuda a examinar los posibles riesgos de seguridad, analizar la colaboración del equipo, diagnosticar problemas de configuración, realizar un seguimiento de quién inicia sesión y cuándo, analizar la actividad del administrador, comprender cómo los usuarios crean y comparten contenido, y revisar más eventos en la organización.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

Para recopilar datos de Google Workspace, debe tener los siguientes permisos y credenciales:

- Permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Permisos de lectura para las claves compartidas en el área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Permisos de lectura y escritura para Azure Functions a fin de crear una aplicación de funciones. [Obtenga más información sobre Azure Functions](../azure-functions/index.yml).

- La credencial **GooglePickleString** es necesaria para la API REST. [Obtenga más información sobre la API REST de Google](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Obtenga más información sobre cómo obtener credenciales](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Configuración y conexión de Google Workspace

Las soluciones de Google Workspace pueden integrar y exportar registros directamente a Azure Sentinel mediante una aplicación de funciones de Azure.

> [!NOTE]
> Este conector usa Azure Functions para conectarse con la API de informes de Google para extraer eventos de actividad en Azure Sentinel. lo que puede conllevar costos adicionales de ingesta de datos. Para más información al respecto, consulte la página [ Precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

1. En el portal de Azure Sentinel, haga clic en **Conectores de datos**. 

1. Seleccione **Google Workspace (G Suite) (versión preliminar)** en la galería de conectores y seleccione **Open connector page** (abrir página de conector).

1. Siga los pasos que se describen en la sección **Configuración** de la página de conectores.

## <a name="validate-connectivity-and-find-your-data"></a>Validación de la conectividad y búsqueda de los datos

Los datos ingeridos pueden tardar hasta 20 minutos en aparecer en Azure Sentinel.

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, en la sección **Registros personalizados**, de las tablas siguientes:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. [Siga estos pasos](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) para crear el alias de función de Kusto **GWorkspaceActivityReports**.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Google Workspace con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.