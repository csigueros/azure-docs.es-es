---
title: Conexión y administración de Azure Files
description: En esta guía se describe cómo conectarse a Azure Files en Azure Purview y usar las características de Purview para examinar y administrar el origen de Azure Files.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e91e435ca2d8050a0c6d9728c4d010f9e9c844c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076270"
---
# <a name="connect-to-and-manage-azure-files-in-azure-purview"></a>Conexión y administración de Azure Files en Azure Purview

En este artículo se describe cómo registrar Azure Files y cómo autenticarse e interactuar con Azure Files en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register) | [Sí](#scan) | [Sí](#scan) | [Sí](#scan) | [Sí](#scan) | No | No |

Azure Files admite exámenes completos e incrementales para capturar los metadatos y las clasificaciones, en función de reglas de clasificación predeterminadas del sistema y personalizadas.

Para los tipos de archivo como csv, tsv, psv y ssv, el esquema se extrae cuando se implementan las siguientes lógicas:

1. Los valores de la primera fila no están vacíos.
2. Los valores de la primera fila son únicos.
3. Los valores de la primera fila no son una fecha ni un número

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

## <a name="register"></a>Register

En esta sección se describe cómo registrar Azure Files en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Autenticación para registro

Actualmente solo hay una forma de configurar la autenticación para los recursos compartidos de archivos de Azure:

- Clave de cuenta

#### <a name="account-key-to-register"></a>Clave de cuenta que se registrará

Cuando el método de autenticación seleccionado es **Clave de cuenta**, debe obtener la clave de acceso y almacenarla en el almacén de claves:

1. Vaya a la cuenta de almacenamiento.
1. Seleccione **Configuración > Claves de acceso**.
1. Copie la *clave* y guárdela para usarla en los pasos siguientes.
1. Vaya a almacén de claves.
1. Seleccione **Configuración > Secretos**.
1. Seleccione **+ Generar/Importar** y escriba el **nombre** y el **valor** como la *clave* de la cuenta de almacenamiento.
1. Seleccione **Crear** para completar la acción.
1. Si el almacén de claves todavía no está conectado a Purview, deberá [crear una conexión del almacén de claves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Por último, [cree una credencial](manage-credentials.md#create-a-new-credential) mediante la clave para configurar el examen.

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar una nueva cuenta de Azure Files en el catálogo de datos, siga estos pasos:

1. Vaya a Purview Data Studio.
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En **Register sources** (Registrar orígenes), seleccione **Azure Files**.
1. Seleccione **Continuar**

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Registro de un origen de datos nuevo" border="true":::

En la pantalla **Register sources (Azure Files)** (Registrar orígenes [Azure Files]), siga estos pasos:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.
2. Elija la suscripción a Azure para filtrar las cuentas de Azure Storage.
3. Seleccione una cuenta de Azure Storage.
4. Seleccione una colección o cree una nueva (opcional).
5. Seleccione **Registrar** para registrar el origen de datos.

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que se indican a continuación para analizar Azure Files para identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Data Map** (Mapa de datos) en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de Azure Files que ha registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial de clave de cuenta para conectarse al origen de datos.

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="Configurar examen":::

1. Elija los elementos adecuados de la lista para limitar el ámbito del examen a bases de datos específicas.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen. Puede elegir entre los valores predeterminados del sistema, los conjuntos de reglas personalizadas existentes o la creación de un conjunto de reglas en línea.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación para que se repita o ejecutar el examen una vez.

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
