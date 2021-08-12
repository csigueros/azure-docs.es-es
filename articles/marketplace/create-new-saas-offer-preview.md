---
title: Adición de una audiencia preliminar a una oferta de SaaS en Azure Marketplace
description: Agregue una audiencia preliminar a una oferta de software como servicio (SaaS) en Azure Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fe42b5c7d9953a3e8bc15c4dbd351d3d2beb4d33
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614411"
---
# <a name="add-a-preview-audience-for-a-saas-offer"></a>Adición de una audiencia preliminar a una oferta de SaaS

Cuando se crea una oferta de software como servicio (SaaS) en el Centro de partners, es necesario definir una audiencia preliminar que pueda revisar la descripción de la oferta antes de publicarla. En este artículo se explica cómo configurar una audiencia preliminar.

> [!NOTE]
> Si elige procesar las transacciones de forma independiente, no verá esta opción. En lugar de ello, vaya a [Comercialización de ofertas de SaaS](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Definición de una audiencia preliminar

En la pestaña **Vista previa de la audiencia**, puede definir una audiencia limitada que revise la oferta de SaaS antes de publicarla para la audiencia más amplia del marketplace. Puede enviar invitaciones a direcciones de correo electrónico de cuentas de Microsoft (MSA) o de Azure Active Directory (Azure AD). Agregue un mínimo de 1 y un máximo de 10 direcciones de correo electrónico manualmente o importe hasta 20 con un archivo .csv. La lista de audiencias preliminares se puede actualizar en cualquier momento.

> [!NOTE]
> Un público preliminar no es el mismo que un público privado. A una audiencia preliminar se le permite el acceso a la oferta antes de que se publique en las tiendas en línea. También puede elegir crear un plan y ponerlo a disposición únicamente de un público privado. Los planes privados se explican en [Creación de planes para la oferta de SaaS](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Agregar direcciones de correo electrónico manualmente

1. En la página **Audiencia preliminar**, agregue una sola dirección de correo electrónico de Azure AD o MSA y una descripción opcional en los cuadros proporcionados.
1. Para agregar otra dirección de correo electrónico, seleccione el vínculo **Add another email** (Agregar otro correo electrónico).
1. Seleccione **Guardar borrador** antes de continuar con la siguiente pestaña: **Configuración técnica**.
1. Continúe con [Incorporación de detalles técnicos a la oferta de SaaS](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Agregar direcciones de correo electrónico mediante el archivo CSV

1. En la página **Audiencia preliminar**, seleccione el vínculo **Exportar público (CSV)** .
1. Abra el archivo CSV en una aplicación, como Microsoft Excel.
1. En el archivo CSV, en la columna **Id.** , escriba las direcciones de correo electrónico que quiere agregar a la audiencia preliminar.
1. En la columna **Descripción**, tiene la opción de agregar una descripción para cada dirección de correo electrónico.
1. En la columna **Tipo**, agregue **MixedAadMsaId** a cada fila que tenga una dirección de correo electrónico.
1. Guarde el archivo como un archivo CSV.
1. En la página **Audiencia preliminar**, seleccione el vínculo **Import Audience (csv)** (Importar audiencia [csv]).
1. En el cuadro de diálogo **Confirmar**, seleccione **Sí**.
1. Seleccione el archivo CSV y, después, elija **Abrir**.
1. Seleccione **Guardar borrador** antes de continuar con la siguiente pestaña: **Configuración técnica**.

## <a name="next-steps"></a>Pasos siguientes

- [Adición de detalles técnicos de una oferta de SaaS](create-new-saas-offer-technical.md)
