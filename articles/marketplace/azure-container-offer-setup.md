---
title: Creación de una oferta de Azure Container en Azure Marketplace
description: Creación de una oferta de Azure Container en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 09/27/2021
ms.openlocfilehash: b09500142878ee383ccc20e8b62ed22b0f9478c5
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079886"
---
# <a name="create-an-azure-container-offer"></a>Creación de una oferta de Azure Container

En este artículo se describe cómo crear una oferta de Azure Container. Todas las ofertas pasan por un proceso de certificación, que comprueba la solución en busca de requisitos estándar, compatibilidad y procedimientos adecuados.

Antes de empezar, cree una cuenta comercial de Marketplace en el [Centro de partners](./create-account.md) y asegúrese de que esté inscrita en el programa comercial de Marketplace.

## <a name="before-you-begin"></a>Antes de empezar

Revise la [Planificación de una oferta de Azure Container](marketplace-containers.md). Ahí se explican los requisitos técnicos de la oferta y se enumeran la información y los recursos que necesitará al crearla.

## <a name="create-a-new-offer"></a>Crear una nueva oferta

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).

1. En la página principal, seleccione el icono **Ofertas de Marketplace**.

    [ ![Muestra el icono Ofertas de Marketplace en la página principal del Centro de partners.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. En la página Ofertas de Marketplace, seleccione **+ Nueva oferta** > **Azure Container**.

    [ ![Opciones del menú del panel de la izquierda y el botón "Nueva oferta".](./media/azure-container/new-offer-azure-container-workspaces.png) ](./media/azure-container/new-offer-azure-container-workspaces.png#lightbox)

> [!IMPORTANT]
> Una vez publicada la oferta, las modificaciones que realice en el Centro de partners aparecerán en Azure Marketplace solo después de volver a publicarla. Asegúrese de volver a publicar siempre una oferta después de cambiarla.

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
2. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > **Información general**.
3. En la página Información general, seleccione **+ Nueva oferta** > **Azure Container**.

    :::image type="content" source="media/azure-container/new-offer-azure-container.png" alt-text="Opciones del menú del panel izquierdo y el botón &quot;Nueva oferta&quot;":::.

> [!IMPORTANT]
> Una vez publicada la oferta, las modificaciones que realice en el Centro de partners aparecerán en Azure Marketplace solo después de volver a publicarla. Asegúrese de volver a publicar siempre una oferta después de cambiarla.

---

## <a name="new-offer"></a>Nueva oferta

Escriba un **Identificador de oferta**. Se trata de un identificador único para cada oferta de su cuenta.

- Este identificador es visible para los clientes en la dirección web de la oferta y en las plantillas de Azure Resource Manager, si procede.
- Use solo letras minúsculas y números. El identificador puede incluir guiones y caracteres de subrayado, pero no espacios, y está limitado a 50 caracteres. Por ejemplo, si el identificador del editor es `testpublisherid` y escribe **test-offer-1**, la dirección web de la oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- El identificador de oferta no se puede cambiar después de seleccionar **Crear**.

Escriba un **Alias de la oferta**. Este es el nombre que se usa para la oferta en el Centro de partners.

- Este nombre no se utiliza en Azure Marketplace. Es diferente del nombre de la oferta y de otros valores que se muestran a los clientes.

Seleccione **Crear** para generar la oferta y continuar. En el Centro de partners, abra la página **Configuración de la oferta**.

## <a name="alias"></a>Alias

Escriba un nombre descriptivo que se usará para hacer referencia a esta oferta únicamente dentro del Centro de partners. Este alias de oferta (rellenado previamente con lo que se haya especificado al crear la oferta) no se usará en el marketplace y es diferente del nombre de la oferta que se muestra a los clientes. Si quiere actualizar el nombre de la oferta más adelante, vaya a la página [Descripción de la oferta](azure-container-offer-listing.md).

## <a name="customer-leads"></a>Clientes potenciales

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Para más información, consulte [Clientes potenciales a partir de la oferta en el marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña del menú de navegación izquierdo, **Propiedades**.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de las propiedades de la oferta](azure-container-properties.md) 
- [Procedimientos recomendados para la publicación de ofertas](gtm-offer-listing-best-practices.md)
