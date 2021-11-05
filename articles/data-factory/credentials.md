---
title: Mediante credenciales
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre el uso de credenciales de Azure para Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: synapse
ms.openlocfilehash: a588bf8a02c6683143cff8096ee6e03c6b120234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093580"
---
# <a name="credentials-in-azure-data-factory-and-azure-synapse"></a>Credenciales de Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="prerequisites"></a>Prerrequisitos

Los usuarios deben tener el rol Operador de identidad administrada (RBAC de Azure) o un rol personalizado con la acción RBAC **Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action** para configurar una identidad administrada asignada por el usuario como una credencial. Se requiere RBAC adicional para crear y usar credenciales en Synapse. [Más información](../synapse-analytics/security/synapse-workspace-synapse-rbac-roles.md).

## <a name="using-credentials"></a>Mediante credenciales

Presentamos Credenciales, que pueden contener identidades administradas asignadas por el usuario, entidades de servicio y también enumerar la identidad administrada asignada por el sistema que puede usar en los servicios vinculados que admiten la autenticación de Azure Active Directory (AAD). Le ayudan a consolidar y administrar todas las credenciales basadas en AAD.  

A continuación se muestran los pasos genéricos para usar una **identidad administrada asignada por el usuario** en los servicios vinculados para la autenticación. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1. Si no tiene una identidad administrada asignada por el usuario creada en Azure, primero cree una en la página de [Identidades administradas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) de Azure Portal.

1. Asocie la identidad administrada asignada por el usuario a la instancia de Data Factory mediante Azure Portal, el SDK, PowerShell, la API de REST. La captura de pantalla siguiente usa Azure Portal (hoja Data Factory) para asociar la identidad administrada asignada por el usuario.

   :::image type="content" source="media/credentials/uami-azure-portal.png" alt-text="Captura de pantalla que muestra cómo usar Azure Portal para asociar una identidad administrada asignada por el usuario.":::

1. Cree una **Credencial** en la interfaz de usuario de Data Factory de forma interactiva. Puede seleccionar la identidad administrada asignada por el usuario asociada a Data Factory en el paso 1. 

   :::image type="content" source="media/credentials/create-new-credential.png" alt-text="Captura de pantalla que muestra la creación de unas credenciales.":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Captura de pantalla que muestra la configuración de unas credenciales.":::

1. Cree un nuevo servicio vinculado y seleccione **Identidad administrada asignada por el usuario** en autenticación

   :::image type="content" source="media/credentials/create-new-linked-service.png" alt-text="Captura de pantalla que muestra el nuevo servicio vinculado con la autenticación de identidad administrada asignada por el usuario.":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Captura de pantalla que muestra la nueva configuración del servicio vinculado con identidad administrada asignada por el usuario y credenciales seleccionadas.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Si no tiene una identidad administrada asignada por el usuario creada en Azure, primero cree una en la página de [Identidades administradas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) de Azure Portal.

1. Asocie la identidad administrada asignada por el usuario al área de trabajo mediante Azure Portal, el SDK, PowerShell, la API de REST. La captura de pantalla siguiente usa Azure Portal (hoja de identidad) para asociar la identidad administrada asignada por el usuario.

   :::image type="content" source="media/credentials/synapse-uami-azure-portal.png" alt-text="Captura de pantalla que muestra cómo usar Azure Portal para asociar una identidad administrada asignada por el usuario.":::

1. Cree una **Credencial** en Synapse Studio interactivamente. Puede seleccionar la identidad administrada asignada por el usuario asociada al área de trabajo en el paso 1.

   :::image type="content" source="media/credentials/synapse-create-new-credential.png" alt-text="Captura de pantalla que muestra la creación de nuevas credenciales.":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="Captura de pantalla que muestra la configuración de unas credenciales.":::

1. Cree un nuevo servicio vinculado y seleccione **Identidad administrada asignada por el usuario** en autenticación

   :::image type="content" source="media/credentials/synapse-create-new-linked-service.png" alt-text="Captura de pantalla que muestra el nuevo servicio vinculado con la autenticación de identidad administrada asignada por el usuario.":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="Captura de pantalla que muestra la nueva configuración del servicio vinculado con identidad administrada asignada por el usuario y credenciales seleccionadas.":::

---

> [!NOTE] 
> Puede usar SDK, PowerShell o la API REST para las acciones anteriores.

## <a name="next-steps"></a>Pasos siguientes

- [Identidad administrada](data-factory-service-identity.md)

Consulte los siguientes temas que presentan cuándo y cómo usar la identidad administrada:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md)
- [Copia de datos con Azure Data Lake Storage Gen1 como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md)

Vea [¿Qué es Managed Identities for Azure Resources?](../active-directory/managed-identities-azure-resources/overview.md) para obtener más información sobre las identidades administradas para recursos de Azure en las que se basa la identidad administrada de Data Factory.
