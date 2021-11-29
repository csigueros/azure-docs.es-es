---
title: Aprovisionamiento de directivas de acceso a los datos para Azure Storage
description: Guía paso a paso sobre cómo integrar Azure Storage en directivas de acceso de Azure Purview y permitir que los propietarios de datos creen directivas de acceso.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/15/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eab2c720aafe2cfd5a1ca46f2549b42d6f644b25
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555724"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Aprovisionamiento de conjuntos de datos por propietario de datos para Azure Storage

## <a name="supported-capabilities"></a>Funcionalidades admitidas
En esta guía se explica cómo configurar Azure Storage para aplicar directivas de acceso a datos creadas y administradas desde Azure Purview. La creación de directivas de Azure Purview admite las siguientes capacidades:
-   Directivas de acceso a datos para controlar el acceso a los datos almacenados en Blob o Azure Data Lake Storage (ADLS) Gen2

> [!IMPORTANT]
> Actualmente, estas capacidades están en versión preliminar. Esta versión preliminar se proporciona sin un contrato de nivel de servicio y no debe usarse para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="important-limitations"></a>Limitaciones importantes
1. La característica de directiva de acceso solo está disponible en las nuevas cuentas de Azure Purview y Azure Storage.
2. Registre todos los orígenes de datos para usar gobernanza y administre todas las directivas de acceso asociadas en una sola cuenta de Azure Purview.
3. Esta característica solo se puede usar en las regiones que se indican a continuación, donde se implementan la administración de directivas de acceso y la funcionalidad de cumplimiento.

### <a name="supported-regions"></a>Regiones admitidas

#### <a name="azure-purview-management-side"></a>Azure Purview (lado de administración)
-   Norte de Europa
-   Oeste de Europa
-   Sur de Reino Unido
-   Este de EE. UU.
-   Este de EE. UU. 2
-   Centro-sur de EE. UU.
-   Oeste de EE. UU. 2
-   Sudeste de Asia
-   Este de Australia
-   Centro de Canadá
-   Centro de Francia

#### <a name="azure-storage-enforcement-side"></a>Azure Storage (lado de cumplimiento)
-   Centro de Francia
-   Centro de Canadá

## <a name="prerequisites"></a>Requisitos previos
> [!Important]
> Lea atentamente esta sección. Hay varios requisitos previos para que las directivas de acceso funcionen correctamente.

### <a name="select-an-isolated-test-subscription"></a>Selección de una suscripción de prueba aislada
Cree o use una suscripción de prueba aislada y siga estos pasos para crear una cuenta de Azure Purview y una de Azure Storage en esa suscripción.

### <a name="create-azure-storage-account"></a>Creación de una cuenta de Azure Storage
Cree una nueva cuenta de Azure Storage en las regiones indicadas anteriormente en Limitaciones. Vea [Creación de una cuenta de Azure Storage](../storage/common/storage-account-create.md)

### <a name="configure-azure-storage-to-enforce-access-policies-from-purview"></a>Configuración de Azure Storage para aplicar directivas de acceso de Purview

#### <a name="enable-access-policy-enforcement-in-the-subscription"></a>Habilitación del cumplimiento de directivas de acceso en la suscripción
Para registrar y confirmar que la funcionalidad de la directiva de acceso está habilitada en la suscripción donde reside la cuenta de Azure Storage, ejecute los siguientes comandos en PowerShell:

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
Si la salida del último comando muestra el valor de "RegistrationState" como "Registrado", la suscripción está habilitada para esta función.

#### <a name="check-access-permissions-in-azure-storage"></a>Comprobación de los permisos de acceso en Azure Storage
Un usuario debe tener el rol "Propietario" en la cuenta de Azure Storage para registrar posteriormente este origen de datos en Azure Purview para las directivas de acceso: [Comprobación del acceso de un usuario a los recursos de Azure](../role-based-access-control/check-access.md)

### <a name="create-azure-purview-account"></a>Creación de una cuenta de Azure Purview
Cree una nueva cuenta de Azure Purview en las regiones donde está habilitada la nueva funcionalidad, en la suscripción de prueba aislada. Para crear una cuenta de Purview, consulte [Inicio rápido: Creación de una cuenta de Azure Purview en Azure Portal](create-catalog-portal.md).

