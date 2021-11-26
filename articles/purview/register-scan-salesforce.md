---
title: Conexión y administración de Salesforce
description: En esta guía se describe cómo conectarse a Salesforce en Azure Purview y cómo usar las características de Purview para examinar y administrar el origen de Salesforce.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 81f4775771c162dce061bc1ad8901bd9b9542d40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554947"
---
# <a name="connect-to-and-manage-salesforce-in-azure-purview-preview"></a>Conexión y administración de Salesforce en Azure Purview (versión preliminar)

En este artículo se describe cómo registrar Salesforce y cómo autenticarse e interactuar con Salesforce en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

> [!IMPORTANT]
> Salesforce como origen está actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| No|

Al examinar Salesforce, Purview admite la extracción de metadatos, como organizaciones, objetos, campos, claves externas y propiedades unique_constraints de Salesforce.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Asegúrese de que la dirección IP de la máquina del entorno de ejecución de integración autohospedado se encuentra dentro de los [intervalos IP de confianza de su organización](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) establecidos en Salesforce. De lo contrario, también debe proporcionar el token de seguridad para autenticarse en Salesforce desde una red que no es de confianza. Obtenga más información en la configuración de credenciales en la sección [Examinar](#scan).

* En caso de que los usuarios envíen documentos de Salesforce, se deben configurar ciertas opciones de seguridad para permitir este acceso en objetos estándar y objetos personalizados. Para configurar permisos:
    - En Salesforce, haga clic en Instalar y, a continuación, haga clic en Administrar usuarios.
    - En el árbol Administrar usuarios, haga clic en Perfiles.
    - Una vez que aparezcan los perfiles a la derecha, seleccione el perfil que desea editar y haga clic en el vínculo Editar situado junto al perfil correspondiente.
    
    En Standard Objects (Objetos estándar), asegúrese de que la sección "Documentos" tiene los permisos de lectura seleccionados. En Custom Objects (Objetos personalizados), asegúrese de que los permisos de lectura estén seleccionados para cada objeto personalizado.

## <a name="register"></a>Register

En esta sección se describe cómo registrar Salesforce en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar un nuevo origen de Salesforce en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview en [Purview Studio](https://web.purview.azure.com/resource/).
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **Salesforce**. Seleccione **Continuar**.

En la pantalla **Register sources (Salesforce)** (Registrar orígenes [Salesforce]), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba la dirección URL del punto de conexión de inicio de sesión de Salesforce como **URL del dominio**. Por ejemplo: `https://login.salesforce.com`. Puede usar la dirección URL de instancia de su empresa (por ejemplo, `https://na30.salesforce.com`) o la dirección URL de dominio (por ejemplo, `https://myCompanyName.my.salesforce.com/`).

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-salesforce/register-sources.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que se indican a continuación para examinar Salesforce para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

Azure Purview usa la versión 41.0 de la API de REST de Salesforce para extraer metadatos, incluidas las solicitudes REST como el URI "Describe Global" (/v41.0/sobjects/), el URI "sObject Basic Information" (/v41.0/sobjects/sObject/) y el URI de "SOQL Query" (/v41.0/query?).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de Salesforce registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Conectar mediante un entorno de ejecución de integración**: seleccione el entorno de ejecución de integración configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:
        * Seleccione **Clave de consumidor** al crear una credencial.
        * Proporcione el nombre del usuario que la aplicación conectada imita en el campo de entrada Nombre de usuario.
        * Almacene la contraseña del usuario que la aplicación conectada imita en un secreto de Azure Key Vault. 
            * Si la dirección IP de la máquina del entorno de ejecución de integración autohospedado se encuentra dentro de los [intervalos IP de confianza de su organización](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) establecidos en Salesforce, proporcione solamente la contraseña del usuario.
            * De lo contrario, concatene la contraseña y el token de seguridad como el valor del secreto. El token de seguridad es una clave generada automáticamente que se debe agregar al final de la contraseña al iniciar sesión en Salesforce desde una red que no es de confianza. Obtenga más información sobre cómo [obtener o restablecer un token de seguridad](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm).
        * Proporcione la clave de consumidor de la definición de la aplicación conectada. Puede encontrarla en la página Administrar aplicaciones conectadas de la aplicación conectada o en la definición de la aplicación conectada.
        * Se almacenó el secreto de consumidor de la definición de la aplicación conectada en un secreto de Azure Key Vault. Puede encontrarlo junto con la clave de consumidor.

    1. **Objetos**: proporcione una lista de nombres de objeto para el ámbito del examen. Por ejemplo: `object1; object2`. Una lista vacía implica la recuperación de todos los objetos disponibles. Puede especificar nombres de objeto como un patrón de comodín. Por ejemplo, `topic?`, `*topic*` o `topic_?,*topic*`.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del origen de Salesforce que se va a examinar.

        > [!Note]
        > Como regla general, especifique 1 GB de memoria por cada 1000 tablas.

        :::image type="content" source="media/register-scan-salesforce/scan.png" alt-text="Examinar Salesforce" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías mostradas a continuación para obtener más información sobre Purview y los datos.

- [Conclusiones sobre los datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
