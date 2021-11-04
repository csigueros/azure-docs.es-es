---
title: Entrega de contenido en China con Azure CDN | Microsoft Docs
description: Obtenga información acerca del uso de Azure Content Delivery Network (CDN) para entregar contenido a los usuarios de China.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: 9ff41255b5e06684cbaff2aa87e1adf4429af42d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439355"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Entrega de contenido en China con Azure CDN

La red global Azure Content Delivery Network (CDN) puede servir contenido a los usuarios de China con ubicaciones de punto de presencia (POP) cerca de China o cualquier POP que proporcione mejor rendimiento para las solicitudes procedentes de China. Sin embargo, si China es un mercado significativo para los clientes y necesitan un rendimiento rápido, considere la posibilidad de usar Azure CDN China en su lugar.

La red Azure CDN China difiere de la red CDN de Azure global en que ofrece el contenido desde POP situados dentro de China mediante la asociación con un número de proveedores locales. Debido a las regulaciones y normas de cumplimiento chinas, debe registrar una suscripción independiente para usar Azure CDN China y los sitios web deben tener una licencia ICP. La experiencia del portal y la API para habilitar y administrar el contenido de entrega es idéntica entre la red CDN de Azure global y Azure CDN China.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Comparación de la red CDN de Azure global y Azure CDN China

La red CDN de Azure global y Azure CDN China tienen las siguientes características:

- Azure CDN global:

     - Portal: https://portal.azure.com  

     - Realiza la entrega de contenido fuera de China

     - Cuatro planes de tarifa: Microsoft Estándar, Verizon Estándar, Verizon Premium y Akamai Estándar

     - [Documentación](./index.yml)

- Azure CDN China:

     - Portal: https://portal.azure.cn

     - Realiza la entrega de contenido dentro de China

     - Dos planes de tarifa: Estándar y Premium

     - [Documentación](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure CDN China, consulte:

- [Características de Azure Content Delivery Network](https://www.azure.cn/en-us/home/features/cdn/)

- [Introducción a Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Uso de Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Disponibilidad del servicio de Azure en China](/azure/china/concepts-service-availability)