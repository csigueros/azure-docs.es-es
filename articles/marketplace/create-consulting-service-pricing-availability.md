---
title: Configuración de los precios y la disponibilidad del servicio de consultoría para marketplace comercial
description: Configure la información sobre precios y disponibilidad de mercado del servicio de consultoría en el marketplace comercial de Microsoft mediante el Centro de partners.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: cf6290632a1c9d78265961ccaa47b25fc1734a1b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065965"
---
# <a name="configure-consulting-service-offer-pricing-and-availability"></a>Configuración de precios y disponibilidad de ofertas del servicio de consultoría

En este artículo se explica cómo definir la información de mercado y precios de su oferta de servicios de consultoría en el marketplace comercial de Microsoft.

> [!NOTE]
> Las ofertas de servicios de consultoría solo se publican. Todas las transacciones deben administrarse entre usted y sus clientes fuera del marketplace comercial.

## <a name="markets"></a>Mercados

En la sección **Mercados**, seleccione el país o región en la que estará disponible el servicio de consultoría.

1. En **Mercados**, seleccione el vínculo **Editar mercados**.
2. En el cuadro de diálogo que aparece, seleccione las ubicaciones de mercado en las que desea que la oferta esté disponible. Debe seleccionar un mínimo de uno y un máximo de 141 mercados.
3. Seleccione **Guardar** para cerrar el cuadro de diálogo.

## <a name="preview-audience"></a>Público preliminar

Al publicar o actualizar la oferta de servicios de consultoría, el Centro de partners crea una versión preliminar de la oferta. Esta versión preliminar solo es visible para los usuarios que tienen una **clave oculta**.

En el campo **clave oculta**, escriba una cadena alfanumérica que usará para acceder a la versión preliminar de la oferta.

## <a name="pricing-informational-only"></a>Precios (solo con fines informativos)

En la sección **Precios**, puede definir si se trata de una oferta gratuita o de pago.

Para las ofertas de pago, especifique si el precio es fijo o estimado. Si el precio es estimado, la descripción de la oferta debe describir qué factores afectarán al precio.

Si eligió un solo país o región en la sección **Mercados**, indique el precio en una moneda admitida para ese mercado y seleccione **Guardar borrador**. Para revisar la lista de monedas, vaya a [Disponibilidad geográfica y compatibilidad con monedas para Marketplace comercial](./marketplace-geo-availability-currencies.md).

Si seleccionó varios países o regiones en la sección **Mercados**, proporcione el precio en dólares estadounidenses (USD) y seleccione **Guardar borrador**. El Centro de partners convertirá ese precio en la divisa local de todos los mercados seleccionados usando las tarifas de cambio disponibles al guardar el borrador.

Para validar la conversión o establecer precios personalizados en un mercado individual, debe exportar, modificar y, a continuación, importar la hoja de cálculo de precios:

1. En **Precio**, seleccione el vínculo **Export pricing data** (Exportar datos de precios). Se descargará un archivo en el dispositivo.
1. Abra el archivo exportedPrice.xlsx en Microsoft Excel.
1. En la hoja de cálculo, puede ajustar los precios y las monedas de cada mercado. Para revisar la lista de monedas, vaya a [Disponibilidad geográfica y compatibilidad con monedas para Marketplace comercial](./marketplace-geo-availability-currencies.md). Cuando haya acabado, guarde el archivo.
1. En el Centro de partners, en **Precios**, seleccione el vínculo **Importar datos de precios**. Al importar el archivo, se sobrescribirá la información de precios anterior.

> [!IMPORTANT]
> Los precios definidos en el Centro de partners son estáticos y no siguen las variaciones en las tasas de cambio. Para cambiar el precio en uno o varios mercados después de la publicación, actualice y vuelva a enviar la oferta en el Centro de partners.

Seleccione **Guardar borrador** antes de continuar.

## <a name="next-steps"></a>Pasos siguientes

* [Revisión y publicación](review-publish-offer.md)
