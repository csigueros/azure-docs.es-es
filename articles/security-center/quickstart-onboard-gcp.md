---
title: Conexión de la cuenta de GCP a Microsoft Defender for Cloud
description: Supervisión de los recursos de GCP desde Microsoft Defender for Cloud
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b7d5e5a418fd776197cdbc6108fc4871668292c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064817"
---
#  <a name="connect-your-gcp-accounts-to-microsoft-defender-for-cloud"></a>Conexión de cuentas de GCP a Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Las cargas de trabajo de nube abarcan normalmente varias plataformas de nube, por lo que los servicios de seguridad de la nube deben hacer lo mismo.

Microsoft Defender for Cloud protege las cargas de trabajo de Azure, Amazon Web Services (AWS) y Google Cloud Platform (GCP).

La incorporación de las cuentas de GCP a Defender for Cloud integra el centro de comandos de seguridad de GCP y Microsoft Defender for Cloud. Por lo tanto, Defender for Cloud ofrece visibilidad y protección en ambos entornos de nube para proporcionar:

- Detección de errores de configuración de seguridad
- Una sola vista que muestra recomendaciones de Defender for Cloud y resultados del centro de comandos de seguridad de GCP
- Incorporación de los recursos de GCP a los cálculos de puntuación segura de Cloud
- Integración de recomendaciones del centro de comandos de seguridad de GCP basadas en el estándar CIS en el panel de cumplimiento de normativas de Defender for Cloud

En la captura de pantalla siguiente puede ver que se muestran proyectos de GCP en el panel de información general de Defender for Cloud.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="Tres proyectos de GCP enumerados en el panel de información general de Defender for Cloud" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Requiere [Microsoft Defender para servidores](defender-for-servers-introduction.md).|
|Roles y permisos necesarios:|**propietario** o **colaborador** en la suscripción de Azure en cuestión|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)|
|||

## <a name="connect-your-gcp-account"></a>Conexión de una cuenta de GCP

Cree un conector para cada organización que desee supervisar desde Defender for Cloud.

Al conectar las cuentas de GCP a suscripciones de Azure específicas, tenga en cuenta la [jerarquía de recursos de Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) y estas directrices:

- Puede conectar las cuentas de GCP a Defender for Cloud en el nivel de la *organización*.
- Puede conectar varias organizaciones a una suscripción de Azure.
- Puede conectar varias organizaciones a varias suscripciones de Azure.
- Al conectar una organización, todos los *proyectos* de esa organización se agregan a Defender for Cloud.

Siga los pasos que se indican a continuación para crear su conector de nube de GCP. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Paso 1. Configurar el centro de comandos de seguridad GCP con el análisis del estado de seguridad

En el caso de todos los proyectos de GCP de la organización, también debe:

