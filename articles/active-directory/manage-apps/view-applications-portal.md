---
title: 'Inicio rápido: Visualización de aplicaciones empresariales'
description: Vea las aplicaciones empresariales que están registradas para usar su inquilino de Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: 0f744176e3a1472cadd740f94e34839bb7a8a09d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613726"
---
# <a name="quickstart-view-enterprise-applications-in-azure-active-directory"></a>Inicio rápido: Visualización de aplicaciones empresariales en Azure Active Directory

En este inicio rápido, aprenderá a usar el centro de administración de Azure Active Directory para buscar y ver las aplicaciones empresariales que ya están configuradas en el inquilino de Azure Active Directory (Azure AD).

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para ver las aplicaciones que se han registrado en el inquilino de Azure AD, necesita:

- Una cuenta de Azure. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Finalización de los pasos de [Inicio rápido: Adición de una aplicación empresarial](add-application-portal.md).

## <a name="view-a-list-of-applications"></a>Visualización de una lista de aplicaciones

Para ver las aplicaciones empresariales registradas en el inquilino:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD.

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="Vea las aplicaciones registradas en el inquilino de Azure AD.":::

1. Para ver más aplicaciones, seleccione **Cargar más** en la parte inferior de la lista. Si hay muchas aplicaciones en el inquilino, puede resultar más fácil buscar una aplicación específica en lugar de desplazarse por la lista.

## <a name="search-for-an-application"></a>Búsqueda de una aplicación

Para buscar una aplicación concreta:

1. En el menú **Tipo de aplicación**, seleccione **Todas las aplicaciones** y elija **Aplicar**.
1. Escriba el nombre de la aplicación que desea buscar. Si la aplicación se ha agregado al inquilino de Azure AD, aparece en los resultados de la búsqueda. Por ejemplo, puede buscar la aplicación **Azure AD SAML Toolkit 1** que se usa en los inicios rápidos anteriores. 
1. Pruebe a escribir las primeras letras del nombre de una aplicación.

## <a name="select-viewing-options"></a>Selección de las opciones de visualización

Seleccione las opciones en función de lo que busque:

1. Puede ver las aplicaciones por **tipo de aplicación**, **estado de aplicación** y **visibilidad de aplicación**.
1. En **Tipo de aplicación**, elija una de estas opciones:
    - **Aplicaciones empresariales** muestra las aplicaciones que no son de Microsoft.
    - **Aplicaciones de Microsoft** muestra las aplicaciones de Microsoft.
    - **Todas las aplicaciones** muestra las aplicaciones de Microsoft y las que no son de Microsoft.
1. En **Estado de la aplicación**, elija **Cualquiera**, **Deshabilitada** o **Habilitada**. La opción **Cualquiera** incluye tanto aplicaciones habilitadas como deshabilitadas.
1. En **Visibilidad de la aplicación**, elija **Cualquiera** u **Oculta**. La opción **Ocultas** muestra las aplicaciones que están en el inquilino, pero que los usuarios no pueden ver.
1. Después de elegir las opciones que desee, seleccione **Aplicar**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha creado una aplicación de prueba denominada **Azure AD SAML Toolkit 1** que ha utilizado en los inicios rápidos, puede considerar la posibilidad de eliminarla ahora para limpiar el inquilino. Para más información, consulte [Eliminación de una aplicación](delete-application-portal.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a eliminar una aplicación empresarial.
> [!div class="nextstepaction"]
> [Eliminar una aplicación](add-application-portal.md)
