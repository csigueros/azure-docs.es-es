---
title: 'HSM administrado de Azure Key Vault: soluciones de terceros | Microsoft Docs'
description: Obtenga información sobre soluciones de terceros integradas con Managed HSM.
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443475"
---
# <a name="third-party-solutions"></a>Soluciones de terceros

HSM administrado de Azure Key Vault es un servicio en la nube que cumple los estándares totalmente administrado, de alta disponibilidad y de un solo inquilino que le permite proteger las claves criptográficas de las aplicaciones en la nube mediante HSM validados de **FIPS 140-2 de nivel 3**. [Más información](overview.md).

Varios proveedores han trabajado estrechamente con Microsoft para integrar sus soluciones con HSM administrado. En la tabla siguiente se enumeran estas soluciones con una breve descripción (proporcionada por el proveedor). También se proporcionan vínculos a sus ofertas de Azure Marketplace y documentación.


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>Soluciones de terceros integradas con HSM administrado

| Nombre del proveedor | Descripción de la solución |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|SSL sin clave de Cloudflare permite que los sitios web usen el servicio SSL de Cloudflare mientras mantienen la custodia de sus claves privadas en HSM administrado. Este servicio, junto con HSM administrado, ayuda a un alto nivel de protección al proteger las claves privadas, realizar operaciones de firma y cifrado internamente, proporcionar controles de acceso y almacenar claves en un HSM de nivel 3 resistente a manipulaciones según el estándar FIPS 140-2. <br>[Documentación](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[Tecnologías de comunicación de NewNet](https://newnet.com/)|Secure Transaction Cloud (STC) de NewNet es una primera solución de enrutamiento, conmutación y transporte de pago seguro basada en la nube, mejorada con HSM virtualizado basado en la nube, que maneja pagos móviles, web y en la tienda. STC permite la transformación en la nube para las entidades de pago e implementación rápida para proveedores de pago desde cero.<br/>[Oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[Documentación](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|EJBCA Enterprise, la infraestructura de clave pública (PKI) más usada del mundo, proporciona los servicios de seguridad básicos para identidades de confianza y comunicación segura para cualquier caso de uso. Una única instancia de EJBCA Enterprise admite varias CA y niveles para permitirle crear infraestructuras completas para varios casos de uso.<br>[Oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[Documentación](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>Pasos siguientes
* [Información general sobre HSM administrado](overview.md)
* [Procedimientos recomendados de HSM administrado](best-practices.md)