1. Configurar el **centro de comandos de seguridad GCP** mediante [estas instrucciones de la documentación de GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Habilitar el **análisis del estado de seguridad** mediante [estas instrucciones de la documentación de GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Compruebe que hay datos que fluyen al centro de comandos de seguridad.

Las instrucciones para conectar el entorno de GCP para la configuración de seguridad siguen las recomendaciones de Google para consumir recomendaciones de configuración de seguridad. La integración aprovecha el centro de comandos de seguridad de Google y consumirá recursos adicionales que podrían afectar a la facturación.

La primera vez que se habilita el análisis del estado de seguridad, los datos pueden tardar varias horas en estar disponibles.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Paso 2. Habilitar la API del centro de comandos de seguridad GCP

1. En la **biblioteca de API de la consola en la nube** de Google, seleccione los proyectos de la organización que desea conectar a Microsoft Defender for Cloud.
1. En la biblioteca de API, busque y seleccione la **API del centro de comandos de seguridad**.
1. En la página de la API, seleccione **HABILITAR**.

Obtenga más información sobre la [API del centro de comandos de seguridad](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Paso 3. Crear una cuenta de servicio dedicada para la integración de la configuración de seguridad

1. En la **consola de GCP**, seleccione un proyecto de la organización donde vaya a crear la cuenta de servicio necesaria. 

    > [!NOTE]
    > Cuando esta cuenta de servicio se agrega en el nivel de organización, se usará para acceder a los datos recopilados por Security Command Center de todos los demás proyectos habilitados en la organización. 

1. En el **menú de navegación**, en las opciones **IAM y administrador**, seleccione **Cuentas de servicio**.
1. Seleccione **CREACIÓN DE LA CUENTA DE SERVICIO**.
1. Especifique un nombre de cuenta y seleccione **Crear**.
1. Especifique el **rol** como **Visor del administrador de Defender for Cloud** y seleccione **Continuar**.
1. La sección sobre la **concesión a los usuarios de acceso a esta cuenta de servicio** es opcional. Seleccione **Listo**.
1. Copie el **valor Correo electrónico** de la cuenta de servicio creada y guárdelo para usarlo posteriormente.
1. En el **menú de navegación**, en las opciones **IAM y administrador**, seleccione **IAM**.
    1. Cambie al nivel de organización.
    1. Seleccione **AGREGAR**.
    1. En el campo **Nuevos miembros**, pegue el **valor Correo electrónico** que copió anteriormente.
    1. Especifique el rol como **Visor del administrador de Defender for Cloud** y, a continuación, seleccione **Guardar**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Establecimiento de los permisos de GCP pertinentes.":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Paso 4. Crear una clave privada para la cuenta de servicio dedicada
1. Cambie al nivel de proyecto.
1. En el **menú de navegación**, en las opciones **IAM y administrador**, seleccione **Cuentas de servicio**.
1. Abra la cuenta de servicio dedicada y seleccione Editar.
1. En la sección **Claves**, seleccione **AGREGAR CLAVE** y, a continuación, **Creación de una clave nueva**.
1. En la pantalla Creación de una clave privada, seleccione **JSON** y, a continuación, seleccione **CREAR**.
1. Guarde este archivo JSON para su uso posterior.


### <a name="step-5-connect-gcp-to-defender-for-cloud"></a>Paso 5. Conectar GCP a Defender for Cloud
1. En el menú de Defender for Cloud, seleccione **Conectores de nube**.
1. Seleccione Agregar cuenta de GCP.
1. En la página Incorporación, haga lo siguiente y, a continuación, seleccione **Siguiente**.
    1. Valide la suscripción elegida.
    1. En el campo **Nombre para mostrar**, escriba un nombre para mostrar para el conector.
    1. En el campo **Id. de la organización**, escriba el identificador de la organización. Si no lo sabe, consulte el artículo sobre [creación y administración de organizaciones](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. En el cuadro Archivo de **clave privada**, vaya al archivo JSON que descargó en el [paso 4. Cree una clave privada para la cuenta de servicio dedicada](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Paso 6. Confirmación

Cuando el conector se ha creado correctamente y el centro de comandos de seguridad GCP se ha configurado correctamente:

- El estándar CIS de GCP se mostrará en el panel de cumplimiento de normativas de Defender for Cloud.
- Las recomendaciones de seguridad para los recursos de GCP aparecerán en el portal de Defender for Cloud y en el panel de cumplimiento normativo de 5 a 10 minutos después de que se complete la incorporación: :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Recursos y recomendaciones de GCP en la página de recomendaciones de Defender for Cloud":::.


## <a name="monitoring-your-gcp-resources"></a>Supervisión de los recursos de GCP

Como se mostró anteriormente, en la página de recomendaciones de seguridad de Microsoft Defender for Cloud aparecen los recursos de GCP junto con los recursos de Azure y AWS, lo que ofrece una verdadera vista multinube.

Para ver todas las recomendaciones activas de los recursos por tipo de recurso, use la página de inventario de recursos de Defender for Cloud y filtre por el tipo de recurso de GCP que le interesa:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso de la página de inventario de recursos que muestra las opciones de GCP"::: 


## <a name="faq---connecting-gcp-accounts-to-microsoft-defender-for-cloud"></a>Preguntas más frecuentes: Conexión de cuentas de GCP a Microsoft Defender for Cloud

### <a name="can-i-connect-multiple-gcp-organizations-to-defender-for-cloud"></a>¿Puedo conectar varias organizaciones de GCP a Defender for Cloud?
Sí. El conector GCP de Defender for Cloud conecta los recursos de Google Cloud al nivel de *organización*. 

Cree un conector para cada organización de GCP que desee supervisar desde Defender for Cloud. Al conectar una organización, todos los proyectos de esa organización se agregan a Defender for Cloud.

Aprenda sobre la jerarquía de recursos de Google Cloud en los [documentos en línea de Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-defender-for-cloud"></a>¿Hay una API para conectar mis recursos de GCP a Defender for Cloud?
Sí. Para crear, editar o eliminar conectores de nube de Defender for Cloud con una API REST, consulte los detalles de la [API de conectores](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Pasos siguientes

La conexión de la cuenta de GCP forma parte de la experiencia multinube disponible en Microsoft Defender for Cloud. Para información relacionada, consulte la página siguiente:

- [Conexión de cuentas de AWS a Microsoft Defender for Cloud](quickstart-onboard-aws.md)
- [Jerarquía de recursos de Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy): aprenda sobre la jerarquía de recursos de Google Cloud en los documentos en línea de Google.
