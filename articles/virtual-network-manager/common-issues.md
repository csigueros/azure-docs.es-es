---
title: Problemas comunes con Azure Virtual Network Manager (versión preliminar)
description: Obtenga información sobre los problemas comunes observados al usar Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bcf66883ba766189215fd4ce267391c9358b10f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090837"
---
# <a name="common-issues-seen-with-azure-virtual-network-manager-preview"></a>Problemas comunes con Azure Virtual Network Manager (versión preliminar)

En este artículo, se tratan los problemas comunes que pueden surgir al usar Azure Virtual Network Manager y se proporcionarán algunas soluciones posibles.

## <a name="why-isnt-my-configuration-getting-applied"></a>¿Por qué no se aplica mi configuración? 

**Motivos comunes por los que no se aplican las configuraciones:** 

* La configuración no se está implementando en las regiones en las que se encuentran las redes virtuales. 

* Aún no ha implementado la configuración. Deberá implementar la configuración para que surta efecto. 

* La configuración no tuvo tiempo suficiente para surtir efecto. Después de confirmar la configuración, esta tarda aproximadamente entre 15 y 20 minutos en aplicarse. Cuando hay una actualización de la pertenencia a su grupo de red, los cambios pueden tardan unos 10 minutos en reflejarse. 

## <a name="i-updated-my-configuration-but-the-changes-arent-reflected-in-my-environment"></a>He actualizado mi configuración, pero los cambios no se reflejan en mi entorno. 

Debe implementar la nueva configuración después de modificarla. 

## <a name="why-is-my-connectivity-configuration-not-working"></a>¿Por qué no funciona mi configuración de conectividad? 

**Debe tener en cuenta lo siguiente:** 

* En una topología en estrella tipo hub-and-spoke, si habilita la opción para *usar el centro de conectividad como puerta de enlace*, deberá tener una puerta de enlace en la red virtual del centro de conectividad. De lo contrario, se producirá un error en la creación del emparejamiento de red virtual entre el centro de conectividad y las redes virtuales de radio. 

* Si quiere que los miembros del grupo de red se comuniquen entre sí entre regiones en una configuración de topología en estrella tipo hub-and-spoke, debe habilitar la opción de malla global. 

## <a name="next-steps"></a>Pasos siguientes

Consulte [Preguntas más frecuentes sobre Azure Virtual Network Manager](faq.md) para conocer las respuestas a las preguntas más frecuentes.
