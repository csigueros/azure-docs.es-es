---
title: Configuración de un nombre de dominio personalizado para su puerta de enlace autohospedada de API Management | Microsoft Docs
description: Este tema describe los pasos para configurar un nombre de dominio personalizado para la puerta de enlace autohospedada de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: danlep
ms.openlocfilehash: b2ddd4c4486930cd7e5327068a5fe3827ac0581a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667642"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Configuración de un nombre de dominio personalizado para una puerta de enlace autohospedada

Al aprovisionar una [puerta de enlace de Azure API Management autohospedada](self-hosted-gateway-overview.md) no se le asigna un nombre de host y se debe hacer referencia a ella por su dirección IP. En este artículo se muestra cómo asignar un nombre DNS personalizado existente (también conocido como nombre de host) a una puerta de enlace autohospedada.

## <a name="prerequisites"></a>Prerrequisitos

Para seguir los pasos que se describen en este artículo, debe tener:

-   Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Una instancia de API Management Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
- Una puerta de enlace autohospedada. Para obtener más información, consulte [cómo aprovisionar una puerta de enlace autohospedada](api-management-howto-provision-self-hosted-gateway.md)
-   Nombre de dominio personalizado que propiedad de su organización o suyo. En este tema no se dan instrucciones para adquirir un nombre de dominio personalizado.
-   Un registro DNS hospedado en un servidor DNS que asigna el nombre de dominio personalizado a la dirección IP de la puerta de enlace autohospedada. En este tema no se dan instrucciones para hospedar un registro DNS.
-   Debe tener un certificado válido con una clave pública y privada (. PFX). El firmante o el nombre alternativo del firmante (SAN) debe coincidir con el nombre de dominio (esto permite que la instancia de API Management exponga de forma segura direcciones URL sobre TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Incorporación de un certificado de dominio personalizado a su servicio de API Management

Agregue un certificado de dominio personalizado (.PFX) a una instancia de API Management o haga referencia a un certificado almacenado en Azure Key Vault. Siga los pasos que se proporcionan en el artículo [Protección de servicios back-end con la autenticación de certificados de cliente en Azure API Management](api-management-howto-mutual-certificates.md).

> [!NOTE]
> Se recomienda usar un certificado del almacén de claves para el dominio de la puerta de enlace autohospedada.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Uso de Azure Portal para configurar un nombre de dominio personalizado para la puerta de enlace autohospedada

1. Seleccione las **Puertas de enlace** en **Implementación e infraestructura**.
2. Seleccione la puerta de enlace autohospedada para la que desea configurar el nombre de dominio.
3. Seleccione **nombres de host** en **Configuración**.
4. Seleccione **+ Agregar**.
5. Escriba el nombre de recurso del nombre de host en el campo de **Nombre**.
6. Escriba el nombre de dominio en el campo de **Nombre de host**.
7. Seleccione un certificado del menú desplegable **Certificado**.
8. Seleccione la casilla **Negociar certificado de cliente** si alguna de las API expuestas a través de esta puerta de enlace usa la autenticación de certificados de clientes.
    > [!WARNING]
    > Esta configuración se comparte con todos los nombres de dominio configurados para la puerta de enlace.
9. Seleccione **Agregar** para asignar el nombre de dominio personalizado a la puerta de enlace autohospedada seleccionada.

## <a name="next-steps"></a>Pasos siguientes

[Actualización y escalado del servicio](upgrade-and-scale.md)
