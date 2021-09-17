---
title: Uso de imágenes de cliente de Windows en Azure
description: Cómo usar las ventajas para la suscripción de Visual Studio a fin de implementar Windows 7, Windows 8 o Windows 10 en Azure para escenarios de desarrollo/pruebas
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 1fc0b3b628256f5ecbc0361ee2294e70e1619160
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698352"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Uso del cliente Windows en Azure para escenarios de desarrollo y pruebas

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Windows 

Puede usar Windows 7, Windows 8 o Windows 10 Enterprise (x64) en Azure para escenarios de desarrollo y pruebas siempre que tenga una suscripción adecuada a Visual Studio (anteriormente MSDN). 

Para ejecutar Windows 10 en un entorno de producción, consulte [Implementación de Windows 10 en Azure con derechos de hospedaje multiinquilino](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Idoneidad de la suscripción
Los suscriptores activos de Visual Studio (es decir, las personas que adquirieron una licencia de suscripción de Visual Studio) pueden usar imágenes de un cliente Windows para desarrollo y pruebas. Las imágenes de un cliente Windows se pueden usar en su propio hardware o en máquinas virtuales de Azure.

Algunas imágenes de clientes Windows están disponibles en Azure Marketplace. Los suscriptores de Visual Studio dentro de cualquier tipo de oferta también pueden [preparar y crear](prepare-for-upload-vhd-image.md) imágenes de Windows 7, Windows 8 o Windows 10 de 64 bits y, luego, [cargarlas en Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Ofertas válidas e imágenes de clientes
En la tabla siguiente se muestran los detalles de los id. de oferta idóneos para implementar imágenes de clientes Windows a través de Azure Marketplace. Las imágenes de clientes Windows solo son visibles para las ofertas siguientes. 

> [!NOTE]
> Las ofertas de imágenes se encuentran en el **cliente de Windows** en Azure Marketplace. Use el **cliente de Windows** al buscar imágenes de cliente disponibles para los suscriptores de Visual Studio. 

| Nombre de la oferta | Número de la oferta | Imágenes de cliente disponibles | 
|:--- |:---:|:---:|
| [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Suscriptores de Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Suscriptores de Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Suscriptores de Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Visual Studio Premium con MSDN (ventaja)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Suscriptores de Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Suscriptores de Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |
| [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N con SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Compruebe la suscripción a Azure
Si no conoce el id. de su oferta, puede obtenerlo en Azure Portal.  
- En la ventana *Suscripciones*: ![Detalles del id. de oferta de Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- O bien, haga clic en **Facturación** y, a continuación, haga clic en el identificador de suscripción. El identificador de oferta aparece en la ventana de *facturación*. 
- También puede ver el id. de la oferta en la pestaña ["Suscripciones"](https://account.windowsazure.com/Subscriptions) del portal de la cuenta de Azure: ![Detalles del id. de oferta de la cuenta en Azure Portal](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Pasos siguientes
Ahora puede implementar las máquinas virtuales con [PowerShell](quick-create-powershell.md), [plantillas de Resource Manager](ps-template.md) o [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
