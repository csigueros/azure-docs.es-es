---
title: 'Controladores de límites de sesión certificados para enrutamiento directo de Azure: Azure Communication Services'
description: Lista de controladores de límites de sesión certificados para enrutamiento directo de Azure Communication Services y limitaciones conocidas
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b2468b13f7093129c137019d8ec7f60e4fd5e4f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258008"
---
# <a name="list-of-session-border-controllers-certified-for-azure-communication-services-direct-routing"></a>Lista de controladores de límites de sesión certificados para enrutamiento directo de Azure Communication Services
Este documento contiene una lista de controladores de límites de sesión certificados para enrutamiento directo de Azure Communication Services. También incluye limitaciones conocidas.

Microsoft está trabajando con los proveedores de controladores de límites de sesión (SBC) seleccionados certificados para que Enrutamiento directo de Teams funcione con el enrutamiento directo de Azure. Puede ver el progreso en esta página. Aunque SBC, certificado para Enrutamiento directo de Teams, puede funcionar con el enrutamiento directo de Azure, aconsejamos no poner ninguna carga de trabajo en SBC hasta que aparezca en esta página. Tampoco se admite SBC sin certificar. Aunque el enrutamiento directo de Azure se basa en el mismo back-end que Enrutamiento directo de Teams, hay algunas diferencias. La certificación abarca la validación completa de SBC para el enrutamiento directo de Azure.

Microsoft trabaja con cada proveedor para:
- Trabajar conjuntamente en los protocolos de interconexión SIP.
- Realizar pruebas intensas mediante un laboratorio de terceros. Solo los dispositivos que pasan las pruebas están certificados.
- Ejecutar pruebas diarias con todos los dispositivos certificados en entornos de producción y preproducción. La validación de los dispositivos en entornos de preproducción garantiza que las nuevas versiones del código de Azure Communication Services código en la nube funcionarán con SBC certificados.
- Establezca un proceso de soporte técnico conjunto con los proveedores de SBC.

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

La omisión de elementos multimedia aún no es compatible con Azure Communication Services. Los elementos multimedia iniciales no son compatibles con un cliente basado en web.
En la tabla siguiente se muestran dispositivos certificados para enrutamiento directo de Azure Communication Services.

Si tiene alguna pregunta sobre el programa de certificación SBC para enrutamiento directo de Communication Services, póngase en contacto con acsdrcertification@microsoft.com.

## <a name="certified-sbc-vendors"></a>Proveedores de SBC certificados

|Vendor|Producto|Versión del software|
|:--- |:--- |:--- 
|AudioCodes|Mediant SBC|7.40A
|Metaswitch|Perimeta SBC|4,9|

Tenga en cuenta la certificación que se concede a una versión principal. Esto significa que se admite el firmware con cualquier número del firmware SBC que sigue a la versión principal.

## <a name="next-steps"></a>Pasos siguientes

### <a name="conceptual-documentation"></a>Documentación conceptual

- [Tipos de número de teléfono en Azure Communication Services](./plan-solution.md)
- [Planeamiento del enrutamiento directo de Azure](./direct-routing-infrastructure.md)
- [Emparejamiento del controlador de límite de sesión y configuración del enrutamiento de voz](./direct-routing-provisioning.md)
- [Precios](../pricing.md)

### <a name="quickstarts"></a>Guías de inicio rápido

- [Llamada a teléfono](../../quickstarts/voice-video-calling/pstn-call.md)
