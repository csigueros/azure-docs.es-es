---
title: Conexión y administración de servidores de Erwin Mart
description: En esta guía se describe cómo conectarse a servidores de Erwin Mart en Azure Purview y utilizar las características de Purview para examinar y administrar el origen del servidor de Erwin Mart.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0a6aef0cdbf55772ada02bef7090ccc3165b5658
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472454"
---
# <a name="connect-to-and-manage-erwin-mart-servers-in-azure-purview-preview"></a>Conexión y administración de servidores de Erwin Mart en Azure Purview (versión preliminar)

En este artículo se describe cómo registrar servidores de Erwin Mart y cómo autenticarse e interactuar con servidores de Erwin Mart en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

> [!IMPORTANT]
> El servidor de Erwin Mart está actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-erwin.md)|

El origen de Erwin admite el examen completo para extraer los metadatos de un servidor de Erwin Mart. Los metadatos incluyen:

1. Solo modelos lógicos con entidades, atributos y dominios, o bien
1. Solo modelos físicos con tablas, columnas y tipos de datos, o bien
1. Modelos lógicos y físicos

> [!Important]
> Las versiones admitidas de Erwin Mart son de la 9.x a la 2021.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Asegúrese de ejecutar el entorno de ejecución de integración autohospedado en la máquina virtual en la que se ejecuta la instancia de Erwin Mart.

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla aquí](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

En esta sección se describe cómo registrar servidores de Erwin Mart en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

La única autenticación admitida para un origen de Erwin Mart es la **autenticación de servidor** en forma de nombre de usuario y contraseña.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview en [Purview Studio](https://web.purview.azure.com/).
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **Erwin**. Seleccione **Continue** (Continuar).
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="registrar orígenes erwin" border="true":::

En la pantalla Register sources (Erwin) (Registrar orígenes [Erwin]), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.
1. Escriba el **nombre del servidor** de Erwin Mart. Este es el nombre de host de red que se usa para conectarse al servidor de Erwin Mart. Por ejemplo, localhost.
1. Escriba el número de puerto que se usa al conectarse a Erwin Mart en el campo **Port** (Puerto). De manera predeterminada, este valor es 18170.
1. Escriba un valor para **Application name** (Nombre de la aplicación).

    >[!Note]
    > Para encontrar los detalles anteriores, vaya a al modelador de datos de Erwin. Seleccione Mart -\> Connect (Conectar) para ver los detalles relacionados con el nombre del servidor, el puerto y el nombre de la aplicación.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Buscar los detalles de Erwin" border="true":::

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Registrar origen" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos a continuación para examinar servidores de Erwin Mart para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que se haya configurado un entorno de ejecución de integración autohospedado en la máquina virtual en la que se ejecuta la instancia de Erwin Mart. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.
1. Vaya a **Sources** (Orígenes).

1. Seleccione la instancia de **Erwin** registrada.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Conectar mediante el entorno de ejecución de integración**: seleccione el entorno de ejecución de integración configurado.

    1. El **nombre del servidor, el puerto** y el **nombre de la aplicación** se rellenan automáticamente en función de los valores especificados durante el registro.

    1. **Credential** (Credenciales): seleccione las credenciales configuradas para conectarse al servidor de Erwin Mart. Al crear una credencial, asegúrese de lo siguiente:
        * Seleccione **Basic Authentication** (Autenticación básica) como método de autenticación.
        * Proporcione el nombre de usuario del servidor de Erwin Mart en el campo Nombre de usuario.
        * Guarde la contraseña de usuario para la autenticación de servidor en un secreto del almacén de claves.

        Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    1. **Use Internet Information Services (IIS)** (Usar Internet Information Services [IIS]): seleccione True o False para notificar si se debe usar Microsoft Internet Information Services (IIS) al conectarse al servidor de Erwin Mart. De manera predeterminada, este valor se establece en False.

    1. **Use Secure Sockets Layer (SSL)** (Usar Capa de sockets seguros [SSL]): seleccione True o False para notificar si se debe usar Capa de sockets seguros (SSL) al conectarse al servidor de Erwin Mart. De manera predeterminada, este valor se establece en False.

        > [!Note]
        > Este parámetro solo es aplicable a la versión 9.1 o posterior de Erwin Mart.

    1. **Browse mode** (Modo de exploración): seleccione el modo para explorar Erwin Mart. Las opciones posibles son "Libraries and Models" (Bibliotecas y modelos) o "Libraries only" (Solo bibliotecas).

    1. **Modelos**: ámbito del examen, que se proporciona mediante una lista separada por puntos y comas de cadenas de localizador de modelo de Erwin. Por ejemplo, mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño de la instancia de Erwin Mart que se va a examinar.

    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Desencadenar examen" border="true":::

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
