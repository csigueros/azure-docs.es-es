---
title: Creación y uso de identidades administradas con bring-your-own-storage (BYOS)
titleSuffix: Azure Applied AI Services
description: Descripción de cómo crear y usar la identidad administrada con cuentas BYOS
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/08/2021
ms.author: lajanuar
ms.openlocfilehash: 8ecd6ae9578f719707c3d52ba8348cda5af3e08d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326511"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>Creación y uso de identidades administradas para el recurso de Form Recognizer

> [!IMPORTANT]
> La asignación de control de acceso basado en rol (Azure RBAC) de Azure se encuentra actualmente en versión preliminar y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Las asignaciones de Azure RBAC se usan para conceder permisos para la identidad administrada.

## <a name="what-is-managed-identity"></a>¿Qué es una identidad administrada?

La identidad administrada de Azure es una entidad de servicio que crea una identidad de Azure Active Directory (Azure AD) y permisos específicos para los recursos administrados de Azure. Puede usar una identidad administrada para conceder acceso a cualquier recurso que admita la autenticación de Azure AD. Para conceder acceso, asigne un rol a una identidad administrada mediante el [control de acceso basado en roles de Azure](../../role-based-access-control/overview.md) (Azure RBAC).  El uso de una identidad administrada en Azure no tiene ningún costo adicional.

La identidad administrada admite cuentas de Azure Blob Storage de acceso privado y público.  Para las cuentas de almacenamiento con acceso público, puede optar por usar una firma de acceso compartido (SAS) para conceder acceso limitado.   En este artículo, aprenderá a habilitar una identidad administrada asignada por el sistema para la instancia de Form Recognizer.

## <a name="private-storage-account-access"></a>Acceso de la cuenta de almacenamiento privado

 Las [identidades administradas de los recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md) admiten el acceso y la autenticación de la cuenta de almacenamiento privado de Azure. Si tiene una cuenta de almacenamiento de Azure protegida por un firewall o Virtual Network (red virtual) o si ha habilitado bring-your-own-storage (BYOS), Form Recognizer no puede acceder directamente a los datos de la cuenta de almacenamiento; sin embargo, una vez habilitada una identidad administrada, el servicio Form Recognizer puede acceder a la cuenta de almacenamiento mediante una credencial de identidad administrada asignada.

