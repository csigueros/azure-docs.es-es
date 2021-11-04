---
title: Conexión y administración de Cassandra
description: En esta guía se describe cómo conectarse a Cassandra en Azure Purview y utilizar las funciones de Purview para explorar y administrar el origen de Cassandra.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 88eda8bbfcf10594ce0c60d2337e6f3ad63fac81
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472473"
---
# <a name="connect-to-and-manage-cassandra-in-azure-purview"></a>Conexión y administración de Cassandra en Azure Purview

En este artículo se describe cómo registrar Cassandra y cómo autenticar e interactuar con Cassandra en Azure Purview. Para obtener más información sobre Azure Purview, lea el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-cassandra.md)|

> [!Important]
> Las versiones de servidor de Cassandra admitidas son 3.*x* o 4.*x*.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
  Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla aquí](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Register

En esta sección se describe cómo registrar Cassandra en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar un nuevo servidor de Cassandra en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview.
1. Seleccione **Mapa de datos** en el panel izquierdo.
1. Seleccione **Registrar**.
1. En la pantalla **Register sources** (Registrar orígenes), seleccione **Cassandra** y, luego, elija **Continuar**:

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Captura de pantalla que muestra la pantalla Register sources (Registrar orígenes)." border="true":::

1. En la pantalla **Register sources (Cassandra)** (Registrar orígenes [Cassandra]), haga lo siguiente:

   1. Escriba un **nombre**. El origen de datos usará este nombre en el catálogo.
   1. En el cuadro **Host**, escriba la dirección del servidor donde se ejecuta el servidor de Cassandra. Por ejemplo, 20.190.193.10.
   1. En el cuadro **Puerto**, escriba el puerto que usa el servidor de Cassandra.
   1. Seleccione una colección o cree una (opcional).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Captura de la pantalla Register sources (Cassandra) (Registrar orígenes [Cassandra])." border="true":::
   1. Seleccione **Registrar**.

## <a name="scan"></a>Examinar

Siga los pasos a continuación para examinar Cassandra, identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione **Entornos de ejecución de integración**. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no tiene uno configurado, lleve a cabo [estos pasos para configurar un entorno de ejecución de integración autohospedado](./manage-integration-runtimes.md).

1. Vaya a **Orígenes**.

1. Seleccione el servidor de Cassandra registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Proporcione los detalles siguientes:

    1. **Nombre**: especifique un nombre para el examen.

    1. **Conectar mediante el entorno de ejecución de integración**: seleccione el entorno de ejecución de integración configurado.

    1. **Credencial**: al configurar las credenciales de Cassandra, asegúrese de realizar las acciones a continuación.

        * Seleccione **Autenticación básica** como método de autenticación.
        * En el cuadro **Nombre de usuario**, proporcione el nombre del usuario con el que va a realizar la conexión. 
        * En el secreto del almacén de claves, guarde la contraseña del usuario de Cassandra con la que va a realizar la conexión.

        Para más información, consulte [Credenciales para la autenticación de origen en Purview](manage-credentials.md).

    1. **Espacios de claves**: especifique una lista de espacios de claves de Cassandra que se van a importar. Si hay varios espacios de claves, se deben separar con punto y coma. Por ejemplo, keyspace1; keyspace2. Si la lista está vacía, se importan todos los espacios de claves disponibles.

        Puede usar patrones de nombres de espacio de claves que emplean la sintaxis de la expresión LIKE de SQL, como %.

        Por ejemplo, A%; %B; %C%; D

        Esta expresión significa algo de lo siguiente:
        * Comienza por A
        * Termina en B
        * Contiene C
        * Es igual a D

        No se puede usar NOT ni caracteres especiales.

    1. **Use Secure Sockets Layer (SSL)** (Usar Capa de sockets seguros [SSL]): seleccione **True** o **False** para notificar si se debe usar Capa de sockets seguros (SSL) al conectarse al servidor de Cassandra. De forma predeterminada, el valor de esta opción es **False**.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que se va a usar en los procesos de examen. Este valor depende del tamaño del servidor de Cassandra que se va a examinar.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Examinar el origen de Cassandra" border="true":::

1. Seleccione **Test Connection** (Probar conexión).

1. Seleccione **Continuar**.

1. Seleccione un **desencadenador de examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y, luego, seleccione **Guardar y ejecutar**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
