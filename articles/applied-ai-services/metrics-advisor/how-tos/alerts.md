---
title: Configuración de alertas de Metrics Advisor
titleSuffix: Azure Cognitive Services
description: En este artículo se explica cómo configurar las alertas de Metrics Advisor mediante enlaces de correo electrónico, webhooks y enlaces de Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: f63ea23cce0ab43dd5c9e0864619ff32d5dae50d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342209"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Procedimiento: Configuración de alertas y obtención de notificaciones mediante enlaces

Una vez que Metrics Advisor detecta una anomalía, se desencadena, mediante un enlace, una notificación de alerta basada en la configuración de alertas. Un valor de alerta se puede usar con varias configuraciones de detección; hay varios parámetros disponibles para personalizar la regla de alerta.

## <a name="create-a-hook"></a>Creación de un enlace

Metrics Advisor admite cuatro tipos diferentes de enlaces: correo electrónico, Teams, webhook y Azure DevOps. Puede elegir el que mejor se adapte a su caso concreto.      

### <a name="email-hook"></a>Enlace de correo electrónico

> [!Note]
> Los administradores de recursos de Metrics Advisor deben configurar los valores de configuración del correo electrónico y especificar la **información relacionada con SMTP** en Metrics Advisor antes de poder enviar alertas de anomalías. El administrador del grupo de recursos o el administrador de la suscripción debe asignar al menos un rol de *administrador de Cognitive Services Metrics Advisor* en la pestaña control de acceso del recurso de Metrics Advisor. [Más información acerca de la configuración del correo electrónico](../faq.yml#how-to-set-up-email-settings-and-enable-alerting-by-email-). 


Un enlace de correo electrónico es el canal por el que se envían las alertas de anomalías a las direcciones de correo electrónico especificadas en la sección **Email to** (Enviar correo electrónico a). Se enviarán dos tipos de correos electrónicos de alerta: **Las alertas de fuentes de distribución de datos no disponibles** y los **informes de incidentes** que contienen una o varias anomalías. 

Para crear un enlace de correo electrónico, están disponibles los siguientes parámetros. 

|Parámetro |Descripción  |
|---------|---------|
| Nombre | Nombre del enlace de correo electrónico |
| Email to (Enviar correo electrónico a)| Direcciones de correo electrónico a las que enviar las alertas|
| Vínculo externo | Campo opcional que habilita una redirección personalizada; por ejemplo, para las notas de solución de problemas. |
| Título personalizado de la alerta de anomalía | La plantilla de título admite `${severity}`, `${alertSettingName}`, `${datafeedName}`, `${metricName}`, `${detectConfigName}`, `${timestamp}`, `${topDimension}`, `${incidentCount}` y `${anomalyCount}`.

Después de seleccionar **OK** (Aceptar), se creará un enlace de correo electrónico. Puede usarlo en cualquier configuración de alertas para recibir alertas de anomalías. Consulte el tutorial de [Habilitación de la notificación de anomalías en Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp) para obtener pasos detallados.

### <a name="teams-hook"></a>Enlace de Teams

Un enlace de Teams es el canal para que las alertas de anomalías se envíen a un canal en Microsoft Teams. Un enlace de Teams se implementa a través de un conector "Webhook entrante". Es posible que tenga que crear un conector "Webhook entrante" por adelantado en el canal de Teams de destino y obtener una dirección URL de él. A continuación, vuelva al área de trabajo de Metrics Advisor. 

Seleccione la pestaña "Enlaces" en la barra de navegación de la izquierda y después el botón "Crear enlace" en la parte superior derecha de la página. Elija el tipo de enlace "Teams", para el que se proporcionan los siguientes parámetros: 

|Parámetro |Descripción  |
|---------|---------|
| Nombre | Nombre del enlace de Teams | 
| URL del conector | Dirección URL que acaba de copiar del conector "Webhook entrante" que creó en el canal de Teams de destino. |

Después de seleccionar **OK** (Aceptar), se creará un enlace de Teams. Puede usarlo en cualquier configuración de alerta para notificar las alertas de anomalías al canal de Teams de destino. Consulte el tutorial de [Habilitación de la notificación de anomalías en Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp) para obtener pasos detallados.

### <a name="web-hook"></a>Webhook

Un webhook es otro canal de notificación mediante un punto de conexión que proporciona el cliente. Cualquier anomalía detectada en la serie temporal se notificará a través de un webhook. Hay varios pasos para habilitar un webhook como canal de notificación de alertas en Metrics Advisor. 

**Paso1.**  Habilitación de la identidad administrada en el recurso de Metrics Advisor

Cada recurso solo puede tener una identidad administrada asignada por el sistema, y cada una de ellas está asociada al ciclo de vida del recurso. Puede conceder permisos a la identidad administrada mediante el control de acceso basado en roles de Azure (Azure RBAC). La identidad administrada se autentica con Azure AD, por lo que no tiene que almacenar ninguna credencial en el código. 

Vaya al recurso de Metrics Advisor en Azure Portal y seleccione "Identity" (Identidad), actívelo y la identidad administrada estará habilitada. 

**Paso 2.** Creación de un webhook en un área de trabajo de Metrics Advisor

Inicie sesión en el área de trabajo y seleccione la pestaña "Hooks" (Enlaces) y, a continuación, seleccione el botón "Create hook" (Crear enlace). 


Para crear un webhook, tendrá que agregar la siguiente información:

|Parámetro |Descripción  |
|---------|---------|
|Punto de conexión     | La dirección de la API a la que se llamará cuando se desencadene una alerta. **DEBE ser HTTPS**.       |
|Nombre de usuario y contraseña | Para autenticarse en la dirección de la API. Déjelo en blanco si no se necesita autenticación.         |
|Encabezado     | Los encabezados personalizados de la llamada API.        |
|Identificador de certificado en los almacenes de claves de Azure| Si el acceso al punto de conexión debe autenticarse mediante un certificado, este debe estar almacenado en almacenes de claves de Azure. Especifique el identificador aquí.

> [!Note]
> Cuando se crea o modifica un webhook, se llama al punto de conexión como una prueba con **un cuerpo de solicitud vacío**. La API debe devolver un código HTTP 200 para pasar correctamente la validación.

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Ventana de creación de webhooks":::

- El método de solicitud es **POST**
- Tiempo de espera de 30 segundos
- Vuelva a intentarlo para el error 5xx, ignore otros errores. No seguirá la solicitud de redirección 301/302.
- Cuerpo de la solicitud: 
```
{
"value": [{
    "hookId": "b0f27e91-28cf-4aa2-aa66-ac0275df14dd",
    "alertType": "Anomaly",
    "alertInfo": {
        "anomalyAlertingConfigurationId": "1bc6052e-9a2a-430b-9cbd-80cd07a78c64",
        "alertId": "172536dbc00",
        "timestamp": "2020-05-27T00:00:00Z",
        "createdTime": "2020-05-29T10:04:45.590Z",
        "modifiedTime": "2020-05-29T10:04:45.590Z"
    },
    "callBackUrl": "https://kensho2-api.azurewebsites.net/alert/anomaly/configurations/1bc6052e-9a2a-430b-9cbd-80cd07a78c64/alerts/172536dbc00/incidents"
}]
}
```

**Paso 3 (opcional)** : almacene el certificado en un almacén de claves de Azure y obtenga el identificador. Como se indicó, si el acceso al punto de conexión debe autenticarse mediante un certificado, este debe estar almacenado en un almacén de claves de Azure. 

- Consulte, [Establecimiento y recuperación de un certificado de Azure Key Vault mediante Azure Portal](../../../key-vault/certificates/quick-create-portal.md).
- Haga clic en el certificado que ha agregado y, a continuación, podrá copiar el "identificador de certificado". 
- A continuación, seleccione "Directivas de acceso" y "Agregar directiva de acceso", conceda el permiso "get" para "Permisos de clave", "Permisos de secreto" y "Permisos de certificado". Seleccione entidad de seguridad como nombre del recurso de Metrics Advisor. Seleccione el botón "Agregar" y "Guardar" en la página "Directivas de acceso". 

**Paso 4.** Recepción de una notificación de anomalías. Cuando se inserta una notificación a través de un webhook, puede capturar datos de incidentes llamando a "callBackUrl" en la solicitud de webhook. Detalles de esta API:

-   [/alert/anomaly/configurations/{configurationId}/alerts/{alertId}/incidents](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/operations/getIncidentsFromAlertByAnomalyAlertingConfiguration)

Mediante un webhook y Azure Logic Apps, se puede enviar una notificación por correo electrónico **sin necesidad de configurar un servidor SMTP**. Consulte el tutorial de [Habilitación de la notificación de anomalías en Metrics Advisor](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp) para obtener pasos detallados.

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor también permite crear automáticamente un elemento de trabajo en Azure DevOps para realizar un seguimiento de los problemas o errores cuando se detecta alguna anomalía. Todas las alertas se pueden enviar a través de enlaces de Azure DevOps.

Para crear un enlace de Azure DevOps, deberá agregar la siguiente información:

|Parámetro |Descripción  |
|---------|---------|
| Nombre | Un nombre para el enlace. |
| Organización | La organización a la que pertenece su instancia de Azure DevOps. |
| Project | El proyecto específico en Azure DevOps. |
| Token de acceso |  Un token para autenticarse en DevOps. | 

> [!Note]
> Debe conceder permisos de escritura si desea que Metrics Advisor cree elementos de trabajo basados en alertas de anomalías. Después de crear enlaces, puede usarlos en cualquiera de las configuraciones de alertas. Administre los enlaces en la página **hook settings** (configuración de enlaces). 

## <a name="add-or-edit-alert-settings"></a>Adición o edición de la configuración de alertas

Vaya a la página de detalles de métricas para buscar la sección **Alert settings** (Configuración de alertas), que se encuentra en la esquina inferior izquierda de la página de detalles de métricas. Se muestran todos los valores de configuración de alertas que se aplican a la configuración de detección seleccionada. Cuando se crea una configuración de detección, no hay ningún valor de alerta y, por tanto, no se envía ninguna alerta.  
Puede usar los iconos de **add** (agregar), **edit** (editar) y **delete** (eliminar) para modificar la configuración de alertas.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Elemento de menú configuración de alertas":::

Seleccione los botones **add** (agregar) o **edit** (editar) para que aparezca una ventana para agregar o editar la configuración de alertas.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Adición o edición de la configuración de alertas":::

**Nombre de la configuración de alerta**: el nombre de la configuración de alerta. Se mostrará en el título del correo electrónico de alerta.

**Hooks** (Enlaces): La lista de enlaces a los que se van a enviar alertas.

La sección marcada en la captura de pantalla anterior son los valores de una configuración de detección. Puede establecer diferentes valores de configuración de alertas para diferentes configuraciones de detección. Elija la configuración de destino en la lista desplegable de esta ventana. 

### <a name="filter-settings"></a>Configuración de filtros 

A continuación se muestran los valores de configuración de filtro de una configuración de detección.

**Alert For** (Alerta para) tiene cuatro opciones para filtrar anomalías.

* **Anomalies in all series** (Anomalías en todas las series): Todas las anomalías se incluirán en la alerta.         
* **Anomalies in the series group** (Anomalías en el grupo de series): Se filtran las series por valores de dimensión. Establezca valores específicos para algunas dimensiones. Las anomalías solo se incluirán en la alerta cuando la serie coincida con el valor especificado.       
* **Anomalies in favorite series** (Anamolías en series favoritas): En la alerta solo se incluirán las series marcadas como favoritas.        |
* **Anomalías en las N series principales de todas las series**: este filtro se usa en caso de que solo le interese la serie cuyo valor se encuentra en las N principales. Metrics Advisor volverá a repasar las marcas de tiempo anteriores y comprobará si el valor de las series de estas marcas de tiempo se encuentra entre las N principales. Si el recuento correspondiente a "en las N principales" es mayor que el número especificado, la anomalía se incluirá en una alerta.        |

**Las opciones de anomalías de filtro son un filtro adicional con las siguientes opciones**:

- **Gravedad**: la anomalía solo se incluirá cuando la gravedad de la anomalía esté dentro del intervalo especificado.
- **Posponer**: detenga temporalmente las alertas de anomalías en los N siguientes puntos (período) cuando se desencadenen en una alerta.
    - **Tipo de aplazamiento**: cuando se establece en **Series**, una anomalía desencadenada solo pospondrá su serie. En el caso de **Metric** (Métrica), una anomalía desencadenada pospondrá todas las series de esta métrica.
    - **snooze number** (posponer número): el número de puntos (período) que se van a posponer.
    - **reset for non-successive** (restablecer para las no sucesivas): cuando se selecciona, una anomalía desencadenada solo pospondrá las siguientes N anomalías sucesivas. Si uno de los siguientes puntos de datos no es una anomalía, el aplazamiento se restablecerá desde ese punto; si no se selecciona, una anomalía desencadenada pospondrá los próximos N puntos (período), incluso si los puntos de datos sucesivos no son anomalías.
- **valor** (opcional): filtra por valor. La anomalía se incluirá solo si los valores de puntos cumplen la condición. Si usa el valor correspondiente de otra métrica, los nombres de dimensión de las dos métricas deberían ser coherentes.

Las anomalías no filtradas se enviarán en una alerta.

### <a name="add-cross-metric-settings"></a>Adición de valores de configuración entre métricas

Seleccione **+ Add cross-metric settings** (+ Agregar valores de configuración entre métricas) en la página de valores de configuración de alertas para agregar otra sección.

El selector **Operator** (Operador) es la relación lógica de cada sección, para determinar si envían una alerta.


|Operador  |Descripción  |
|---------|---------|
|y     | Solo se envía una alerta si una serie coincide con cada sección de alerta y todos los puntos de datos son anomalías. Si las métricas tienen diferentes nombres de dimensión, nunca se desencadenará una alerta.         |
|O BIEN     | Envíe la alerta si al menos una sección contiene anomalías.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operador para varias secciones de configuración de alertas":::

## <a name="next-steps"></a>Pasos siguientes

- [Ajuste de la detección de anomalías mediante comentarios](anomaly-feedback.md)
- [Diagnóstico de incidentes](diagnose-an-incident.md)
- [Configuración de métricas y ajuste de la configuración de la detección](configure-metrics.md)
