---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ef4edb99ab2d6d4a25f2151c5b63c1c31ff91ef4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724953"
---
* Tiene una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Tiene una red virtual a la que quiere conectarse. 

   * Compruebe que ninguna de las subredes de sus redes locales se superpone a las redes virtuales a las que quiere conectarse. 
   * Para crear una red virtual en Azure Portal, consulte el artículo [Inicio rápido](../articles/virtual-network/quick-create-portal.md).

* Su red virtual no debe tener ninguna puerta de enlace de red virtual. 

   * Si la red virtual ya tiene puertas de enlace (VPN o ExpressRoute), debe quitarlas todas antes de continuar. 
   * Esta configuración requiere que las redes virtuales se conecten solo a la puerta de enlace del centro de Virtual WAN.

* Un centro de conectividad virtual es una red virtual que Virtual WAN crea y usa. Es el núcleo de la red Virtual WAN en una región. 

   * Obtenga un intervalo de direcciones IP para la región del centro virtual. 
   * El intervalo de direcciones que especifique para el centro no se puede superponer con ninguna de las redes virtuales existentes a las que se conecta. 
   * El intervalo de direcciones no se puede superponer con los intervalos de direcciones locales a las que se conecta. 
   * Si no está familiarizado con los intervalos de direcciones IP ubicados en la configuración de la red local, póngase de acuerdo con alguien que pueda proporcionarle estos detalles.
