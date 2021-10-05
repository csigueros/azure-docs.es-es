---
title: Creación y uso de identidad administrada
titleSuffix: Azure Cognitive Services
description: Descripción de cómo crear y usar la identidad administrada en Azure Portal
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 09/09/2021
ms.author: lajanuar
ms.openlocfilehash: 688fd2391d12f74b46a16954706b3c9e0ee1fb8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771873"
---
# <a name="create-and-use-managed-identity"></a>Creación y uso de identidad administrada

> [!IMPORTANT]
>
> La identidad administrada para la traducción de documentos no está disponible actualmente en la región global. Si piensa usar la identidad administrada para las operaciones de traducción de documentos, [cree el recurso de Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) en una región de Azure no global.

## <a name="what-is-managed-identity"></a>¿Qué es una identidad administrada?

 La identidad administrada de Azure es una entidad de servicio que crea una identidad de Azure Active Directory (Azure AD) y permisos específicos para los recursos administrados de Azure. Puede usar una identidad administrada para conceder acceso a cualquier recurso que admita la autenticación de Azure AD. Para conceder acceso, asigne un rol a una identidad administrada mediante el [control de acceso basado en roles de Azure](../../../role-based-access-control/overview.md) (Azure RBAC).  El uso de una identidad administrada en Azure no tiene ningún costo adicional.

