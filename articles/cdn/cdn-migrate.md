---
title: Migración de un perfil de Azure CDN de Verizon estándar a Verizon premium
description: Obtenga información sobre cómo migrar un perfil de Verizon estándar a Verizon premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: aa5f2e51549baeaea6373a2d24e54f780d508bce
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113596270"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrar un perfil de Azure CDN de Verizon estándar a Verizon premium

Cuando crea un perfil de Azure Content Delivery Network (CDN) para administrar sus puntos de conexión, Azure CDN le ofrece cuatro productos diferentes entre los que elegir. Para obtener información sobre los diferentes productos y sus características disponibles, consulte [Comparación de las características de los productos de Azure CDN](cdn-features.md).

Si ha creado un perfil **estándar de Azure CDN de Verizon** y lo está usando para administrar sus puntos de conexión de CDN, tiene la opción de actualizarlo a un perfil **premium de Azure CDN de Verizon**. Cuando lo actualice, se conservarán sus puntos de conexión de CDN y todos sus datos. 

> [!IMPORTANT]
> Una vez que haya actualizado a un perfil **premium de Azure CDN de Verizon**, no podrá volver a la versión **estándar de Azure CDN de Verizon**.
> 

Para actualizar un perfil **estándar de Azure CDN de Verizon**, póngase en contacto con el [soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparación de perfiles
Los perfiles **premium de Azure CDN de Verizon** tienen las siguientes diferencias clave, en comparación con los perfiles **estándar de Azure CDN de Verizon**:
- En determinadas características de Azure CDN, como la [compresión](cdn-improve-performance.md), las [reglas de almacenamiento en caché](cdn-caching-rules.md) y el [filtrado geográfico](cdn-restrict-access-by-country-region.md), no puede usar la interfaz de Azure CDN, si no que debe usar el portal de Verizon mediante el botón **Administrar**.
- API: a diferencia de Verizon estándar, no puede usar la API para controlar las características a las que se accede desde el portal de Verizon premium. Sin embargo, puede usar la API para controlar otras características comunes, como crear o eliminar un punto de conexión, purgar o cargar activos en la caché y habilitar o deshabilitar un dominio personalizado.
- Precio: en comparación con Verizon estándar, Verizon premium tiene una estructura de precios diferente para las transferencias de datos. Para más información, consulte [Precios de Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

Los perfiles **premium de Azure CDN de Verizon** tienen las siguientes características adicionales:
- [Autenticación de token](cdn-token-auth.md): permite a los usuarios obtener y usar un token para recuperar recursos seguros.
- [Motor de reglas](./cdn-verizon-premium-rules-engine.md): le permite personalizar cómo se administran las solicitudes HTTP.
- Herramientas de análisis avanzadas:
   - [Análisis HTTP detallado](cdn-advanced-http-reports.md)
   - [Análisis de rendimiento de Edge ](cdn-edge-performance.md)
   - [Análisis en tiempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el motor de reglas, consulte la [referencia del motor de reglas de Azure CDN](./cdn-verizon-premium-rules-engine-reference.md).