### <a name="configure-azure-purview-to-manage-access-policies"></a>Configuración de Azure Purview para administrar directivas de acceso
Realice todos los pasos siguientes para permitir que Azure Purview administre directivas de acceso 

#### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Participación en la versión preliminar de la directiva de uso de datos de Azure Purview
Esta funcionalidad está actualmente en versión preliminar, por lo que debe [participar en la versión preliminar de las directivas de uso de datos de Purview](https://aka.ms/opt-in-data-use-policy)

#### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>Registro de Purview como proveedor de recursos en otras suscripciones
Ejecute este paso solo si la cuenta de Storage cuyo acceso quiere administrar está en una suscripción diferente a la de la cuenta de Azure Purview. Registre Azure Purview como proveedor de recursos en esas suscripciones mediante esta guía:  
[Tipos y proveedores de recursos de Azure](../azure-resource-manager/management/resource-providers-and-types.md)

#### <a name="configure-permissions-for-policy-management-actions"></a>Configuración de permisos para acciones de administración de directivas
- El usuario debe ser propietario y administrador del origen de datos de Purview para registrar un origen para la gobernanza del uso de datos. Sin embargo, cualquiera de esos roles de forma independiente puede quitar el registro del origen para la gobernanza del uso de datos.
- El usuario debe formar parte del rol Creador de directivas de Purview en el nivel de colección raíz para realizar acciones de creación o administración de directivas.
- El usuario debe formar parte del rol Administrador de orígenes de datos de Purview en el nivel de colección raíz para publicar la directiva.

Vea la sección sobre la administración de asignaciones de roles en esta guía: [Procedimientos para crear y administrar colecciones](how-to-create-and-manage-collections.md).

Además de estos, consulte la sección "Problemas conocidos" en la parte inferior de este documento.

#### <a name="register-and-scan-data-sources-in-purview"></a>Registro y examen de orígenes de datos en Purview
Registre y examine cada origen de datos con Purview para más adelante definir directivas de acceso. Siga las guías de registro de Purview para registrar la cuenta de almacenamiento:

-   [Registro y examen de Azure Storage Blob: Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Registro y examen de Azure Data Lake Storage (ADLS) Gen2: Azure Purview](register-scan-adls-gen2.md)

Durante el registro, habilite el origen de datos de la directiva de acceso por medio del botón de alternancia **Gobernanza del uso de datos**, como se muestra en la imagen

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Imagen en la que se muestra cómo registrar un origen de datos para la directiva.":::

> [!NOTE]
> El comportamiento del botón de alternancia asegura que todos los orígenes de datos de una suscripción determinada solo se puedan registrar en la gobernanza del uso de datos en una sola cuenta de Purview. Esa cuenta de Purview podría estar en cualquier suscripción del inquilino.

## <a name="policy-authoring"></a>Creación de directivas

En esta sección se describen los pasos necesarios para crear, actualizar y publicar directivas de acceso de Purview.

### <a name="create-a-new-policy"></a>Creación de una nueva directiva

En esta sección se describen los pasos necesarios para crear una directiva en Azure Purview.

1. Inicie sesión en el portal de Purview.

1. Vaya a la aplicación **Administración de directivas** desde el panel izquierdo.

1. Seleccione el botón **Nueva directiva** en la página de la directiva.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede acceder a la función de directiva en Azure Purview cuando quiere crear directivas.":::

1. Aparecerá la nueva página de directiva. Escriba el **Nombre** de la directiva y una **Descripción**.

1. Para agregar instrucciones de directiva a la nueva directiva, seleccione el botón **New policy statement** (Nueva instrucción de directiva). Se mostrará el generador de instrucciones de directiva.

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="Imagen en la que muestra cómo un propietario de datos puede crear una instrucción de directiva.":::

1. Seleccione el botón **Acción** y elija Leer o Modificar en la lista desplegable.

1. Seleccione el botón **Efecto** y elija Allow from the drop-down list (Permitir en la lista desplegable).

1. Seleccione el botón **Recursos de datos** para mostrar las opciones a fin de proporcionar la ruta de acceso del recurso de datos.

1. En el cuadro **Recursos**, escriba el **Tipo de origen de datos** y seleccione el **Nombre** de un origen de datos registrado previamente.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede seleccionar un recurso de datos al editar una instrucción de directiva.":::

1. Seleccione el botón **Continuar** y navegue por la jerarquía para seleccionar la carpeta o archivo. Después, seleccione el botón **Agregar**. Esto le llevará de vuelta al editor de directivas.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede seleccionar el recurso al crear o editar una instrucción de directiva.":::

1. Seleccione el botón **Subjects** (Firmantes) y especifique la identidad del firmante como entidad de seguridad, grupo o MSI. Después, seleccione el botón **Aceptar**. Esto le llevará de vuelta al editor de directivas.

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede seleccionar el firmante al crear o editar una instrucción de directiva.":::

1. Repita los pasos del 5 al 11 para escribir más instrucciones de directiva.

1. Seleccione el botón **Guardar** para guardar la directiva.

### <a name="update-or-delete-a-policy"></a>Actualización o eliminación de una directiva

Los pasos para crear una directiva en Purview son los siguientes.

1. Inicie sesión en el portal de Purview.

1. Vaya a la aplicación de directivas de Purview desde el panel izquierdo.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede acceder a la función de directiva en Azure Purview cuando quiere actualizar una directiva.":::

1. El portal de directivas presentará la lista de directivas existentes en Purview. Seleccione la directiva que debe actualizarse.

1. Aparecerá la página de detalles de la directiva, incluidas las opciones Editar y Eliminar. Seleccione el botón **Editar**, que abre el generador de instrucciones de directiva para las instrucciones de esta directiva. Ahora, se pueden actualizar todas las partes de las instrucciones de esta directiva. Para eliminar la directiva, use el botón **Eliminar**.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede editar o eliminar una instrucción de directiva.":::

### <a name="publish-the-policy"></a>Publicación de la directiva

Una directiva recién creada tiene el estado de borrador. El proceso de publicación asocia la nueva directiva con uno o varios orígenes de datos bajo gobernanza.
Los pasos necesarios para publicar una directiva son los siguientes:

1. Inicie sesión en el portal de Purview.

1. Vaya a la aplicación de directivas de Purview desde el panel izquierdo.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede acceder a la función de directiva en Azure Purview cuando quiere publicar una directiva.":::

1. El portal de directivas presentará la lista de directivas existentes en Purview. Busque la directiva que debe publicarse. Seleccione el botón **Publicar** en la esquina superior derecha de la página.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede publicar una directiva.":::

1. Se muestra una lista de orígenes de datos. Puede escribir un nombre para filtrar la lista. Después, seleccione cada origen de datos en el que se vaya a publicar esta directiva y luego el botón **Publicar**.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede seleccionar el origen de datos donde se publicará la directiva.":::

>[!NOTE]
> Publicar es una operación que se realiza en segundo plano. Los cambios pueden tardar hasta **2 horas** en reflejarse en el origen de datos.

## <a name="azure-purview-policy-action-to-azure-storage-action-mapping"></a>Acción de directiva de Azure Purview en asignación de acción de Azure Storage

Esta sección contiene una referencia de cómo las acciones de las directivas de datos de Azure Purview se asignan a acciones específicas de Azure Storage.

| **Acción de directiva de Purview** | **Acciones específicas del origen de datos**                                                        |
|---------------------------|-----------------------------------------------------------------------------------------|
|||
| *Lectura*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|||
| *Modificar*                  |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write               |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action          |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action         |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete              |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/write                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/delete                    |
|||

## <a name="known-issues"></a>Problemas conocidos
Estos son los problemas conocidos de esta versión
1. Además del rol de autor de directivas, el usuario requiere el permiso Lector de directorios de Azure Active Directory (AAD) para crear la directiva de propietario de datos.
1. El rol del autor de directivas no es suficiente para crear directivas. También requiere el rol de administrador del origen de datos de Purview.

## <a name="next-steps"></a>Pasos siguientes
Vea el blog y la demostración relacionados con las capacidades mencionadas en esta guía paso a paso

* [Novedades de Azure Purview en Microsoft Ignite 2021](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Demostración de directiva de acceso de Azure Storage](https://www.youtube.com/watch?v=CFE8ltT19Ss)