La identidad administrada admite cuentas de Azure Blob Storage de acceso privado y público.  Para las cuentas de almacenamiento **con acceso público**, puede optar por usar una firma de acceso compartido (SAS) para conceder acceso limitado.  En este artículo, examinaremos cómo administrar el acceso a los documentos de traducción de la cuenta de Azure Blob Storage mediante la identidad administrada asignada por el sistema.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [**cuenta activa de Azure**](https://azure.microsoft.com/free/cognitive-services/): en caso de no tener una, puede [**crear una cuenta gratuita**](https://azure.microsoft.com/free/).

* Un [**recurso Translator de servicio único**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (no un recurso multiservicio de Cognitive Services) asignado a una región **no global**. Para conocer los pasos detallados, _consulte_ [Creación de un recurso de Cognitive Services con Azure Portal](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Estar algo familiarizado con el [**control de acceso basado en roles de Azure**](../../../role-based-access-control/role-assignments-portal.md) mediante Azure Portal.

* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) en la misma región que el recurso de Translator. Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento. 

* Si la cuenta de almacenamiento está detrás de un firewall, **debe habilitar la configuración siguiente**: </br>

  * En la página de la cuenta de almacenamiento, seleccione **Seguridad y redes** → **Redes** en el menú izquierdo.
    :::image type="content" source="../media/managed-identities/security-and-networking-node.png" alt-text="Captura de pantalla: pestaña Seguridad y redes.":::

  * En la ventana principal, seleccione **Permitir acceso desde Redes seleccionadas**.
  :::image type="content" source="../media/managed-identities/firewalls-and-virtual-networks.png" alt-text="Captura de pantalla: botón de radio Redes seleccionadas elegido.":::

  * En la página de redes seleccionadas, vaya a la categoría **Excepciones** y asegúrese de que la casilla [**Allow Azure services on the trusted services list to access this storage account**](/azure/storage/common/storage-network-security?tabs=azure-portal#manage-exceptions) (Permitir que los servicios de Azure de la lista de servicios de confianza accedan a esta cuenta de almacenamiento) esté seleccionada.

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Captura de pantalla: casilla para permitir los servicios de confianza, vista del portal":::

## <a name="managed-identity-assignments"></a>Asignaciones de identidad administrada

Hay dos tipos de identidad administrada: las **asignadas por el sistema** y las **asignadas por el usuario**.  Actualmente, la traducción de documentos es compatible con las identidades administradas asignadas por el sistema. Una identidad administrada asignada por el sistema se **habilita** directamente en una instancia de servicio. No está habilitada de forma predeterminada; debe ir al recurso y actualizar la configuración de identidad. La identidad administrada asignada por el sistema está vinculada al recurso a lo largo de su ciclo de vida. Si elimina el recurso, la identidad administrada se eliminará también.

En los pasos siguientes, habilitaremos una identidad administrada asignada por el sistema y concederemos al recurso de Translator acceso limitado a la cuenta de Azure Blob Storage.

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>Habilitación de una identidad administrada asignada por el sistema mediante Azure Portal

>[!IMPORTANT]
>
> Para habilitar una identidad administrada asignada por el sistema, necesita permisos de **Microsoft.Authorization/roleAssignments/write** como, por ejemplo, [**Propietario**](../../../role-based-access-control/built-in-roles.md#owner) o [**Administrador de acceso de usuarios**](../../../role-based-access-control/built-in-roles.md#user-access-administrator). Puede especificar un ámbito en cuatro niveles: grupo de administración, suscripción, grupo de recursos o recurso.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a su suscripción de Azure.

1. Vaya hasta la página del recurso de **Translator** en Azure Portal.

1. En el raíl izquierdo, seleccione **Identidad** en la lista **Administración de recursos**:

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="Captura de pantalla: pestaña de identidad de administración de recursos en Azure Portal.":::

1. En la ventana principal, cambie la pestaña **Estado asignado por el sistema** a **On**.

1. En **Permisos**, seleccione **Asignaciones de roles de Azure**:

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Captura de pantalla: habilitación de una identidad administrada asignada por el sistema en Azure Portal":::.

1. Se abrirá una página de asignaciones de roles de Azure. Elija su suscripción en el menú desplegable y, a continuación, seleccione **&plus;Agregar asignación de roles**.

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Captura de pantalla: página Asignaciones de roles de Azure en Azure Portal.":::

    >[!NOTE]
    >
    > Si no puede asignar un rol en Azure Portal porque la opción Agregar > Agregar asignación de roles está deshabilitada o experimenta este error de permisos: "no tiene permisos para agregar la asignación de roles en este ámbito", compruebe que tiene una sesión iniciada actualmente como usuario con un rol asignado que disponga de permisos Microsoft.Authorization/roleAssignments/write como, por ejemplo, [**Propietario**](../../../role-based-access-control/built-in-roles.md#owner) o [**Administrador de acceso de usuario**](../../../role-based-access-control/built-in-roles.md#user-access-administrator) en el ámbito de almacenamiento del recurso de almacenamiento.

1. A continuación, va a asignar un rol **Colaborador de datos de blobs de almacenamiento** al recurso del servicio Translator. En la ventana emergente **Agregar asignación de roles**, complete los campos como se muestra a continuación y seleccione **Guardar**:

    | Campo | Valor|
    |------|--------|
    |**Ámbito**| **_Storage_**.|
    |**Suscripción**| **_La suscripción asociada al recurso de almacenamiento_**.|
    |**Recurso**| **_El nombre del recurso de almacenamiento_**.|
    |**Rol** | **_Colaborador de datos de Storage Blob_**.|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="Captura de pantalla: página Agregar asignación de roles en Azure Portal.":::

1. Después de recibir el mensaje de confirmación _Asignación de roles agregada_, actualice la página para ver la asignación de roles agregada. 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="Captura de pantalla: mensaje emergente de confirmación de asignación de roles agregada.":::

1. Si no ve el cambio de inmediato, espere e intente actualizar la página una vez más. Al asignar o quitar asignaciones de roles, los cambios pueden tardar hasta 30 minutos en aplicarse.

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="Captura de pantalla: ventana de asignaciones de roles de Azure.":::

 Estupendo. Ha completado los pasos para habilitar una identidad administrada asignada por el sistema. Con esta credencial de identidad, puede conceder a Translator derechos de acceso específicos al recurso de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Preguntas más frecuentes sobre identidades administradas para recursos de Azure](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[Uso de identidades administradas para adquirir un token de acceso](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)
