---
title: Conexión de datos de Cloud App Security a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de "Microsoft Cloud App Security" (MCAS) para transmitir alertas y registros de Cloud Discovery de MCAS a Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 18dcd909ef55894e7548129a9b5836b68a30efe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780040"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar datos de Microsoft Cloud App Security 

El conector de [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) le permite transmitir alertas y [registros de Cloud Discovery](/cloud-app-security/tutorial-shadow-it) de MCAS a Azure Sentinel. Esto le permitirá adquirir visibilidad sobre las aplicaciones en la nube, obtener análisis sofisticados para identificar y combatir las ciberamenazas y controlar cómo viajan los datos.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- El usuario debe tener permisos de lectura y escritura en el área de trabajo.
- El usuario debe tener permisos de administrador global o de administrador de seguridad en el inquilino del área de trabajo.
- Para transmitir registros de Cloud Discovery a Azure Sentinel, [habilite Azure Sentinel como SIEM en Microsoft Cloud App Security](/cloud-app-security/siem-sentinel).

> [!IMPORTANT]
> La ingesta de registros de Cloud Discovery se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar a Cloud App Security

Si ya tiene Cloud App Security, asegúrese de que está [habilitado en la red](/cloud-app-security/getting-started-with-cloud-app-security).
Si Cloud App Security ya está implementado e ingiriendo datos, los datos de alerta se pueden transmitir a Azure Sentinel muy fácilmente.


1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**. En la lista de conectores, haga clic en el icono de **Microsoft Cloud App Security** y, a continuación, en el botón **Abrir página del conector** en la parte inferior derecha.

1. Seleccione qué registros quiere transmitir a Azure Sentinel. Aquí puede elegir **Alertas** y **Registros de Cloud Discovery** (versión preliminar). 

1. Haga clic en **Aplicar cambios**.

1. Puede seleccionar si quiere que las alertas de Cloud App Security generen incidentes automáticamente en Azure Sentinel. En **Create incidents - Recommended!** (Crear incidentes, recomendado), seleccione **Habilitado** para activar la regla de análisis predeterminada que crea automáticamente incidentes a partir de alertas. Luego, puede editar esta regla en **Analytics** (Análisis), en la pestaña **Active rules** (Reglas activas).

1. Si quiere usar el esquema correspondiente en Log Analytics para las alertas de Cloud App Security, escriba `SecurityAlert` en la ventana de consulta. En el esquema de registros de Cloud Discovery, escriba `McasShadowItReporting`.

> [!NOTE]
> Cloud Discovery ayuda a detectar e identificar tendencias agregando los datos subyacentes a las conexiones de usuario a las aplicaciones en la nube.
>
> Dado que los datos de Cloud Discovery se agregan por día, tenga en cuenta que hasta 24 horas de los datos más recientes no se reflejarán en Azure Sentinel. En caso de que una investigación de bajo nivel requiera datos más inmediatos, debe realizarse directamente en el dispositivo de origen o en el servicio en el que residen los datos sin procesar.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Microsoft Cloud App Security a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a detectar amenazas con Azure Sentinel mediante las reglas [integradas](./detect-threats-built-in.md) o [personalizadas](detect-threats-custom.md).