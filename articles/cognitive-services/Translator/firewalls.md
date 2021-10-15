---
title: 'Traducción detrás de firewalls: Translator'
titleSuffix: Azure Cognitive Services
description: Translator de Azure Cognitive Services puede traducir detrás de los firewalls con el filtrado de nombres de dominio o de direcciones IP.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401871"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Traducción detrás de firewalls de direcciones IP con Translator

Translator puede traducir detrás de los firewalls utilizando el filtrado de nombres de dominio o de direcciones IP. El filtrado de nombres de dominio es el método preferido. Si sigue necesitando el filtrado de IP, se recomienda obtener los [detalles de las direcciones IP mediante la etiqueta de servicio](../../virtual-network/service-tags-overview.md#service-tags-on-premises). Translator está bajo la etiqueta de servicio "CognitiveServicesManagement". 

**No se recomienda** ejecutar Microsoft Translator detrás de un firewall con filtrado de direcciones IP específicas. Es probable que la configuración se interrumpa en el futuro sin previo aviso.

Las direcciones IP de los puntos de conexión geográficos de Translator el 21 de septiembre de 2021 son:

|Geography|URL base (punto de conexión geográfico)|Direcciones IP|
|:--|:--|:--|
|Estados Unidos|api-nam.cognitive.microsofttranslator.com|20.42.6.144, 20.49.96.128, 40.80.190.224, 40.64.128.192|
|Europa|api-eur.cognitive.microsofttranslator.com|20.50.1.16, 20.38.87.129|
|Asia Pacífico|api-apc.cognitive.microsofttranslator.com|40.80.170.160, 20.43.132.96, 20.37.196.160, 20.43.66.16|
