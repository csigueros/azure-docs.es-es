---
title: Ofertas privadas en el marketplace comercial de Microsoft
description: Ofertas privadas en el marketplace comercial de Microsoft para publicadores de aplicaciones y servicios.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.date: 02/22/2021
ms.openlocfilehash: 9828d5e52f06f54026894ad70d6c16aaebe8d2fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749452"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas en el marketplace comercial de Microsoft

Las ofertas privadas, también llamados planes privados, permiten a los editores crear planes que solo pueden ver los clientes específicos. En este artículo se tratan las opciones y ventajas de las ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueo de ofertas empresariales con ofertas privadas

Con la creación de ofertas privadas, los editores pueden ofrecer de manera privada soluciones personalizadas a clientes específicos que tengan las funcionalidades que requieren las empresas:

- Los *precios negociados* permiten que los editores extiendan descuentos y precios fuera de la lista más allá de las ofertas disponibles a todo público.
- Los *términos y condiciones privados* permiten que los editores adapten los términos y condiciones a un cliente específico.
- Las *configuraciones especializadas* permiten que los editores adapten sus ofertas de Virtual Machines, Aplicaciones de Azure y software como servicio (SaaS) a las necesidades de un cliente individual. Esta opción también permite que los editores brinden acceso de versión preliminar a características de productos nuevos, antes de lanzarlos a todos los clientes.

Las ofertas privadas permiten que los editores aprovechen la disponibilidad global y a escala de un marketplace público, con la flexibilidad y el control que se necesitan para negociar y brindar ofertas y configuraciones personalizadas. Ahora las empresas pueden comprar y vender tal y como estaba planeado.

## <a name="create-private-offers-using-plans"></a>Creación de ofertas privadas mediante planes

Para las *ofertas nuevas o existentes con planes públicos*, los editores pueden crear fácilmente variaciones privadas nuevas si crean planes nuevos (anteriormente denominados "SKU") y los marcan como privados. Cada oferta puede tener hasta 45 planes privados.

<!--- [Private SKUs]() --->

Los planes privados están disponibles para los tipos de ofertas siguientes:

- Máquina virtual de Azure
- Aplicación de Azure (se implementa como plantillas de solución o aplicaciones administradas)
- Servicio administrado
- Ofertas de SaaS

Los planes privados son componentes de una oferta y solo clientes específicos pueden verlos y comprarlos. Solo clientes específicos pueden ver y comprar los planes privados. Los planes privados pueden estar a disposición de los clientes tanto en Azure Global como en Azure Government.

Los planes privados pueden volver a usar las imágenes base o metadatos de la oferta ya publicados para un plan público. Esta opción permite que los editores creen múltiples variaciones privadas de una oferta pública sin tener que publicar varias versiones de la misma imagen base y metadatos de oferta. Solo en el caso de las ofertas de Azure Virtual Machines y Aplicación de Azure, cuando un plan privado comparte una imagen base con un plan público, los cambios que se hacen en la imagen base de la oferta se propagan a todos los planes, tanto públicos como privados, que usan esa imagen base.

En el caso de las *ofertas nuevas que solo incluyen planes privados*, los editores pueden crear sus ofertas como cualquier otra oferta y, luego, marcar los planes como privados. Los clientes que no estén asociados con la oferta no podrán descubrir ni acceder a través de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) a las ofertas que solo tienen planes privados.

>[!NOTE]
>Una oferta que solo contenga planes privados no será visible en el catálogo público de Azure Marketplace ni AppSource.

## <a name="target-customers-with-private-offers"></a>Selección de clientes específicos para las ofertas privadas

Tanto para las ofertas privadas nuevas como existentes, los editores pueden seleccionar clientes específicos mediante el uso de identificadores de suscripción. En el caso de las ofertas de Azure Virtual Machines, Aplicación de Azure y Servicio administrado, los editores pueden restringir la disponibilidad de un plan privado a un identificador de suscripción de Azure o cargar un archivo .csv con hasta 10 000 identificadores de suscripción de Azure. En el caso de las ofertas de SaaS, los editores pueden asociar un identificador de inquilino de Azure Active Directory para restringir la disponibilidad de un plan privado, ya sea con el enfoque manual o mediante la carga del archivo .csv.

Una vez que se certifica o publica una oferta, los clientes se pueden actualizar o quitar del plan mediante la característica de sincronización de suscripciones privadas. Esta funcionalidad permite que los editores actualicen rápida y sencillamente la lista de clientes a los que se presenta el plan privado sin que sea necesario volver a certificar ni a publicar la oferta.

## <a name="deploying-private-offers"></a>Implementación de las ofertas privadas

Una vez que inician sesión en Azure Portal, los clientes pueden seguir estos pasos para seleccionar las ofertas privadas.

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/).
1. En **Servicios de Azure**, seleccione **Crear un recurso**.
1. En la página **Nuevo**, junto a **Azure Marketplace**, seleccione **Ver todo**. Aparece la página Marketplace.
1. En el panel de navegación de la izquierda, seleccione **Ofertas privadas**.

> [!NOTE]
> Las ofertas privadas solo se descubren en [Azure Portal](https://azure.microsoft.com/features/azure-portal/). No aparecen en [Microsoft AppSource](https://appsource.microsoft.com/) ni en [Azure Marketplace](https://azuremarketplace.microsoft.com). Para más información sobre la publicación en las distintas tiendas en línea de marketplace comercial, consulte [Introducción a las opciones de lista](./determine-your-listing-type.md).

Las ofertas privadas también aparecerán en los resultados de la búsqueda y se pueden implementar a través de la línea de comandos y plantillas de Azure Resource Manager como cualquier otra oferta.

[![[Ofertas privadas que aparecen en los resultados de búsqueda.]](media/marketplace-publishers-guide/private-offer.png)](media/marketplace-publishers-guide/private-offer.png#lightbox)

>[!Note]
>Las ofertas privadas no son compatibles con las suscripciones que se establecen a través de un revendedor del programa Proveedor de soluciones en la nube (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
