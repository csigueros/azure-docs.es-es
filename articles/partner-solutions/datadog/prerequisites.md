---
title: 'Requisitos previos para Datadog en Azure: soluciones para asociados de Azure'
description: En este artículo se describe cómo configurar un entorno de Azure para crear una instancia de Datadog.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 637d3cfb3baf8a53cb62943419077fad0f70d33d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110657087"
---
# <a name="configure-environment-before-datadog-deployment"></a>Configuración del entorno antes de la implementación de Datadog

En este artículo se describe cómo configurar un entorno antes de implementar la primera instancia de Datadog. Estas condiciones son requisitos previos para completar los inicios rápidos.

## <a name="access-control"></a>Control de acceso

Para configurar la integración de Azure Datadog, debe tener acceso de **propietario** en la suscripción de Azure. [Confirme que tiene el acceso adecuado](../../role-based-access-control/check-access.md) antes de iniciar el programa de instalación.

## <a name="add-enterprise-application"></a>Incorporación de una aplicación empresarial
 
Para usar la característica de inicio de sesión único de Lenguaje de marcado de aserción de seguridad dentro del recurso Datadog, debe configurar una aplicación empresarial. Para agregar una aplicación empresarial, se necesita uno de estos roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o propietario de la entidad de servicio.

Siga estos pasos para configurar la aplicación empresarial:

1. Vaya al [Portal de Azure](https://portal.azure.com). Seleccione **Azure Active Directory**.
1. En el panel izquierdo, seleccione **Aplicaciones empresariales**.
1. Select **Nueva aplicación**.
1. En **Agregar desde la galería**, busque *Datadog*. Seleccione el resultado de la búsqueda y, después, seleccione **Agregar**.

   :::image type="content" source="media/prerequisites/datadog-azure-ad-app-gallery.png" alt-text="Aplicación Datadog en la galería empresarial de AAD." border="true":::

1. Una vez creada la aplicación, vaya a las propiedades del panel lateral. En **¿Asignación de usuarios?** , seleccione **No** y, después, **Guardar**.

   :::image type="content" source="media/prerequisites/user-assignment-required.png" alt-text="Establecer las propiedades de la aplicación Datadog" border="true":::

1. Vaya a **Inicio de sesión único** en el panel lateral. Luego, seleccione **SAML**.

   :::image type="content" source="media/prerequisites/saml-sso.png" alt-text="Autenticación SAML" border="true":::.

1. Seleccione **Sí** cuando se le pregunte si desea **guardar la configuración de inicio de sesión único**.

   :::image type="content" source="media/prerequisites/save-sso.png" alt-text="Guardar el inicio de sesión único para la aplicación Datadog" border="true":::

1. La configuración del inicio de sesión único ahora está completa.

## <a name="next-steps"></a>Pasos siguientes

Para crear una instancia de Datadog, consulte [Inicio rápido: Introducción a Datadog](create.md).
