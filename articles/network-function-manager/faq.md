---
title: Preguntas más frecuentes sobre Network Function Manager
titleSuffix: Azure Network Function Manager
description: Consulte las preguntas más frecuentes sobre Network Function Manager.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5cf6b9bdc9982adf2da86956ce41e7cd5e7458ec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004951"
---
# <a name="azure-network-function-manager-faq"></a>Preguntas frecuentes de Azure Network Function Manager

## <a name="faqs"></a>Preguntas más frecuentes

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>Soy un asociado de función de red y quiero incorporarme a Network Function Manager. ¿Cómo puedo ofrecer mi función de red con NFM?

Nuestro objetivo es proporcionar a los clientes el ecosistema enriquecido de funciones de red disponibles que elijan en Azure Stack Edge. Envíenos un mensaje de correo electrónico a **aznfmpartner@microsoft.com** para hablar sobre los requisitos de incorporación.

### <a name="does-network-function-manager-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>¿Network Function Manager admite otros dispositivos perimetrales de Azure además de Azure Stack Edge Pro con GPU?

NFM está disponible actualmente en Azure Stack Edge Pro con GPU, que está disponible con carácter general. ASE Pro es hardware como servicio que está diseñado para ejecutar funciones de red especializadas, como el núcleo de paquetes móviles y el borde SD-WAN. El dispositivo está equipado con seis puertos físicos con compatibilidad con la aceleración de red en los puertos 5 y 6. Consulte las [especificaciones de la interfaz de red](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications) para Azure Stack Edge Pro con un dispositivo de GPU. Los asociados de funciones de red pueden aprovechar las funcionalidades SR-IOV y DPDK para ofrecer un rendimiento de red superior para sus funciones de red.

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>¿Qué funcionalidades adicionales están disponibles en Azure Stack Edge Pro con GPU además de la ejecución de funciones de red?

Azure Stack Edge (ASE) Pro con GPU y Azure Network Function Manager forman parte de la solución [Proceso perimetral multiacceso privado (MEC) de Azure](../private-multi-access-edge-compute-mec/index.yml). Ahora puede ejecutar una red móvil privada y una máquina virtual o una aplicación perimetral basada en contenedores en el dispositivo ASE. Esto le permite crear soluciones innovadoras que proporcionan contratos de nivel de servicio predecibles para las aplicaciones empresariales críticas. Azure Stack Edge Pro también está equipado con una o dos [GPU](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications) que le permiten aprovechar escenarios como la inferencia de vídeo y el aprendizaje automático en el perímetro.

### <a name="what-is-the-pricing-for-network-function-manager"></a>¿Cuáles son los precios de Network Function Manager?

Azure Network Function Manager se ofrece sin costo adicional en el dispositivo de Azure Stack Edge Pro. Los asociados de función de red pueden tener un cargo independiente para ofrecer sus funciones de red con el servicio NFM. Consulte al asociado de funciones de red los detalles de los precios.

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>Si mi dispositivo de Azure Stack Edge Pro está en modo desconectado o parcialmente conectado, ¿dejarán de funcionar las funciones de red ya implementadas?

El servicio Network Function Manager requiere conectividad de red con el dispositivo ASE para las operaciones de administración con el objetivo de crear o eliminar funciones de red, supervisarlas y solucionar los problemas que surjan con aquellas que se ejecutan en el dispositivo. Si la función de red se implementa en el dispositivo ASE y el dispositivo está desconectado o parcialmente conectado a la función de red subyacente, las máquinas virtuales deberían seguir funcionando sin interrupciones. Las funciones de red implementadas en estas máquinas virtuales pueden tener requisitos diferentes basados en la administración de configuración y requisitos de conectividad de red adicionales de los asociados de función de red. Consúltele a su asociado cuáles son los requisitos de conectividad de red y los modos de funcionamiento.

### <a name="which-regions-are-supported-for-nfm-will-you-add-support-for-additional-azure-regions"></a>¿Qué regiones se admiten para NFM? ¿Se agregará compatibilidad con otras regiones de Azure?

Network Function Manager está disponible en las siguientes regiones:

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

Azure Stack Edge Pro con GPU está disponible en varios países para implementar y ejecutar las funciones de red que elija. Para ver una lista de todos los países y las regiones en los que el dispositivo de Azure Stack Edge Pro GPU está disponible, vaya a la página [Precios de Azure Stack Edge Pro GPU](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). En la pestaña **Azure Stack Edge Pro**, vea la sección  **Disponibilidad** .

Puede registrar el dispositivo de Azure Stack Edge y los recursos de Network Function Manager en función de los requisitos normativos y de soberanía de datos. La región de Azure asociada a los recursos de Network Function Manager se usa para guiar las operaciones de administración desde el servicio en la nube hasta el dispositivo físico.

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>Al eliminar la aplicación administrada para mi función de red que se ejecuta en Azure Stack Edge, ¿se detendrá automáticamente la facturación de las funciones de red?

Consúltele al asociado de función de red cuál es el ciclo de facturación de las funciones de red implementadas mediante Network Function Manager. Cada asociado tendrá una directiva de facturación diferente para sus ofertas de funciones de red.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte la [introducción](overview.md).
