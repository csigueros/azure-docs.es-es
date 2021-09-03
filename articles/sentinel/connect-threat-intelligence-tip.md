---
title: Conexión de la plataforma de inteligencia sobre amenazas a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar la plataforma de inteligencia sobre amenazas (TIP) o la fuente personalizada a Azure Sentinel y a enviar indicadores de amenazas.
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
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: 51650ad1a9d72611d694e5514dca1036937a2a13
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730992"
---
# <a name="connect-your-threat-intelligence-platform-to-azure-sentinel"></a>Conexión de la plataforma de inteligencia sobre amenazas a Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Vea también**: [Conexión de Azure Sentinel a las fuentes de inteligencia sobre amenazas STIX/TAXII](connect-threat-intelligence-taxii.md).

Muchas organizaciones usan soluciones de la plataforma de inteligencia sobre amenazas (TIP) para agregar fuentes de indicadores de amenazas desde diferentes orígenes, mantener los datos dentro de la plataforma y, a continuación, elegir qué indicadores de amenazas se aplicarán a diferentes soluciones de seguridad como dispositivos de red, soluciones de EDR/XDR o SIEM como Azure Sentinel. El **conector de datos de plataformas de inteligencia sobre amenazas** permite usar estas soluciones para importar indicadores de amenazas en Azure Sentinel. 

Como el conector de datos de TIP funciona con la [API tiIndicators de Microsoft Graph Security](/graph/api/resources/tiindicator) para lograrlo, puede usar el conector para enviar indicadores a Azure Sentinel y a otras soluciones de seguridad de Microsoft, como Microsoft 365 Defender, desde cualquier otra plataforma de inteligencia sobre amenazas personalizada que se pueda comunicar con dicha API.

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="Ruta de acceso de importación de inteligencia sobre amenazas":::

Obtenga más información sobre la [inteligencia sobre amenazas](understand-threat-intelligence.md) en Azure Sentinel y específicamente sobre los [productos de la plataforma de inteligencia sobre amenazas](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products) que se pueden integrar con Azure Sentinel.

## <a name="prerequisites"></a>Requisitos previos  

- Debe tener los roles de Azure AD de **Administrador global** o de **Administrador de seguridad** para poder conceder permisos al producto de TIP o a cualquier otra aplicación personalizada que use la integración directa con la API tiIndicators de Microsoft Graph Security.

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel para almacenar los indicadores de amenazas.

## <a name="instructions"></a>Instructions

Siga estos pasos para importar indicadores de amenazas a Azure Sentinel desde su TIP integrada o solución de inteligencia sobre amenazas personalizada:
1.  Obtenga un id. de la aplicación y un secreto de cliente en Azure Active Directory
2.  Escriba esta información en la solución TIP o en la aplicación personalizada
3.  Habilite el conector de datos Plataformas de inteligencia sobre amenazas en Azure Sentinel

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Suscripción para un identificador de aplicación y un secreto de cliente desde Azure Active Directory

Tanto si trabaja con una TIP o con una solución personalizada, la API tiIndicators requiere cierta información básica para poder conectarse a la fuente y enviar a ella indicadores de amenazas. Los tres fragmentos de información que necesita son:

- Id. de aplicación (cliente)
- Id. de directorio (inquilino)
- Secreto del cliente

Puede obtener esta información de su instancia de Azure Active Directory a través de un proceso llamado **Registro de aplicaciones** que incluye los tres pasos siguientes:

- Registrar una aplicación con Azure Active Directory
- Especifique los permisos que necesita la aplicación para conectarse a la API tiIndicators de Microsoft Graph y enviar indicadores de amenazas
- Obtenga el consentimiento de la organización para conceder estos permisos a esta aplicación.

#### <a name="register-an-application-with-azure-active-directory"></a>Registro de una aplicación con Azure Active Directory

1. En Azure Portal, vaya al servicio **Azure Active Directory**.
1. Seleccione **Registros de aplicaciones** en el menú y **Nuevo registro**.
1. Elija un nombre para el registro de aplicación, seleccione el botón de radio **Un solo inquilino** y **Registrar**. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="Registro de una aplicación":::

1. En la pantalla resultante, copie los valores **Id. de la aplicación (cliente)** e **Id. de directorio (inquilino)** . Estos son los dos primeros fragmentos de información que necesitará posteriormente para configurar su TIP o solución personalizada para enviar indicadores de amenazas a Azure Sentinel. El tercero, el **Secreto de cliente**, viene después.

#### <a name="specify-the-permissions-required-by-the-application"></a>Especifique los permisos que requiere la aplicación

