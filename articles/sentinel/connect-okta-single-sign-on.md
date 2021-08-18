---
title: Conexión de datos de inicio de sesión único de Okta a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar los datos de inicio de sesión único de Okta a Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: a5bf0abbbe5a3d9d580cc432abc88041ac5fc6f1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252670"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Conexión del inicio de sesión único de Okta a Azure Sentinel con Azure Functions

> [!IMPORTANT]
> El conector de datos de inicio de sesión único de Okta en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector de inicio de sesión único (SSO) de Okta permite conectar fácilmente todos los registros de la solución de seguridad de [inicio de sesión único de Okta](https://www.okta.com/products/single-sign-on/) con Azure Sentinel para ver los paneles, crear alertas personalizadas y mejorar la investigación. La integración entre el inicio de sesión único de Okta y Azure Sentinel usa Azure Functions para extraer datos de registro mediante la API REST.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="configure-and-connect-okta-single-sign-on"></a>Configuración y conexión del inicio de sesión único de Okta

Azure Functions puede integrar y extraer eventos y registros directamente del inicio de sesión único de Okta y reenviarlos a Azure Sentinel.

1. En el portal de Azure Sentinel, haga clic en **Data connectors** (Conectores de datos) y seleccione el conector **Okta Single Sign-On** (Inicio de sesión único de Okta).

1. Seleccione **Open connector page** (Abrir página del conector).

1. Siga las instrucciones que aparecen en la página **Okta Single Sign-On** (Inicio de sesión único de Okta).

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en Log Analytics bajo la tabla **Okta_CL**.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar el inicio de sesión único de Okta a Azure Sentinel con Azure Function Apps. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.