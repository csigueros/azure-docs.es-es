---
title: Implementación del servicio de red privada afirmada en Azure
description: Aprenda a implementar la solución Affirmed Private Network Service en Azure
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458459"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>Implementación del servicio de red privada afirmada en Azure

En este artículo se proporciona información general sobre el proceso de implementación de la solución Affirmed Private Network Service (APNS) en un dispositivo de Azure Stack Edge desde Microsoft Azure Marketplace.

En el diagrama siguiente se muestra la arquitectura del sistema de Affirmed Private Network Service, incluidos los recursos necesarios para la implementación.

![Implementación de Affirmed Private Network Service](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>Recopilación de la información necesaria

Para implementar APNS, debe tener los recursos siguientes:

- Un objeto de dispositivo de Azure Network Function Manager configurado que actúe como gemelo digital del dispositivo de Azure Stack Edge. 

- Una instancia de Azure Stack Edge totalmente implementada con una máquina virtual de NetFoundry. 

- Aprobación de la suscripción para la oferta de máquina virtual de Affirmed Management Systems y la aplicación administrada APNS. 

- Una cuenta de Azure con una suscripción activa y acceso a lo siguiente:  

    - Rol **Propietario** integrado para el grupo de recursos. 

    - Rol **Colaborador de aplicaciones administradas** integrado para la suscripción. 

    - Una red virtual y una subred a las que unirse (puertos abiertos tcp/443 y tcp/8443). 

    - 5 direcciones IP en la subred virtual. 

    - Un token de SAS válido proporcionado por Affirmed Release Engineering.  

    - Un nombre de usuario y una contraseña de administración para programar durante la implementación. 
    
## <a name="deploy-apns"></a>Implementación de APNS

Para implementar de forma automática la aplicación administrada APNS con todos los recursos y la información pertinente necesaria, seleccione la aplicación administrada APNS en Microsoft Azure Marketplace. Al implementar APNS, todos los recursos necesarios se crean de forma automática y se incluyen en un grupo de recursos administrado.

Complete el procedimiento siguiente para implementar APNS:
1.  Abra Azure Portal y seleccione **Crear un recurso**.
2.  Escriba *APNS* en la barra de búsqueda y presione Entrar.
3.  Seleccione **Ver ofertas privadas**. 
    > [!NOTE]
    > La aplicación administrada APNS no aparecerá hasta que seleccione **Ver ofertas privadas**.
4.  Seleccione **Crear** en el menú desplegable de la **oferta privada** y, después, seleccione la opción para implementar.
5.  Complete la configuración de la aplicación, la configuración de red y seleccione Revisar y crear.
6.  Seleccione **Implementar**.

## <a name="next-steps"></a>Pasos siguientes

- A fin de obtener instrucciones paso a paso para implementar APNS y configurar los valores de NetFoundry en un dispositivo de Azure Stack Edge, vea la [guía de implementación de Affirmed Private Network Service](https://go.microsoft.com/fwlink/?linkid=2165732).
- Para obtener información sobre el portal controlado por GUI mediante programación que los operadores usan para implementar, supervisar y administrar redes privadas de núcleos móviles, vea la [guía del usuario para administradores de Affirmed Private Network Service](https://go.microsoft.com/fwlink/?linkid=2165932).
- Obtenga más información sobre [Affirmed Private Network Service en Azure](affirmed-private-network-service-overview.md).
