---
title: Conexión de las soluciones Agari Phishing Defense y Brand Protection a Azure Sentinel | Microsoft Docs
description: Aprenda a usar el conector de Agari Phishing Defense and Brand Protection para extraer sus registros en Azure Sentinel. Vea los datos de Agari en los libros, cree alertas y mejore la investigación.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: f356bce141ece3f92e6f482cfa4aff1a914fbb61
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253041"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Conexión de las soluciones Agari Phishing Defense y Brand Protection a Azure Sentinel

> [!IMPORTANT]
> El conector Agari Phishing Defense and Brand Protection está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

El conector Agari Phishing Defense and Brand Protection le permite conectar fácilmente las soluciones de Brand Protection and Phishing Defense con Azure Sentinel, de modo que pueda ver los datos en los libros, consultarlos para crear alertas personalizadas e incorporarlos para mejorar la investigación. Las soluciones de Agari se integran con Azure Sentinel mediante Azure Functions y la API REST.

Además, los clientes de Brand Protection and Phishing Response pueden aprovechar el uso compartido de Threat Intelligence a través de Security Graph API.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

Esto es lo que se necesita para conectar las soluciones Agari Phishing Defense y Brand Protection a Azure Sentinel:

- Permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Permisos de lectura para las claves compartidas en el área de trabajo. [Obtenga más información sobre las claves del área de trabajo](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Permisos de lectura y escritura para Azure Functions para crear una aplicación de funciones. [Obtenga más información sobre Azure Functions](../azure-functions/index.yml).

- Asegúrese de que tiene el **id. de cliente** y las **claves secretas** de Agari (son los mismos en todas las soluciones de Agari). Para obtener instrucciones, consulte [el sitio de desarrolladores de Agari](https://developers.agari.com/agari-platform/docs/quick-start).

## <a name="configure-and-connect-agari-solutions"></a>Configuración y conexión de las soluciones de Agari 

Las soluciones de Agari pueden integrar y exportar registros directamente a Azure Sentinel mediante una aplicación de funciones de Azure.

> [!NOTE]
> Este conector usa Azure Functions para conectarse a las soluciones de Agari para extraer sus registros en Azure Sentinel, lo que puede conllevar costos adicionales de ingesta de datos. Para más información al respecto, consulte la página [ Precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

1. **Recopile las credenciales de Agari API:** 

    Asegúrese de que tiene el **id. de cliente** y las **claves secretas** de Agari. Puede encontrar las instrucciones necesarias en el [sitio de desarrolladores de Agari](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **(Opcional) Habilite Security Graph API:** 

    La aplicación de funciones de Agari permite compartir la inteligencia sobre amenazas con Azure Sentinel a través de Security Graph API. Para usar esta característica, no solo es preciso habilitar el conector [Sentinel Threat Intelligence Platforms](./connect-threat-intelligence-tip.md), sino también [registrar una aplicación](/graph/auth-register-app-v2) en Azure Active Directory.

    Este proceso proporcionará tres datos que se usarán al implementar la aplicación de funciones siguiente: el **Id. de inquilino de Graph**, el **id. de cliente de Graph** y el **secreto de cliente de Graph**.

1. **Implemente el conector y la aplicación de funciones de Azure asociada:** 

    1. En el portal de Azure Sentinel, seleccione **Data connectors** (Conectores de datos). Seleccione **Agari Phishing Defense and Brand Protection (versión preliminar)** y, después, **Open connector page** (Abrir página del conector).

    1. En **Configuration** (Configuración), copie el **identificador del área de trabajo** y la **clave principal** de Azure Sentinel y péguelos aparte.

    1. Seleccione **Implementar en Azure** (es posible que deba desplazarse hacia abajo para encontrar el botón).

    1. Aparecerá la pantalla **Implementación personalizada**.

        - Escriba el **identificador de cliente** y el **secreto de cliente** (claves secretas) de Agari.

        - Especifique el **id. del área de trabajo** y la **clave del área de trabajo**  (clave principal) de Azure Sentinel que copió y pegó anteriormente.

        - Seleccione **True** (Verdadero) o **False** (Falso) en las soluciones de Agari para las que tiene suscripciones activas.

        - Si ha creado una aplicación de Azure para compartir IOC con Azure Sentinel mediante Security Graph API, seleccione **True** en **Enable Security Graph Sharing** (Habilitar uso compartido de Security Graph) y especifique los valores de **Graph tenant ID** (Id. de inquilino de Graph), **Graph client ID** (Id. de cliente de Graph) y **Graph client secret** (Secreto de cliente de Graph).

    1. Seleccione **Revisar + crear**. Cuando se haya completado la validación, haga clic en **Create** (Crear).

1. **Asigne los permisos necesarios a la aplicación de funciones:**

    El conector de Agari usa una variable de entorno para almacenar las marcas de tiempo de acceso al registro. Para que la aplicación escriba en esta variable, es preciso asignar permisos a la identidad asignada por el sistema.

    1. En Azure Portal, vaya a **Aplicación de funciones**.

    1. En la hoja **Aplicación de funciones**, seleccione una aplicación de funciones de la lista y, después, seleccione **Identidad** en la opción **Configuración** del menú de navegación de Aplicación de funciones.

    1. En la pestaña **Asignado por el sistema**, en **Estado** seleccione **Activado**. 

    1. Seleccione **Guardar** y aparecerá el botón **Asignaciones de roles de Azure**. Haga clic en él.

    1. En la pantalla **Asignaciones de roles de Azure**, seleccione **Agregar asignación de rol**. En **Ámbito**, seleccione **Suscripción**, seleccione la suscripción en la lista desplegable **Suscripción** y en **Rol**, seleccione **App Configuration Data Owner** (Propietario de datos de la configuración de la aplicación). 

    1. Seleccione **Guardar**.

## <a name="find-your-data"></a>Búsqueda de sus datos

Una vez establecida una conexión correcta, los datos aparecen en la opción **Registros** de *CustomLogs* de las tablas siguientes: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Para consultar los datos de las soluciones de Agari, escriba uno de los nombres de tabla anteriores en la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunas consultas de ejemplo útiles.

## <a name="validate-connectivity"></a>Validar conectividad

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar las soluciones Agari Phishing Defense y Brand Protection a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.