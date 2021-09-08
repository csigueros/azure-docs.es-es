---
title: Visualización de detalles de autenticación de Microsoft Azure Maps
description: Use Azure Portal para ver detalles de autenticación de Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802665"
---
Para ver los detalles de autenticación de la cuenta de Azure Maps en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Vaya al menú de Azure Portal. Seleccione **Todos los recursos** y, a continuación, seleccione la cuenta de Azure Maps.

3. En **Configuración** en el panel izquierdo, seleccione **Autenticación**.

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Detalles de la autenticación.":::

Se crean tres valores cuando se crea la cuenta de Azure Maps. Se usan para admitir dos tipos de autenticación en Azure Maps:
- **Autenticación de Azure Active Directory**: `Client ID` representa la cuenta que se va a usar para las solicitudes de API REST. El valor de `Client ID` debe almacenarse en la configuración de la aplicación y recuperarse antes de realizar solicitudes HTTP de Azure Maps que usan la autenticación de Azure AD.
- **Autenticación de clave compartida**: `Primary Key` y `Secondary Key` se usan como clave de suscripción para la autenticación de clave compartida. La autenticación de clave compartida se basa en pasar la clave generada por la cuenta de Azure Maps con cada solicitud a Azure Maps. Se recomienda volver a generar periódicamente las claves. Para mantener las conexiones actuales durante la regeneración, se proporcionan dos claves. Se puede usar una clave mientras se regenera la otra. Cuando regenere las claves, debe actualizar todas las aplicaciones que acceden a esta cuenta para usar las nuevas claves. Para obtener más información, consulte [Autenticación con Azure Maps](../azure-maps-authentication.md).
