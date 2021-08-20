---
title: Azure Bastion | Microsoft Docs
description: Obtenga información sobre Azure Bastion, que proporciona conectividad RDP/SSH segura y directa con sus máquinas virtuales sin exponer los puertos RDP/SSH externamente.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 07/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal
ms.openlocfilehash: f23a21bdba5602dad2e38ee931a5e754f0607cde
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730600"
---
# <a name="what-is-azure-bastion"></a>¿Qué es Azure Bastion?

Azure Bastion es un servicio que se implementa que le permite conectarse a una máquina virtual mediante el explorador y Azure Portal. Azure Bastion es un nuevo servicio PaaS totalmente administrado por la plataforma que se aprovisiona en las redes virtuales. Proporciona una conectividad RDP/SSH segura e ininterrumpida a las máquinas virtuales directamente desde Azure Portal a través de TLS. Cuando se conecta a través de Azure Bastion, las máquinas virtuales no necesitan una dirección IP pública, un agente o software cliente especial.

Bastion proporciona conectividad segura de RDP y SSH a todas las máquinas virtuales de la red virtual en la que se aprovisiona. El uso de Azure Bastion protege las máquinas virtuales frente a la exposición de los puertos de RDP/SSH al mundo exterior, al tiempo que ofrece acceso seguro mediante RDP/SSH.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagrama que muestra la arquitectura de Azure Bastion.":::

## <a name="key-benefits"></a><a name="key"></a>Ventajas principales

* **RDP y SSH directamente en Azure Portal:** puede ir directamente a la sesión RDP y SSH en Azure Portal con un solo clic y sin problemas.
* **Sesión remota a través de TLS y recorrido del firewall para RDP/SSH:** Azure Bastion usa un cliente web basado en HTML5 que se transmite automáticamente al dispositivo local. Obtiene la sesión RDP/SSH a través de TLS en el puerto 443, lo que le permite recorrer firewalls corporativos de forma segura.
* **No se requiere ninguna dirección IP pública en la máquina virtual de Azure:** Azure Bastion abre la conexión RDP/SSH a la máquina virtual de Azure con la dirección IP privada en la máquina virtual. No necesita una dirección IP pública en su máquina virtual.
* **No hay problemas de administración de los NSG:** Azure Bastion es un servicio PaaS de Azure de plataforma totalmente administrada que se refuerza internamente para proporcionar una conexión RDP/SSH segura. No es necesario que aplique ningún NSG en la subred de Azure Bastion. Dado que Azure Bastion se conecta a las máquinas virtuales a través de la dirección IP privada, puede configurar los NSG para permitir RDP o SSH solo desde Azure Bastion. De este modo se evita tener que administrar los NSG cada vez que necesite conectarse de forma segura a las máquinas virtuales.
* **Protección contra la exploración de puertos:** ya que no es necesario exponer las máquinas virtuales a Internet pública, las máquinas virtuales están protegidas contra la exploración de puertos por parte de usuarios malintencionados o no autorizados que se encuentran fuera de la red virtual.
* **Protección frente a explotaciones de día cero. Protección en un solo lugar:** Azure Bastion es un servicio PaaS totalmente administrado de plataforma. Dado que se encuentra en el perímetro de la red virtual, no es necesario preocuparse por proteger cada una de las máquinas virtuales de la red virtual. La plataforma de Azure protege contra ataques de día cero manteniendo automáticamente el servicio Azure Bastion protegido y siempre actualizado.

## <a name="skus"></a><a name="sku"></a>SKU

Azure Bastion tiene dos SKU disponibles, básica y estándar. La SKU estándar se encuentra actualmente en versión preliminar. Para obtener más información, incluyendo cómo actualizar una SKU, consulte el artículo [Parámetros de configuración](configuration-settings.md#skus). 

En la tabla siguiente se muestran las características y las SKU correspondientes.

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>Arquitectura

Azure Bastion se implementa en una red virtual y admite el emparejamiento de red virtual. En concreto, Azure Bastion administra la conectividad RDP/SSH con máquinas virtuales creadas en las redes virtuales locales o emparejadas.

RDP y SSH son algunos de los medios fundamentales a mediante los que puede conectarse a las cargas de trabajo que se ejecutan en Azure. La exposición de los puertos RDP/SSH a través de Internet no se conveniente y se considera una superficie de amenaza considerable. Esto suele deberse a las vulnerabilidades del protocolo. Para contener esta superficie de amenaza, puede implementar hosts de bastión (también conocidos como servidores de salto) en la parte pública de la red perimetral. Los servidores host de bastión están diseñados y configurados para resistir los ataques. Los servidores de bastión también proporcionan conectividad RDP y SSH con las cargas de trabajo que se encuentran detrás del bastión, así como más adentro en la red.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagrama que muestra la arquitectura de Azure Bastion.":::

Esta ilustración muestra la arquitectura de una implementación de Azure Bastion. En este diagrama:

* El host bastión se implementa en la red virtual que contiene la subred AzureBastionSubnet que tiene un prefijo /27 mínimo.
* El usuario se conecta a Azure Portal con cualquier explorador HTML5.
* El usuario selecciona la máquina virtual a la que conectarse.
* Con un solo clic, la sesión RDP/SSH se abre en el explorador.
* No se requiere ninguna dirección IP pública en la máquina virtual de Azure.

## <a name="host-scaling"></a><a name="host-scaling"></a>Escalado de host

Azure Bastion admite el escalado de host manual. Puede configurar el número de instancias de host (unidades de escalado) para administrar el número de conexiones RDP/SSH simultáneas que Azure Bastion pueden admitir. Aumentar el número de instancias de host permite a Azure Bastion administrar más sesiones simultáneas. Al reducir el número de instancias, se reduce el número de sesiones admitidas simultáneas. Azure Bastion admite hasta 50 instancias de host. Esta característica solo está disponible para la SKU estándar de Azure Bastion.

Para más información, consulte el artículo [Parámetros de configuración](configuration-settings.md#instance).

## <a name="pricing"></a><a name="pricing"></a>Precios

Los precios de Azure Bastion implican una combinación de precios por hora en función de la SKU, las unidades de escalado y las tasas de transferencia de datos. Puede encontrar más información sobre los precios en la página de [precios](https://azure.microsoft.com/pricing/details/azure-bastion).

## <a name="whats-new"></a><a name="new"></a>Novedades

Suscríbase a la fuente RSS y vea las actualizaciones más recientes de las características de Azure Bastion en la página [Actualizaciones de Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion).

## <a name="bastion-faq"></a>Preguntas más frecuentes sobre Bastion

Para ver las preguntas más frecuentes, consulte las [preguntas más frecuentes](bastion-faq.md) de Bastion.

## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Creación de un host Azure Bastion y conexión a una máquina virtual Windows](tutorial-create-host-portal.md).
* [Módulo de Learn: Introducción a Azure Bastion](/learn/modules/intro-to-azure-bastion/).
* Obtenga información sobre las demás [funcionalidades de red](../networking/fundamentals/networking-overview.md) clave de Azure.
