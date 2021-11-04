---
title: Conexión y administración de Looker
description: Esta guía describe cómo conectarse a Looker en Azure Purview y utilizar las funciones de Purview para explorar y administrar el origen de Looker.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 27a21f7cb9c04cdb03138540aa10aadf0f94cb57
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475547"
---
# <a name="connect-to-and-manage-looker-in-azure-purview"></a>Conexión y administración de Looker en Azure Purview

Este artículo describe cómo registrar Looker y cómo autenticar e interactuar con Looker en Azure Purview. Para obtener más información sobre Azure Purview, lea el [artículo de introducción](overview.md).

> [!IMPORTANT]
> Looker está actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-looker.md)|

> [!Important]
> La versión compatible del servidor de Looker es la versión 7.2

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla aquí](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

En esta sección se describe cómo registrar Looker en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación de registro

Se necesita una clave API3 para conectarse al servidor de Looker. La clave API3 consta de un client_id público y un client_secret privado y sigue un patrón de autenticación de OAuth2.

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar un nuevo servidor de Looker en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar.**
1. En Register sources (Registrar orígenes), seleccione **Looker**. Seleccione **Continue** (Continuar).

:::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="registro de origen de Looker" border="true":::

En la pantalla Register sources (Registrar orígenes) (Looker), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba la dirección URL de la API de Looker en el campo **Dirección URL de la API del servidor**. El puerto predeterminado para las solicitudes de API es el puerto 19999. Además, todos los puntos de conexión de la API de Looker requieren una conexión HTTPS. Por ejemplo: "https://azurepurview.cloud.looker.com"

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="origen de Looker de análisis" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar Looker e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que se haya configurado un entorno de ejecución de integración autohospedado en la máquina virtual en la que se ejecuta la instancia de Erwin Mart. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el servidor de **Looker** registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    1. El campo **Dirección URL de la API del servidor** se rellena automáticamente en función del valor especificado durante el registro.

    1. **Credencial:** al configurar las credenciales de Looker, asegúrese de hacer lo siguiente:

        * Seleccione **Autenticación básica** como método de autenticación.
        * Proporcione el id. de cliente de la clave API3 de Looker en el campo Nombre de usuario.
        * Guarde el secreto de cliente de la clave API3 de Looker en el secreto del almacén de claves.

        Para acceder al id. de cliente y al secreto de cliente, vaya a Looker -\>Administrador -\> Usuarios -\> Seleccione **Editar** en un usuario - \>Seleccione **Editar claves** -\> Use el id. de cliente y el secreto de cliente o cree uno nuevo.

        :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="obtener detalles de Looker" border="true":::

        Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    1. **Filtro del proyecto**: el ámbito del análisis que se proporciona mediante una lista de proyectos de Looker separados por puntos y comas. Esta opción se usa para seleccionar los aspectos y los paneles por su proyecto principal.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño de la instancia de Erwin Mart que se va a examinar.

        :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Desencadenar examen" border="true":::

1. Seleccione **Test Connection** (Probar conexión).

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
