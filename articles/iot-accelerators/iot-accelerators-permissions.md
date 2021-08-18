---
title: 'Uso del sitio de soluciones de Azure IoT: Azure | Microsoft Docs'
description: Describe cómo usar el sitio web de AzureIoTSolutions.com para implementar el acelerador de soluciones.
author: dominicbetts
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 3251adb72adfb53209878fcfc1450518b9bc9f37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743339"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Uso del sitio azureiotsolutions.com para implementar el acelerador de soluciones

Puede implementar los aceleradores de soluciones de Azure IoT en su suscripción a Azure desde [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com aloja tanto el código abierto de Microsoft como los aceleradores de soluciones de asociados. Estos aceleradores de soluciones se alinean con la [Arquitectura de referencia de Azure IoT](/azure/architecture/reference-architectures/iot). Puede usar el sitio para implementar rápidamente un acelerador de soluciones como entorno de demostración o de producción.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="Página principal de soluciones de IoT":::

> [!TIP]
> Si necesita más control sobre el proceso de implementación, puede usar la CLI para implementar un acelerador de soluciones.

Puede implementar los aceleradores de soluciones en las siguientes configuraciones:

* **Estándar**: implementación ampliada de la infraestructura para desarrollar una implementación de producción. Azure Container Service implementa los microservicios en varias máquinas virtuales de Azure. Kubernetes orquesta los contenedores de Docker que hospedan los microservicios individuales.
* **Básico**: versión de menor costo para ver una demostración o probar una implementación. Todos los microservicios se implementan en una máquina virtual de Azure.
* **Local**: implementación de la máquina local para desarrollo y pruebas. Este enfoque implementa los microservicios en un contenedor Docker local y se conecta a IoT Hub, Azure Cosmos DB y los servicios de almacenamiento de Azure en la nube.

Cada uno de los aceleradores de soluciones utiliza una combinación diferente de servicios de Azure, como IoT Hub, Azure Stream Analytics y Cosmos DB. Para obtener más información, visite [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) y seleccione un acelerador de soluciones.

## <a name="sign-in-at-azureiotsolutionscom"></a>Inicie sesión en azureiotsolutions.com

Antes de poder implementar un acelerador de soluciones, debe iniciar sesión en AzureIoTSolutions.com con las credenciales asociadas a una suscripción a Azure. Si la cuenta está asociada con más de un inquilino de Microsoft Azure Active Directory (AD), puede usar la **lista desplegable para la selección de cuenta** para elegir el directorio que se usará.

Los permisos para implementar aceleradores de soluciones, administrar usuarios y administrar servicios de Azure dependen de su rol en el directorio seleccionado. Los roles de Azure AD comunes asociados con los aceleradores de soluciones incluyen:

* **Administrador global**: Puede haber muchos [administradores globales](../active-directory/roles/permissions-reference.md) por inquilino de Azure AD:

  * Al crear un inquilino de Azure AD, quien lo crea es de manera predeterminada el administrador global del mismo.
  * El administrador global puede implementar aceleradores de soluciones básicos y estándar.

* **Usuario de dominio**: Puede haber muchos usuarios de dominio por inquilino de Azure AD. Un usuario de dominio puede implementar un acelerador de soluciones básico.

* **Usuario invitado**: Puede haber muchos usuarios invitados por inquilino de Azure AD. Los usuarios invitados no pueden implementar un acelerador de soluciones en el inquilino de Azure AD.

Para más información acerca de los usuarios y roles de Azure AD, consulte estos recursos:

* [Creación de usuarios en Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Asignación de usuarios a aplicaciones](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Elección del dispositivo

El sitio AzureIoTSolutions.com vincula al [catálogo de dispositivos Azure Certified for IoT](https://devicecatalog.azure.com/).

El catálogo enumera los cientos de dispositivos de hardware IoT certificados que puede conectar a los aceleradores de soluciones para comenzar a crear la solución de IoT.

![Catálogo de dispositivos](media/iot-accelerators-permissions/devicecatalog.png)

Si es un fabricante de hardware, haga clic en **Convertirse en partner** para obtener información sobre la asociación con Microsoft en el programa Certified for IoT.

## <a name="next-steps"></a>Pasos siguientes

Para probar alguno de los aceleradores de soluciones de IoT, vea el siguiente inicio rápido: [Prueba de una solución de fábrica conectada](quickstart-connected-factory-deploy.md).
