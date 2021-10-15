---
title: Búsqueda del id. de inquilino, el id. de objeto y los detalles de la asociación de asociados en Azure Marketplace
description: Cómo buscar el id. de inquilino, el id. de objeto y los detalles de la asociación de asociados de un id. de suscripción en Azure Marketplace.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 92819c28002c79ff905174e5b2f94779050237fd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356710"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Búsqueda del id. de inquilino, el id. de objeto y los detalles de la asociación de asociados

En este artículo se describe cómo buscar el id. de inquilino, el id. de objeto y los detalles de la asociación de asociados, junto con sus respectivos id. de suscripción.

Si necesita obtener capturas de pantallas de estos elementos en Azure Cloud Shell para usarlos con el fin de obtener ayuda para la depuración, vaya a [Búsqueda del inquilino, el objeto y la asociación de id. de asociados para la depuración](#find-ids-for-debugging).

>[!Note]
> Solo el propietario de una suscripción tiene los privilegios necesarios para realizar estos pasos.

## <a name="find-tenant-id"></a>Búsqueda del id. de inquilino

1. Vaya a [Azure Portal](https://ms.portal.azure.com/).
2. Seleccione **Azure Active Directory**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Icono de Azure Active Directory en Azure Portal.":::

3. Seleccione **Información general**. El identificador de inquilino debe aparecer en **Información básica**.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Seleccione los grupos en Azure Portal.":::

## <a name="find-subscriptions-and-roles"></a>Búsqueda de suscripciones y roles

1. Vaya a Azure Portal y seleccione **Azure Active Directory**, tal como se indicó en los pasos 1 y 2 anteriores.
2. Seleccione **Suscripciones**.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Icono de suscripciones en Azure Portal.":::

3. Consulte las suscripciones y los roles.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Pantalla de suscripciones en Azure Portal.":::

## <a name="find-partner-id"></a>Búsqueda del id. de asociado

1. Vaya a la página de suscripciones tal y como se describe en la sección anterior.
2. Seleccione una suscripción.
3. En **Facturación**, seleccione **Información sobre el asociado**.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Información del asociado en el menú de navegación izquierdo.":::

## <a name="find-user-object-id"></a>Búsqueda del usuario (id. de objeto)

1. Inicie sesión en el [portal de administración de Office 365](https://portal.office.com/adminportal/home) con una cuenta en el inquilino que quiera, mediante los derechos administrativos adecuados.
2. En el menú de la izquierda, expanda la sección **Centros de administración** que está en la parte inferior y, a continuación, seleccione la opción Azure Active Directory para iniciar la consola de administración en una nueva ventana del explorador.
3. Seleccione **Usuarios**.
4. Busque el usuario que quiera y seleccione el nombre de la cuenta para ver la información de perfil de la cuenta de ese usuario.
5. El id. de objeto se encuentra en la sección de identidad de la derecha.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Centro de administración de Azure Active Directory.":::

6. Para buscar las **asignaciones de roles**, seleccione el **Control de acceso (IAM)** en el menú de la izquierda y, a continuación, **Asignación de roles**.

    :::image type="content" source="../role-based-access-control/media/role-assignments-portal/rg-role-assignments.png" alt-text="Asignaciones de roles para los recursos de Azure.":::


## <a name="find-ids-for-debugging"></a>Búsqueda de id. para la depuración

En esta sección se describe cómo buscar el inquilino, el objeto y la asociación de id. de asociados con fines de depuración.

1. Vaya a [Azure Portal](https://ms.portal.azure.com/).
2. Abra Azure Cloud Shell seleccionando el icono de PowerShell que está en la parte superior derecha.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="Icono de PowerShell en la parte superior derecha de la pantalla.":::

3. Seleccione **PowerShell**.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Seleccione el vínculo de PowerShell.":::

4. Seleccione el cuadro de **Suscripción** para elegir a qué vínculo estará relacionado el asociado y, a continuación, seleccione **Crear almacenamiento**. Esta es una acción única; si ya tiene configurado el almacenamiento, vaya al siguiente paso.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Seleccione el botón Crear almacenamiento.":::

5. Al crear el almacenamiento (o si ya lo tiene), se abre la ventana de Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Ventana de Azure Cloud Shell.":::

6. Para obtener información sobre la asociación de asociados, instale la extensión con este comando:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell analizará si la extensión ya está instalada:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="Ventana de Azure Cloud Shell que muestra que la extensión ya está instalada.":::

7. Compruebe los detalles del asociado mediante este comando:<br>`az managementpartner show --partner-id xxxxxx`<br>Ejemplo: `az managementpartner show --partner-id 4760962`.<br>Ajuste una captura de pantalla de los resultados del comando, que tendrá un aspecto similar al siguiente:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Pantalla de ejemplo que muestra los resultados del comando anterior para ver el id. del asociado.":::

>[!NOTE]
>Si debe realizar una captura de pantalla de varias suscripciones, use este comando para intercambiarlas:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Pasos siguientes

- [Países y regiones admitidos](sell-from-countries.md)