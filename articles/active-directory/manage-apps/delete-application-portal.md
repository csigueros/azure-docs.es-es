---
title: 'Inicio rápido: Eliminación de una aplicación empresarial'
description: Eliminación de una aplicación empresarial en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 16bc055ed2b47ee5c212fa26387599a8d4ada7a8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058216"
---
# <a name="quickstart-delete-an-enterprise-application-in-azure-active-directory"></a>Inicio rápido: Eliminación de una aplicación empresarial en Azure Active Directory

En esta guía de inicio rápido, usará el centro de administración de Azure Active Directory para eliminar una aplicación que se agregó al inquilino de Azure Active Directory (Azure AD).

Para probar los pasos de este inicio rápido, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para la eliminación de una aplicación empresarial, necesitará:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Finalización de los pasos de [Inicio rápido: Adición de una aplicación empresarial](add-application-portal.md).

## <a name="delete-an-enterprise-application"></a>Eliminación de una aplicación empresarial

Para la eliminación de una aplicación empresarial:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD. Busque y seleccione la aplicación que desea eliminar. Por ejemplo, **Azure AD SAML Toolkit 1**.
1. En la sección **Administrar** del menú izquierdo, seleccione **Propiedades**.
1. En la parte superior del panel **Propiedades** seleccione **Eliminar** y, a continuación, seleccione **Sí** para confirmar que quiere eliminar la aplicación de su inquilino de Azure AD.

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="Eliminación de una aplicación empresarial":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con esta serie de inicios rápidos, considere la posibilidad de eliminar la aplicación para limpiar el inquilino de prueba. En este inicio rápido se ha indicado cómo eliminar la aplicación.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo planear una implementación de inicio de sesión único.
> [!div class="nextstepaction"]
> [Planeamiento de la implementación del inicio de sesión único](plan-sso-deployment.md)
