---
title: Cómo examinar Azure Files
description: En esta guía se describen los detalles de cómo examinar Azure Files.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/22/2021
ms.openlocfilehash: 39e720f35a591ac7075b5723f3e577151e698371
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605163"
---
# <a name="register-and-scan-azure-files"></a>Registro y examen de Azure Files

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Azure Files admite exámenes completos e incrementales para capturar los metadatos y las clasificaciones, en función de reglas de clasificación predeterminadas del sistema y personalizadas.

Para los tipos de archivo como csv, tsv, psv y ssv, el esquema se extrae cuando se implementan las siguientes lógicas:

1. Los valores de la primera fila no están vacíos.
2. Los valores de la primera fila son únicos.
3. Los valores de la primera fila no son una fecha ni un número.

## <a name="prerequisites"></a>Requisitos previos

- Antes de registrar los orígenes de datos, cree una cuenta de Azure Purview. Para más información sobre cómo crear una cuenta de Purview, consulte [Inicio rápido: creación de una cuenta de Azure Purview](create-catalog-portal.md).
- Debe ser administrador de los orígenes de datos para poder configurar y programar exámenes. Consulte [Permisos del catálogo](catalog-permissions.md) para obtener más información.

## <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

Actualmente solo hay una manera de configurar la autenticación para el almacenamiento de archivos de Azure:

- Clave de cuenta

### <a name="account-key"></a>Clave de cuenta

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

## <a name="register-an-azure-files-storage-account"></a>Registro de una cuenta de Azure Files

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

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de Purview Studio.

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

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
