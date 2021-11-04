---
title: Requisitos previos y condiciones para Azure Network Function Manager
description: Obtenga información sobre los requisitos y requisitos previos de Network Function Manager.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8843c92943071a96c6e0e9340823a85d01b1c218
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093269"
---
# <a name="network-function-manager-prerequisites-and-requirements"></a>Condiciones y requisitos previos de Network Function Manager

Este artículo le ayuda a comprender las condiciones y los requisitos previos necesarios para configurar y usar Azure Network Function Manager.

## <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Azure Stack Edge Pro con GPU instalado y activado

Compruebe que tiene los siguientes requisitos previos:

* El servicio Azure Network Function Manager está habilitado en el dispositivo Azure Stack Edge Pro.
* Antes de implementar funciones de red, confirme que Azure Stack Edge Pro está instalado y activado.
* El recurso de Azure Stack Edge debe implementarse en una región que sea compatible con los recursos de Network Function Manager. Para obtener más información, vea [Disponibilidad en regiones](overview.md#regions).
* Asegúrese de seguir todos los pasos de los [inicios rápidos](../databox-online/azure-stack-edge-gpu-quickstart.md) y los [tutoriales](../databox-online/azure-stack-edge-gpu-deploy-checklist.md) de Azure Stack Edge Pro.
* Compruebe que el **Estado** del dispositivo,que se encuentra en la sección de propiedades del recurso de Azure Stack Edge en el portal de administración de Azure, sea **En línea**.

   :::image type="content" source="./media/overview/properties.png" alt-text="Captura de pantalla de las propiedades." lightbox="./media/overview/properties.png":::

## <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Requisitos previos de los asociados

Los clientes pueden elegir entre uno o varios [asociados](partners.md) de Network Function Manager para implementar su función de red en un dispositivo Azure Stack Edge. Para más información sobre los asociados de Network Function Manager, consulte el artículo [asociados](partners.md).

Cada asociado tiene requisitos de red para la implementación de su función de red en un dispositivo Azure Stack Edge. Vea la documentación del producto de los asociados de funciones de red para realizar las siguientes tareas de configuración:

* [Configuración de la red en distintos puertos](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Habilitación de la red de proceso en el dispositivo Azure Stack Edge](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

## <a name="azure-account"></a><a name="account"></a>Cuenta de Azure

Los asociados de Azure Network Function Manager deben habilitar el mismo identificador de suscripción de Azure para implementar sus funciones de red desde Azure Marketplace. Asegúrese de que el identificador de suscripción de Azure está incorporado con el asociado.

El servicio Azure Network Function Manager consta de los recursos de **dispositivo** y **función de red** de Network Function Manager. Los recursos de dispositivo y función de red están en suscripciones de Azure. El identificador de suscripción de Azure que se usa para activar el dispositivo Azure Stack Edge Pro y los recursos de Network Function Manager debe ser el mismo.

## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Requisitos de puertos y reglas de firewall

Los servicios de Network Function Manager (NFM) que se ejecutan en Azure Stack Edge requieren conectividad saliente con el servicio en la nube NFM para que el tráfico de administración implemente funciones de red. NFM está totalmente integrado con el servicio Azure Stack Edge. Revise los requisitos de puertos de redes y las reglas de firewall del dispositivo [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements).  

Los asociados de funciones de red tienen requisitos diferentes para las reglas de configuración de puertos y firewall para administrar el tráfico al portal de administración de asociados. Consulte al asociado de funciones de red los requisitos específicos.

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de un recurso de dispositivo para Network Function Manager](create-device.md).
