---
title: Conectar y administrar Teradata
description: Esta guía describe cómo conectarse a Teradata en Azure Purview y utilizar las funciones de Purview para explorar y administrar el origen de Teradata.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 5e0d1de26a87cb4a0ac7ddf440b2ce529e02de34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441939"
---
# <a name="connect-to-and-manage-teradata-in-azure-purview"></a>Conectar y administrar Teradata en Azure Purview

Este artículo describe cómo registrar Teradata y cómo autenticar e interactuar con Teradata en Azure Purview. Para obtener más información sobre Azure Purview, lea el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-teradata.md)|

> [!Important]
> Las versiones de la base de datos de Teradata compatibles son 12.x a 16.x.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Tiene que descargar manualmente el controlador JDBC de Teradata en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado. El archivo JAR ejecutable se puede descargar desde el sitio web de [Teradata](https://downloads.teradata.com/).

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

## <a name="register"></a>Register

En esta sección se describe cómo registrar Teradata en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación de registro

La única autenticación admitida en un origen de Teradata es la **autenticación básica**. Asegúrese de tener acceso de lectura al origen de Teradata que se está examinando.

### <a name="steps-to-register"></a>Pasos para registrarse

1.  Vaya a la cuenta de Purview.
1.  Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1.  Seleccione **Registrar**.
1.  En Register sources (Registrar orígenes), seleccione **Teradata**. Seleccione **Continuar**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Registro de opciones de Teradata" border="true":::

En la pantalla **Registrar orígenes** [Registrar orígenes (Teradata)], haga lo siguiente:

1.  Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.

1.  Escriba el nombre del **host** para conectarse a un origen de Teradata. También puede ser una dirección IP o una cadena de conexión completa al servidor.

1.  Seleccione una colección o cree una nueva (opcional).

1.  Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="register Teradata" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar Teradata e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

1. En el centro de administración, seleccione **Entornos de ejecución de integración**. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de Teradata registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:
        * Selecciona la autenticación básica al crear una credencial.
        * Proporciona un nombre de usuario para conectarse al servidor de bases de datos en el campo de entrada de nombre de usuario.
        * Almacena la contraseña del servidor de bases de datos en la clave secreta.

        Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](./manage-credentials.md).

1. **Schema** (Esquema): indique el subconjunto de esquemas que se va a importar expresado como una lista separada por puntos y coma. Por ejemplo, esquema1; esquema2. Si esa lista está vacía, se importan todos los esquemas del usuario. De forma predeterminada, se ignoran todos los esquemas del sistema (por ejemplo, SysAdmin) y los objetos. Si la lista está vacía, se importan todos los esquemas disponibles.

    Los patrones de nombres de esquema aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %. Por ejemplo, A%; %B; %C%; D
     * empieza por A o
     * termina en B o
     * contiene C o
     * igual a D

    No se permite usar NOT ni caracteres especiales.

1. **Driver location** (Ubicación del controlador): especifique la ruta de acceso a la ubicación del controlador JDBC en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado. Debe ser la ruta de acceso a la ubicación válida de la carpeta JAR.

1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del origen de Teradata que se va a examinar.

    > [!Note]
    > Como regla general, especifique 2 GB de memoria por cada 1000 tablas

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="examen de configuración" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