1. Vuelva a la página principal del servicio **Azure Active Directory**.

1. Seleccione **Registros de aplicaciones** en el menú y la aplicación recién registrada.

1. Seleccione **Permisos de API** en el menú y haga clic en el botón **Agregar un permiso**.

1. En la página **Seleccionar una API**, seleccione **Microsoft Graph** API y elija de una lista de permisos de Microsoft Graph.

1. Cuando se le pregunte "¿Qué tipo de permiso necesita la aplicación web?", seleccione **Permisos de aplicación**. Este es el tipo de permisos usados por aplicaciones que se autentican con el id. de la aplicación y secretos de aplicación (claves de API).

1. Seleccione **ThreatIndicators.ReadWrite.OwnedBy** y **Agregar permisos** para agregar este permiso a la lista de permisos de la aplicación.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="Especifique los permisos":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>Obtener el consentimiento de la organización para conceder estos permisos

1. Para obtener el consentimiento, necesita un administrador global de Azure Active Directory para seleccionar el botón **Grant admin consent for your tenant** (Conceder consentimiento de administrador para el inquilino) en la página **Permisos de API** de la aplicación. Si no tiene el rol Administrador global en su cuenta, este botón no estará disponible y tendrá que pedir a un administrador global de la organización que realice este paso. 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="Otorgar consentimiento":::

1. Una vez que se haya dado el consentimiento a la aplicación, debería ver una marca de verificación verde en **Estado**.

Ahora que se ha registrado la aplicación y que se han concedido permisos, puede obtener lo último de la lista: un secreto de cliente para la aplicación.

1. Vuelva a la página principal del servicio **Azure Active Directory**.

1. Seleccione **Registros de aplicaciones** en el menú y la aplicación recién registrada.

1. Seleccione **Certificados y secretos** en el menú y haga clic en el botón **Nuevo secreto de cliente** para recibir un secreto (clave de API) para la aplicación.

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="Obtenga el secreto de cliente":::

1. Haga clic en el botón **Agregar** y **copie el secreto de cliente**.

    > [!IMPORTANT]
    > Debe copiar el **secreto de cliente** antes de salir de esta pantalla. No puede recuperar este secreto de nuevo si sale de esta página. Necesitará este valor al configurar su TIP o solución personalizada.

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Escriba esta información en la solución TIP o en la aplicación personalizada

Ahora tiene los tres fragmentos de información que necesita para configurar su TIP o solución personalizada para enviar indicadores de amenazas a Azure Sentinel.

- Id. de aplicación (cliente)
- Id. de directorio (inquilino)
- Secreto del cliente

1. Escriba estos valores en la configuración de su TIP integrada o solución personalizada cuando sea necesario.

1. Para el producto de destino, especifique **Azure Sentinel**.

1. Para la acción, especifique **alerta**.

Una vez completada esta configuración, los indicadores de amenazas se enviarán desde la TIP o la solución personalizada, a través de la **API tiIndicators de Microsoft Graph**, destinada a Azure Sentinel.

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Habilite el conector de datos Plataformas de inteligencia sobre amenazas en Azure Sentinel

El último paso del proceso de integración es habilitar el conector **Plataformas de inteligencia sobre amenazas** en Azure Sentinel. Habilitar el conector es lo que permite a Azure Sentinel recibir los indicadores de amenazas enviados desde la TIP o la solución personalizada. Estos indicadores estarán disponibles para todas las áreas de trabajo de Azure Sentinel de la organización. Siga estos pasos para habilitar el conector de datos Plataformas de inteligencia sobre amenazas para cada área de trabajo:

1. Desde Azure Portal, vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que desea importar los indicadores de amenazas enviados desde su TIP o solución personalizada.

1. Seleccione **Conectores de datos** en el menú, seleccione **Plataformas de inteligencia sobre amenazas** en la galería de conectores y haga clic en el botón **Open connector page** (Abrir página del conector).

1. Como ya ha completado el registro de aplicaciones y configurado su TIP o solución personalizada para enviar indicadores de amenazas, el único paso que queda es hacer clic en el botón **Conectar**.

En cuestión de minutos, los indicadores de amenazas deberían empezar a fluir en esta área de trabajo de Azure Sentinel. Puede encontrar los nuevos indicadores en la hoja **Inteligencia sobre amenazas**, a la que se puede acceder desde el menú de navegación de Azure Sentinel.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar su plataforma de inteligencia sobre amenazas a Azure Sentinel. Para obtener más información sobre Azure Sentinel, consulte los siguientes artículos.

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](./detect-threats-built-in.md).