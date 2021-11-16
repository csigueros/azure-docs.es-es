---
title: Conexión y administración de un origen de SAP S/4HANA
description: En esta guía se describe cómo conectarse a SAP S/4HANA en Azure Purview y cómo usar las características de Purview para examinar y administrar el origen de SAP S/4HANA.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: abc98ed4df5da533dc7f6a8483a917150babf0ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488038"
---
# <a name="connect-to-and-manage-sap-s4hana-in-azure-purview"></a>Conexión y administración de SAP S/4HANA en Azure Purview

En este artículo se describe cómo registrar SAP S/4HANA y cómo autenticarse e interactuar con SAP S/4HANA en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí**](how-to-lineage-sapecc.md)|

\** Se admite el linaje si el conjunto de datos se usa como origen o receptor en la [actividad de copia de Data Factory](how-to-link-azure-data-factory.md) 

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

    >[!NOTE]
    >El examen de SAP S/4HANA es una operación que consume mucha memoria, por lo que se recomienda instalar IR autohospedado en una máquina con memoria grande, por ejemplo, 128 GB.

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Descargue el [conector de SAP para Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) de 64 bits del sitio web de SAP e instálelo en la máquina del entorno de ejecución de integración autohospedado. Al instalarlo, asegúrese de seleccionar la opción **Install Assemblies to GAC** (Instalar ensamblados en GAC) en la ventana **Optional setup steps** (Pasos de configuración opcionales).

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="Requisito previo" border="true":::

* El conector lee los metadatos de SAP mediante la versión 3.0 de la API del [conector de Java (JCo) para SAP](https://support.sap.com/en/product/connectors/jco.html). Asegúrese de que el conector de Java esté disponible en la máquina virtual donde está instalado el entorno de ejecución de integración autohospedado. Asegúrese de que usa la distribución de JCo correcta para su entorno. Por ejemplo, en una máquina de Microsoft Windows, asegúrese de que los archivos sapjco3.jar y sapjco3.dll estén disponibles.

    > [!Note]
    >Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

* Implemente el módulo de función de ABAP de extracción de metadatos en el servidor SAP mediante los pasos indicados en la [guía de implementación de funciones de ABAP](abap-functions-deployment-guide.md). Necesitará una cuenta de desarrollador de ABAP para crear el módulo de función de RFC en el servidor SAP. La cuenta de usuario necesita los permisos suficientes para conectarse al servidor SAP y ejecutar los siguientes módulos de función de RFC:
  * STFC_CONNECTION (comprobación de la conectividad)
  * RFC_SYSTEM_INFO (comprobación de la información del sistema)

## <a name="register"></a>Register

En esta sección se describe cómo registrar SAP S/4HANA en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para el registro

La única autenticación admitida en un origen de SAP S/4HANA es la **autenticación básica**.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **SAP S/4HANA**. Seleccione **Continuar**

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="Registro de opciones de SAP S/4Hana" border="true":::

En la pantalla **Register sources (SAP S/4HANA)** (Registrar orígenes (SAP S/4HANA)), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba el nombre del **Application server** (Servidor de aplicaciones) para conectarse al origen de SAP S/4HANA. También puede ser una dirección IP del host del servidor de aplicaciones de SAP.

1. Escriba el **número del sistema** de SAP. Se trata de un entero de dos dígitos comprendido entre 00 y 99.

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="Registro de SAP S/4HANA" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar SAP S/4HANA para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de SAP S/4HANA registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:

        * Selecciona la autenticación básica al crear una credencial.
        * Proporciona un identificador de usuario para conectarse al servidor SAP en el campo de entrada de nombre de usuario.
        * Almacena la contraseña de usuario usada para conectarse al servidor SAP en la clave secreta.

    1. **Client ID** (Id. de cliente): escriba aquí el identificador de cliente del sistema SAP. El cliente se identifica con un número de tres dígitos comprendido entre 000 y 999.

    1. **JCo library path** (Ruta de acceso de la biblioteca de JCo): especifique la ruta de acceso a la carpeta donde se encuentran las bibliotecas de JCo.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del origen de SAP S/4HANA que se va a examinar. Se recomienda proporcionar una gran cantidad de memoria disponible, por ejemplo, 100 GB.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="Examen de SAP S/4HANA" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías mostradas a continuación para obtener más información sobre Purview y los datos.

- [Conclusiones sobre los datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
