---
title: Creación de una oferta de objeto visual de Power BI en el Centro de partners para Microsoft AppSource
description: Obtenga información sobre cómo crear una oferta de objeto visual de Power BI en el Centro de partners.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: eca02cf776eb1f6fa690ecefd865dadbc3d9be78
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080076"
---
# <a name="create-a-power-bi-visual-offer"></a>Creación de una oferta de objeto visual de Power BI

En este artículo se describe cómo usar el Centro de partners para enviar una oferta de objeto visual de Power BI a [Microsoft AppSource](https://appsource.microsoft.com) para que otros usuarios puedan descubrirla y usarla.

Antes de empezar, cree una cuenta de marketplace comercial en el [Centro de partners](./create-account.md) y asegúrese de que esté inscrita en el programa del marketplace comercial.

## <a name="before-you-begin"></a>Antes de empezar

Revise [Planificación de una oferta de objeto visual de Power BI](marketplace-power-bi-visual.md). En este artículo se explican los requisitos técnicos de esta oferta y se muestra la información y los recursos que necesitará para crearla.

## <a name="create-a-new-offer"></a>Crear una nueva oferta

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).

1. En la página principal, seleccione el elemento **Ofertas de Marketplace**.

    [ ![Muestra el elemento Ofertas de Marketplace en la página principal del Centro de partners.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. En la página "Ofertas de Marketplace", seleccione **+ Nueva oferta** > **Objeto visual de Power BI**.

    [ ![Muestra las opciones del menú del panel izquierdo y el botón "Nueva oferta".](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
1. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > **Información general**.
1. En la página de información general, seleccione **+ Nueva oferta** > **Objeto visual de Power BI**.

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="Muestra las opciones del menú del panel izquierdo y el botón &quot;Nueva oferta&quot;.":::

---

> [!IMPORTANT]
> Una vez publicada la oferta, las modificaciones que realice en ella en el Centro de partners aparecerán en AppSource solo después de volver a publicarla. Asegúrese de volver a publicar siempre una oferta después de cambiarla.

## <a name="new-offer"></a>Nueva oferta

Escriba un **Identificador de oferta**. Se trata de un identificador único para cada oferta de su cuenta.

- Este identificador es visible para los clientes en la dirección web de la oferta y en las plantillas de Azure Resource Manager, si procede.
- Use solo letras minúsculas y números. El identificador puede incluir guiones y caracteres de subrayado, pero no espacios, y está limitado a 50 caracteres. Por ejemplo, si el identificador del editor es `testpublisherid` y escribe **test-offer-1**, la dirección web de la oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- El identificador de oferta no se puede cambiar después de seleccionar **Crear**.
- El id. de oferta debe ser único en la lista de todas las demás ofertas de objetos visuales de Power BI en el Centro de partners.

Escriba un **Alias de la oferta**. Este es el nombre que se usa para la oferta en el Centro de partners.

- Este nombre no se utiliza en AppSource. Es diferente del nombre de la oferta y de otros valores que se muestran a los clientes.

Seleccione **Crear** para generar la oferta y continuar. En el Centro de partners, abra la página **Configuración de la oferta**.

## <a name="setup-details"></a>Detalles de la configuración

En **Additional purchases** (Compras adicionales), seleccione si la oferta requiere o no compras de un servicio o compras adicionales en la aplicación.

En **Power BI certification** (Certificación de Power BI) (opcional), lea detenidamente la descripción y, si desea solicitar una certificación de Power BI, active la casilla. Los objetos visuales de Power BI [certificados](https://aka.ms/PBIvisualcertification) cumplen determinados requisitos de código especificados que el equipo de Microsoft Power BI ha probado y aprobado. Se recomienda enviar y publicar el objeto visual de Power BI *antes* de solicitar la certificación, ya que el proceso de certificación tarda más tiempo, de modo que podría retrasar la publicación de la oferta.

## <a name="customer-leads"></a>Clientes potenciales

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

La conexión a CRM es opcional. Para más información, consulte [Clientes potenciales a partir de la oferta en el marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña del menú de navegación izquierdo, **Propiedades**.

## <a name="next-steps"></a>Pasos siguientes

- [**Propiedades de la oferta**](power-bi-visual-properties.md)
