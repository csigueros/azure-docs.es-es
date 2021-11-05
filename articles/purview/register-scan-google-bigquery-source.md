---
title: Conexión y administración de proyectos de Google BigQuery
description: En esta guía se describe cómo conectarse a proyectos de Google BigQuery en Azure Purview y usar las características de Purview para examinar y administrar el origen de Google BigQuery.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e264aaef00eb0e1af12db3a7a2f3047de5e6e3e5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056231"
---
# <a name="connect-to-and-manage-google-bigquery-projects-in-azure-purview"></a>Conexión y administración de proyectos de Google BigQuery en Azure Purview

En este artículo se describe cómo registrar proyectos de Google BigQuery y cómo autenticarse e interactuar con Google BigQuery en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

> [!IMPORTANT]
> Google BigQuery como origen está actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-google-bigquery.md)|

> [!Important]
> La versión compatible de Google BigQuery es la 11.0.0.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Descargue e instale el controlador JDBC de BigQuery en la máquina en la que se ejecuta el entorno de ejecución de integración autohospedado. Puede encontrar el controlador [aquí](https://cloud.google.com/bigquery/providers/simba-drivers).

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

## <a name="register"></a>Register

En esta sección se describe cómo registrar un proyecto de Google BigQuery en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar.**
1. En Register sources (Registrar orígenes), seleccione **Google BigQuery**. Seleccione **Continue** (Continuar).

    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="registro del origen de BigQuery" border="true":::

En la pantalla Register sources (Registrar orígenes) (Google BigQuery), siga estos pasos:

1. Escriba en **Name** (Nombre), el nombre con el que el origen de datos se va a mostrar en el catálogo.

1. Escriba el valor de **ProjectID**. Debe ser un identificador de proyecto completo. Por ejemplo, mydomain.com:myProject.

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione **Registrar**.

    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Configurar el origen de BigQuery" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar un proyecto de Google BigQuery e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no está configurado, siga los pasos mencionados [aquí](./manage-integration-runtimes.md).

1. Vaya a **Sources** (Orígenes).

1. Seleccione el proyecto de **BigQuery** registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante IR): seleccione el entorno de ejecución de integración configurado.

    1. **Credencial**: al configurar las credenciales de BigQuery, asegúrese de:

        * Seleccionar **Basic Authentication** (Autenticación básica) como método de autenticación
        * Proporcione el identificador de correo electrónico de la cuenta de servicio en el campo Nombre de usuario. Por ejemplo, xyz\@developer.gserviceaccount.com.
        * Guarde el archivo de clave privada de la cuenta de servicio en formato JSON en el secreto del almacén de claves.

        Para crear una nueva clave privada desde la plataforma en la nube de Google:
        1. En el menú de navegación, en las opciones IAM y administrador, seleccione \>Cuentas de servicio\>, Seleccionar un proyecto\>. 
        1. Seleccione la dirección de correo electrónico de la cuenta de servicio para la que desea crear una clave.
        1. Seleccione la pestaña **Claves**.
        1. Seleccione el menú desplegable **Agregar clave** y, a continuación, seleccione Crear nueva clave. 
        1. Elija el formato JSON.

          > [!Note]
          > El contenido de la clave privada se guarda en un archivo temporal en la máquina virtual cuando se ejecutan los procesos de examen. Este archivo temporal se elimina después de que los exámenes se hayan completado correctamente. Si se produce un error en cualquiera de ellos, el sistema seguirá reintentando la operación hasta que se ejecute correctamente. Asegúrese de que el acceso está restringido adecuadamente en la máquina virtual en la que se ejecuta SHIR.

        Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    1. **Driver location** (Ubicación del controlador): especifique la ruta de acceso a la ubicación del controlador JDBC en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado. Debe ser la ruta de acceso a la ubicación válida de la carpeta JAR. 

        > [!Note]
        > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

    1. **Conjunto de datos**: especifique una lista de conjuntos de datos de BigQuery que se importarán.
        Por ejemplo, dataset1; dataset2. Si la lista está vacía, se importan todos los conjuntos de datos disponibles.
        Los patrones de nombres de conjunto de datos aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %.

        Por ejemplo, A%; %B; %C%; D
        * empieza por A o
        * termina en B o
        * contiene C o
        * igual a D

        No se acepta el empleo de NOT ni de caracteres especiales.

    1. **Memoria máxima disponible**: memoria máxima (en GB) disponible en la máquina virtual que se va a usar en los procesos de examen. Depende del tamaño del proyecto de Google BigQuery que se va a examinar.

        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Examinar origen de BigQuery" border="true":::

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