> [!NOTE]
>
> * Si piensa analizar los datos de almacenamiento con la [**Herramienta de etiquetado de ejemplo de Form Recognizer (FOTT)** ](https://fott-2-1.azurewebsites.net/) debe implementar la herramienta detrás de la red virtual o el firewall.
>
> * Las API Analyze [**Receipt**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync), [**Business Card**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync), [**Invoice**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291), [**Identity Document**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707) y [**Custom Form**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) pueden extraer datos de un solo documento mediante la publicación de solicitudes como contenido binario sin formato. En estos escenarios, no hay ningún requisito para una credencial de identidad administrada.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [**cuenta activa de Azure**](https://azure.microsoft.com/free/cognitive-services/): en caso de no tener una, puede [**crear una cuenta gratuita**](https://azure.microsoft.com/free/).

* Un recurso de [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) o [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal. Para conocer los pasos detallados, _consulte_ [Creación de un recurso de Cognitive Services con Azure Portal](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows).

* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento. Si la cuenta tiene un firewall, debe tener habilitada la casilla que permite la [excepción para los servicios de Azure de confianza](../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions).

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="Captura de pantalla: casilla para permitir los servicios de confianza, vista del portal":::

* Estar algo familiarizado con el [**control de acceso basado en roles de Azure**](../../role-based-access-control/role-assignments-portal.md) mediante Azure Portal.

## <a name="managed-identity-assignments"></a>Asignaciones de identidad administrada

Hay dos tipos de identidad administrada: las **asignadas por el sistema** y las **asignadas por el usuario**. Actualmente, Form Recognizer admite la identidad administrada asignada por el sistema. Una identidad administrada asignada por el sistema se **habilita** directamente en una instancia de servicio. No está habilitada de forma predeterminada; debe ir al recurso y actualizar la configuración de identidad. La identidad administrada asignada por el sistema está vinculada al recurso a lo largo de su ciclo de vida. Si elimina el recurso, la identidad administrada se eliminará también.

En los pasos siguientes, habilitaremos una identidad administrada asignada por el sistema y concederemos a Form Recognizer acceso limitado a la cuenta de Azure Blob Storage.

## <a name="enable-a-system-assigned-managed-identity"></a>Habilitación de una identidad administrada asignada por el sistema.

>[!IMPORTANT]
>
> Para habilitar una identidad administrada asignada por el sistema, necesita permisos de **Microsoft.Authorization/roleAssignments/write** como, por ejemplo, [**Propietario**](../../role-based-access-control/built-in-roles.md#owner) o [**Administrador de acceso de usuarios**](../../role-based-access-control/built-in-roles.md#user-access-administrator). Puede especificar un ámbito en cuatro niveles: grupo de administración, suscripción, grupo de recursos o recurso.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a su suscripción de Azure.

1. Vaya hasta la página del recurso de **Form Recognizer** en Azure Portal.

1. En el raíl izquierdo, seleccione **Identidad** en la lista **Administración de recursos**:

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="Captura de pantalla: pestaña de identidad de administración de recursos en Azure Portal.":::

1. En la ventana principal, cambie la pestaña **Estado asignado por el sistema** a **On**.

1. En **Permisos**, seleccione **Asignaciones de roles de Azure**:

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="Captura de pantalla: habilitación de una identidad administrada asignada por el sistema en Azure Portal":::.

1. Se abrirá una página de asignaciones de roles de Azure. Elija su suscripción en el menú desplegable y, a continuación, seleccione **&plus;Agregar asignación de roles**.

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="Captura de pantalla: página Asignaciones de roles de Azure en Azure Portal.":::

    > [!NOTE]
    >
    > Si no puede asignar un rol en Azure Portal porque la opción Agregar > Agregar asignación de roles está deshabilitada o experimenta este error de permisos: "no tiene permisos para agregar la asignación de roles en este ámbito", compruebe que tiene una sesión iniciada actualmente como usuario con un rol asignado que disponga de permisos Microsoft.Authorization/roleAssignments/write como, por ejemplo, Propietario o Administrador de acceso de usuario en el ámbito de almacenamiento del recurso de almacenamiento.

 7. A continuación, va a asignar un rol **Lector de datos de blobs de almacenamiento** al recurso del servicio Form Recognizer. En la ventana emergente **Agregar asignación de roles**, complete los campos como se muestra a continuación y seleccione **Guardar**:

    | Campo | Valor|
    |------|--------|
    |**Ámbito**| **_Storage_**|
    |**Suscripción**| **_La suscripción asociada al recurso de almacenamiento_**.|
    |**Recurso**| **_El nombre del recurso de almacenamiento_**.|
    |**Rol** | **_Lector de datos de blobs de almacenamiento_**: permite el acceso de lectura a los contenedores de blobs y a los datos de Azure Storage.|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="Captura de pantalla: página Agregar asignación de roles en Azure Portal.":::

1. Después de recibir el mensaje de confirmación _Asignación de roles agregada_, actualice la página para ver la asignación de roles agregada.

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="Captura de pantalla: mensaje emergente de confirmación de asignación de roles agregada.":::

1. Si no ve el cambio de inmediato, espere e intente actualizar la página una vez más. Al asignar o quitar asignaciones de roles, los cambios pueden tardar hasta 30 minutos en aplicarse.

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="Captura de pantalla: ventana de asignaciones de roles de Azure.":::

 Eso es todo. Ha completado los pasos para habilitar una identidad administrada asignada por el sistema. Con esta credencial de identidad, puede conceder a Form Recognizer derechos de acceso específicos a documentos y archivos almacenados en su cuenta BYOS.

## <a name="learn-more-about--managed-identity"></a>Obtenga más información sobre la identidad administrada.

> [!div class="nextstepaction"]
> [Preguntas más frecuentes sobre identidades administradas para recursos de Azure: Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)