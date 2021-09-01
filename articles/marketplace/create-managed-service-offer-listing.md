---
title: Configuración de los detalles de descripción de la oferta de servicio administrado en el Centro de partners de Microsoft
description: Configure los detalles de la lista de ofertas de servicios administrados en Azure Marketplace.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/12/2021
ms.openlocfilehash: 1b2171c300fd52403da29d23f9f34d3f94961b3d
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204505"
---
# <a name="configure-managed-service-offer-listing-details"></a>Configuración de los detalles de la lista de ofertas de servicios administrados

La información que especifique en la página **Descripción de la oferta** del Centro de partners se mostrará en Azure Marketplace. Esto incluye el nombre de la oferta, la descripción, los elementos multimedia y otros recursos de marketing.

> [!NOTE]
> Si la oferta está en un idioma distinto del inglés, la descripción de esta podrá estar en tal idioma, pero la descripción deberá comenzar o finalizar con la frase en inglés "This service is available in &lt;idioma del contenido de la oferta>". También puede proporcionar documentos complementarios en un idioma distinto al utilizado en la descripción de la oferta.

En la página **Descripción de la oferta** del Centro de partners, proporcione la información que se describe a continuación. Para obtener más información sobre los detalles de la descripción de la oferta de servicio administrado, revise [Planeamiento de una oferta de servicio administrado](./plan-managed-service-offer.md).

## <a name="marketplace-details"></a>Detalles del marketplace

1. El cuadro **Nombre** se llena previamente con el nombre que escribió anteriormente en el cuadro de diálogo Nueva oferta, aunque puede cambiarlo en cualquier momento. Este nombre aparecerá como título de la descripción de la oferta en la tienda en línea.
2. En el cuadro **Resumen de resultados de búsqueda**, describa el propósito u objetivo de la oferta en 100 caracteres como máximo.
3. En el campo **Descripción breve**, proporcione una descripción breve de la oferta (256 caracteres como máximo). Se mostrará en la descripción de la oferta en Azure Portal.
4. En el campo **Descripción**, describa la oferta de servicio administrado. En este cuadro puede escribir hasta 2000 caracteres de texto, incluidos los espacios y las etiquetas HTML. Para obtener información sobre el formato HTML, consulte [Etiquetas HTML admitidas en las descripciones de las ofertas](./supported-html-tags.md).
5. En el cuadro **Vínculo a la directiva de privacidad**, escriba un vínculo (que comience por https) a la directiva de privacidad de su organización. Usted es responsable no solo de garantizar que la oferta cumpla la normativa y la legislación en lo relativo a la privacidad, sino también de proporcionar una directiva de privacidad válida.

## <a name="product-information-links"></a>Vínculos de información del producto

Tiene la opción de proporcionar documentos en línea complementarios opcionales sobre su solución:

1. Seleccione **Agregar un vínculo**.
2. Proporcione un nombre y una dirección web (que empiece por https) para cada documento.

## <a name="contact-information"></a>Información de contacto

Proporcione el nombre, la dirección de correo electrónico y el número de teléfono de dos personas de la empresa (usted mismo puede ser una de esas personas). Dichas personas serán un contacto de soporte técnico y otro de ingeniería. Usaremos esta información para comunicarnos con usted sobre la oferta. Esta información no se muestra a los clientes, pero se proporcionará a los asociados de Proveedor de soluciones en la nube (CSP).

## <a name="support-link"></a>Vínculo de soporte técnico

Si tiene sitios web de soporte técnico para clientes globales de Azure o clientes de Azure Government, proporcione sus direcciones URL (que empiecen por https).

## <a name="marketplace-media"></a>Elementos multimedia del marketplace

> [!NOTE]
> Si tiene un problema al cargar archivos, asegúrese de que la red local no bloquee el servicio `https://upload.xboxlive.com` que usa el Centro de partners.

### <a name="add-logos"></a>Adición de logotipos

En **Logotipos**, cargue un logotipo **grande** en formato PNG, con unas dimensiones de entre 216 x 216 y 350 x 350 píxeles. El Centro de partners creará automáticamente logotipos de tamaño **mediano** y **pequeño** que podrá reemplazar más adelante.

- El logotipo grande (entre 216 x 216 y 350 x 350 píxeles) aparece en la descripción de la oferta en Azure Marketplace.
- El logotipo mediano (90 x 90 píxeles) se muestra cuando se crea un recurso.
- El logotipo pequeño (48 x 48 píxeles) se usa en los resultados de búsqueda de Azure Marketplace.

### <a name="add-screenshots-optional"></a>Adición de capturas de pantalla (opcional)

Agregue hasta cinco imágenes que muestren la oferta. Todas las imágenes deben tener el tamaño 1280 x 720 píxeles y estar en formato PNG.

1. En **Capturas de pantallas**, arrastre y coloque el archivo PNG en el cuadro **Captura de pantalla**.
2. Seleccione **Agregar leyenda de imagen**.
3. En el cuadro de diálogo que aparece, escriba una leyenda.
4. Repita los pasos 1 a 3 para agregar capturas de pantalla adicionales.

### <a name="add-videos-optional"></a>Adición de vídeos (opcional)

Puede agregar vínculos a vídeos de YouTube o Vimeo que muestren la oferta. Estos vídeos se muestran a los clientes junto con la oferta. Debe incluir una imagen en miniatura del vídeo, con un tamaño de 1280 x 720 píxeles y en formato PNG. Agregue un máximo de cinco vídeos por oferta.

1. En **Vídeos**, seleccione **Add video link** (Agregar vínculo de vídeo).
2. En los cuadros que aparecen, escriba el nombre y el vínculo del vídeo.
3. Arrastre y coloque un archivo PNG (1280 x 720 píxeles) en el cuadro **Miniatura** gris.
4. Para agregar otro vídeo, repita los pasos 1 a 3.

Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña, **Público preliminar**.

## <a name="next-steps"></a>Pasos siguientes

- [Adición de un público en la versión preliminar](create-managed-service-offer-preview.md)
