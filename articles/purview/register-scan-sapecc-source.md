---
title: Conexión y administración de un origen de SAP ECC
description: En esta guía se describe cómo conectarse a SAP ECC en Azure Purview y cómo usar las características de Purview para examinar y administrar el origen de SAP ECC.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f1cd45b16c9569eb5069fa1cfb6a1f09eb97f3fb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023773"
---
# <a name="connect-to-and-manage-sap-ecc-in-azure-purview"></a>Conexión y administración de SAP ECC en Azure Purview

En este artículo se describe cómo registrar SAP ECC y cómo autenticarse e interactuar con SAP ECC en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-sapecc.md)|

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* El conector lee los metadatos de SAP mediante la versión 3.0 de la API del [conector de Java (JCo) para SAP](https://support.sap.com/en/product/connectors/jco.html). Asegúrese de que el conector de Java esté disponible en la máquina virtual donde está instalado el entorno de ejecución de integración autohospedado. Asegúrese de que usa la distribución de JCo correcta para su entorno. Por ejemplo, en una máquina de Microsoft Windows, asegúrese de que los archivos sapjco3.jar y sapjco3.dll estén disponibles.

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

* Implemente el módulo de función de ABAP de extracción de metadatos en el servidor SAP mediante los pasos indicados en la [guía de implementación de funciones de ABAP](abap-functions-deployment-guide.md). Necesitará una cuenta de desarrollador de ABAP para crear el módulo de función de RFC en el servidor SAP. La cuenta de usuario necesita los permisos suficientes para conectarse al servidor SAP y ejecutar los siguientes módulos de función de RFC:
  * STFC_CONNECTION (comprobación de la conectividad)
  * RFC_SYSTEM_INFO (comprobación de la información del sistema)

## <a name="register"></a>Register

En esta sección se describe cómo registrar SAP ECC en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para registro

La única autenticación admitida en un origen de SAP ECC es la **autenticación básica**.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **SAP ECC**. Seleccione **Continue** (Continuar).

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="registro de opciones de SAP ECC" border="true":::

En la pantalla **Register sources (SAP ECC)** (Registrar orígenes [SAP ECC]), haga lo siguiente:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.

1. Escriba el nombre de **Application server** (Servidor de aplicaciones) para conectarse al origen de SAP ECC. También puede ser una dirección IP del host del servidor de aplicaciones de SAP.

1. Escriba el **número del sistema** de SAP. Se trata de un entero de dos dígitos comprendido entre 00 y 99.

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="registro de SAP ECC" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar SAP ECC para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de SAP ECC registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:

        * Selecciona la autenticación básica al crear una credencial.
        * Proporciona un identificador de usuario para conectarse al servidor SAP en el campo de entrada de nombre de usuario.
        * Almacena la contraseña de usuario usada para conectarse al servidor SAP en la clave secreta.

    1. **Client ID** (Id. de cliente): escriba el identificador de cliente de SAP. Se trata de un número de tres dígitos comprendido entre 000 y 999.

    1. **Ruta de acceso de la biblioteca de JCo**: ruta de acceso del directorio donde se encuentran las bibliotecas de JCo.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina de Integration Runtime autohospedado que se va a usar en los procesos de examen. Depende del tamaño del origen de SAP ECC que se va a examinar. Se recomienda proporcionar una gran cantidad de memoria disponible, por ejemplo, 100 GB.

        :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="examen de SAPECC" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
