---
title: Aprovisionamiento de directivas de acceso a los datos para Azure Storage
description: Guía paso a paso sobre cómo integrar Azure Storage en directivas de acceso de Azure Purview y permitir que los propietarios de datos creen directivas de acceso.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ea5285c5fd29bfe34f97c87b2ac0c9bd7a5502a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425242"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Aprovisionamiento de conjuntos de datos por propietario de datos para Azure Storage

## <a name="supported-capabilities"></a>Funcionalidades admitidas

La creación de directivas de Purview admite las siguientes capacidades:
-   Directiva de acceso a datos de Azure Storage para poder controlar el acceso a los datos almacenados en blobs o archivos de ADLS Gen2

> [!IMPORTANT]
> Actualmente, estas capacidades están en versión preliminar. Esta versión preliminar se proporciona sin un contrato de nivel de servicio y no debe usarse para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="prerequisites"></a>Prerrequisitos

### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Optar por participar en la versión preliminar de la directiva de uso de datos de Azure Purview
Esta funcionalidad está actualmente en versión preliminar, por lo que deberá participar en la [versión preliminar de las directivas de uso de datos de Purview](https://aka.ms/opt-in-data-use-policy).

### <a name="provision-new-accounts-in-an-isolated-test-subscription"></a>Aprovisionamiento de nuevas cuentas en una suscripción de prueba aislada
Siga estos pasos para crear una cuenta de Azure Purview y una de Azure Storage en una suscripción de prueba aislada. Después, habilite la función de directiva de acceso en estas cuentas.

### <a name="supported-regions"></a>Regiones admitidas

> [!IMPORTANT]
> 1. La característica de directiva de acceso solo está disponible en las nuevas cuentas de Azure Purview y Azure Storage.
> 2. Esta característica solo se puede usar en las regiones enumeradas a continuación, donde se implementa la función de directiva de acceso.

#### <a name="azure-purview"></a>Azure Purview 

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


#### <a name="azure-storage"></a>Azure Storage

-   Centro de Francia
-   Centro de Canadá


### <a name="create-azure-purview-account"></a>Creación de una cuenta de Azure Purview

Cree una cuenta de Azure Purview en las regiones donde está habilitada la nueva función, en la suscripción que está aislada para la nueva función.

Para crear una cuenta de Purview, consulte [Inicio rápido: Creación de una cuenta de Azure Purview en Azure Portal](create-catalog-portal.md).

### <a name="create-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Para crear una cuenta de Azure Storage, consulte [Creación de una cuenta de almacenamiento: Azure Storage](../storage/common/storage-account-create.md).

### <a name="configure-azure-purview-and-storage-for-access-policies"></a>Configuración de Azure Purview y Storage para directivas de acceso

En esta sección se describen los pasos para configurar Azure Purview y Storage a fin de habilitar las directivas de acceso.

#### <a name="register-the-access-policies-functionality-in-azure-storage"></a>Registro de la función de directivas de acceso en Azure Storage

A fin de registrar y confirmar que esta función está habilitada para la suscripción, ejecute los comandos siguientes en PowerShell.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

Si la salida del último comando muestra el valor de "RegistrationState" como "Registrado", la suscripción está habilitada para esta función.

#### <a name="register-and-scan-data-sources-in-purview"></a>Registro y examen de orígenes de datos en Purview

El origen de datos debe registrarse y examinarse con Purview para definir las directivas. Siga las guías de registro de Purview para registrar la cuenta de almacenamiento:

-   [Procedimientos para examinar Azure Storage Blob: Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Registro y examen de Azure Data Lake Storage (ADLS) Gen2: Azure Purview](register-scan-adls-gen2.md)

Durante el registro, habilite el origen de datos para la gobernanza del uso de datos, tal como se muestra en la imagen.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Imagen en la que se muestra cómo registrar un origen de datos para la directiva.":::

#### <a name="configure-permissions-for-policy-management-actions"></a>Configuración de permisos para acciones de administración de directivas

-   Un usuario debe formar parte del rol de administrador de datos de Purview para realizar acciones de creación y administración de directivas.
-   Un usuario debe formar parte del rol de administrador de orígenes de datos de Purview para publicar la directiva.

Vea la sección sobre la administración de asignaciones de roles en esta guía: [Procedimientos para crear y administrar colecciones](how-to-create-and-manage-collections.md).

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

1. Aparecerá la página de detalles de la directiva, incluidas las opciones Editar y Eliminar. Seleccione el botón **Editar** que abre el generador de instrucciones de directiva para las instrucciones de esta directiva. Ahora, se pueden actualizar todas las partes de las instrucciones de esta directiva. Para eliminar la directiva, use el botón **Eliminar**.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede editar o eliminar una instrucción de directiva.":::

### <a name="publish-the-policy"></a>Publicación de la directiva

Una directiva recién creada tiene el estado de borrador. El proceso de publicación asocia la nueva directiva con uno o varios orígenes de datos bajo gobernanza.
Los pasos necesarios para publicar una directiva son los siguientes:

1. Inicie sesión en el portal de Purview.

1. Vaya a la aplicación de directivas de Purview desde el panel izquierdo.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede acceder a la función de directiva en Azure Purview cuando quiere publicar una directiva.":::

1. El portal de directivas presentará la lista de directivas existentes en Purview. Busque la directiva que debe publicarse. Seleccione el botón **Publicar** en la esquina superior derecha de la página.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede publicar una directiva.":::

1. Se muestra una lista de orígenes de datos. Puede escribir un nombre para filtrar la lista. Después, seleccione cada origen de datos en el que se vaya a publicar esta directiva y luego el botón **Publicar**. Publicar es una operación que se realiza en segundo plano. Los cambios pueden tardar hasta 2 horas en reflejarse en el origen de datos.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Imagen en la que se muestra cómo un propietario de datos puede seleccionar el origen de datos donde se publicará la directiva.":::

## <a name="next-steps"></a>Pasos siguientes

Consulte este artículo para comprender los conceptos relacionados con Azure Purview:

* [Información general de Azure Purview](overview.md)
