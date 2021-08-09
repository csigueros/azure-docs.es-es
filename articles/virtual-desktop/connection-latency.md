---
title: 'Latencia de la conexión para usuarios de Azure Virtual Desktop: Azure'
description: Latencia de la conexión para usuarios de Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ba4db9048154af2562d2c3b7431d2a75ba3c036f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747390"
---
# <a name="determine-user-connection-latency-in-azure-virtual-desktop"></a>Determinación de la latencia de la conexión de los usuarios en Azure Virtual Desktop

Azure Virtual Desktop está disponible a nivel mundial. Los administradores pueden crear máquinas virtuales (VM) en cualquier región de Azure que deseen. La latencia de la conexión variará en función de la ubicación de los usuarios y las máquinas virtuales. Los servicios de Azure Virtual Desktop se implementarán continuamente en geografías nuevas para mejorar la latencia.

La [herramienta Estimador de experiencia de Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) puede ayudarle a determinar la mejor ubicación para optimizar la latencia de las máquinas virtuales. Se recomienda usar la herramienta cada dos o tres meses para asegurarse de que la ubicación óptima no ha cambiado a medida que Azure Virtual Desktop se implementa en áreas nuevas.

## <a name="interpreting-results-from-the-azure-virtual-desktop-experience-estimator-tool"></a>Interpretación de los resultados de la herramienta Estimador de experiencia de Azure Virtual Desktop

En Azure Virtual Desktop, la latencia de hasta 150 ms no debería afectar a la experiencia del usuario que no implica representación o vídeo. Las latencias entre 150 ms y 200 ms deben ser adecuadas para el procesamiento de texto. La latencia por encima de 200 ms puede afectar a la experiencia del usuario. 

Además, la conexión de Azure Virtual Desktop depende de la conexión a Internet de la máquina desde la que el usuario está utilizando el servicio. Los usuarios pueden perder la conexión o experimentar un retraso en la entrada en una de las siguientes situaciones:

 - El usuario no tiene una conexión a Internet local estable y la latencia es superior a 200 ms.
 - La red está saturada o tiene la velocidad limitada.

Le recomendamos que elija ubicaciones de VM que estén lo más cerca posible de sus usuarios. Por ejemplo, si el usuario se encuentra en la India pero la máquina virtual está en Estados Unidos, se producirá una latencia que afectará a la experiencia global del usuario. 

## <a name="azure-front-door"></a>Azure Front Door

Azure Virtual Desktop usa [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) para redirigir la conexión de usuario a la puerta de enlace de Azure Virtual Desktop más cercana en función de la dirección IP de origen. Azure Virtual Desktop siempre usará la puerta de enlace de Azure Virtual Desktop que el cliente elija.

## <a name="next-steps"></a>Pasos siguientes

- Para comprobar cuál es la mejor ubicación de cara a una latencia óptima, consulte la [herramienta Estimador de experiencia de Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Para información sobre los planes de precios, consulte [Precios de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Para empezar a trabajar con la implementación de Azure Virtual Desktop, consulte [nuestro tutorial](./create-host-pools-azure-marketplace.md).