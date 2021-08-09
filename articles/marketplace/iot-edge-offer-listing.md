---
title: Configuración de los detalles de la lista de ofertas del módulo IoT Edge en Azure Marketplace
description: Configure los detalles de la lista de ofertas del módulo IoT Edge en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 285b57d5d4094f51611b36794584a83d3d115b66
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542082"
---
# <a name="configure-iot-edge-module-offer-listing-details"></a>Configuración de los detalles de la lista de ofertas del módulo IoT Edge

Esta página le permite definir los detalles de la oferta, como el nombre de la oferta, la descripción, los vínculos, los contactos, los logotipos y las capturas de pantalla.

> [!NOTE]
> Proporcione los detalles de la descripción de la oferta solo en un idioma. No es necesario que estén en inglés si la descripción de la oferta comienza con la frase "Esta aplicación solo está disponible en [idioma distinto del inglés]". También es aceptable especificar una *dirección URL de vínculo útil* para ofrecer contenido en un idioma distinto del que se usa en el contenido de la descripción de la oferta.

A continuación se muestra un ejemplo de cómo aparece la información en Azure Marketplace (los precios mostrados son meramente ilustrativos y no pretenden reflejar los costos reales):

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer.png" alt-text="Ilustración de cómo aparece esta oferta en Azure Marketplace.":::

##### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Logotipo
1. Categorías
1. Dirección de soporte técnico (vínculo)
1. Términos de uso
1. Directiva de privacidad (vínculo)
1. Nombre de la oferta
1. Resumen de la oferta
1. Descripción
1. Vínculos para obtener más información
1. Capturas de pantallas o vídeos

<br>Este es un ejemplo de cómo aparece la información de la oferta en los resultados de la búsqueda de Azure Marketplace:

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer-search-results.png" alt-text="Ilustración de cómo aparece esta oferta en los resultados de la búsqueda de Azure Marketplace.":::

##### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Logotipo pequeño
2. Nombre de la oferta
3. Resumen de los resultados de la búsqueda

<br>Este es un ejemplo de cómo aparece la información de la oferta en Azure Portal:

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer.png" alt-text="Ilustración de cómo aparece esta oferta en Azure Portal.":::

##### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Nombre
2. Descripción
3. Vínculos útiles
4. Capturas de pantalla

<br>Este es un ejemplo de cómo aparece la información de la oferta en los resultados de la búsqueda de Azure Portal:

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer-search-results.png" alt-text="Ilustración de cómo aparece esta oferta en los resultados de la búsqueda de Azure Portal.":::

##### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Logotipo pequeño
2. Nombre de la oferta
3. Resumen de los resultados de la búsqueda

## <a name="marketplace-details"></a>Detalles del marketplace

El **nombre** que escriba aquí se muestra a los clientes como el título de la oferta. Este campo se ha rellenado previamente con el texto especificado en **Alias de oferta** cuando creó la oferta, pero puede cambiarlo. El nombre:

- puede incluir símbolos de marca comercial y copyright.
- Debe tener 50 caracteres o menos.
- No puede incluir emojis.

Proporcione una descripción breve de la oferta (100 caracteres como máximo), en el **resumen de resultados de la búsqueda**. Esta descripción se puede usar en los resultados de la búsqueda de Marketplace.

Proporcione una **breve descripción** de la oferta (hasta 256 caracteres). Aparecerá en los resultados de la búsqueda y en la página de detalles de la oferta.

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Use etiquetas HTML para formatear la descripción y hacer que sea más atractiva. Para obtener una lista de las etiquetas permitidas, consulte el artículo sobre las [etiquetas HTML admitidas](supported-html-tags.md).

Escriba el **vínculo a la directiva de privacidad** (URL) de su organización. Usted es el responsable no solo de garantizar que la aplicación cumple la normativa y la legislación en lo tocante a la privacidad, sino también de proporcionar una directiva de privacidad válida.

## <a name="useful-links"></a>Vínculos útiles

Proporcione documentos en línea complementarios sobre su oferta. Puede agregar hasta 25 vínculos. Para agregar un vínculo, seleccione **+ Agregar un vínculo** y, a continuación, rellene los campos siguientes:

- **Título**: los clientes verán el título en la página de detalles de la oferta.
- **Vínculo (URL)** : escriba un vínculo para que los clientes vean el documento en línea. El vínculo debe comenzar con `http://` o `https://`.

Agregue al menos un vínculo a la documentación y un vínculo a los dispositivos IoT Edge compatibles desde el  [catálogo de dispositivos IoT de Azure](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Información de contacto

Proporcione el nombre, el correo electrónico y el número de teléfono de un **contacto de soporte técnico**, un **contacto de ingeniería** y un **contacto del programa del Proveedor de soluciones en la nube**. Esta información no se muestra a los clientes, pero estará disponible para Microsoft y puede proporcionarse a los asociados de CSP.

En la sección de **Contacto del programa Proveedor de soluciones en la nube**, proporcione la dirección del **sitio web de soporte técnico para clientes de Azure global**, donde los asociados pueden encontrar soporte técnico para la oferta en función de si esta está disponible en Azure global, Azure Government o ambos.

En la sección **Contacto de soporte técnico**, proporcione la dirección de **Materiales de marketing del programa CSP**, donde los asociados del programa CSP pueden encontrar materiales de marketing y soporte técnico para la oferta.

## <a name="marketplace-media"></a>Elementos multimedia del marketplace

Proporcione logotipos e imágenes para usarlos con la oferta. Todas las imágenes deben estar en formato PNG. Las imágenes borrosas harán que se rechace el envío.

>[!NOTE]
>Si tiene problemas al cargar archivos, asegúrese de que la red local no bloquee el servicio https://upload.xboxlive.com que usa el Centro de partners.

### <a name="logos"></a>Logotipos

Proporcione un archivo PNG para el logotipo de tamaño **Grande**. El Centro de partners lo usará para crear otros tamaños necesarios. Opcionalmente, puede reemplazarlo por una imagen diferente.

Estos logotipos se usan en distintos lugares de la publicación:

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Capturas de pantalla

Agregue hasta cinco capturas de pantalla opcionales que muestren el funcionamiento de la oferta. Las capturas de pantallas deben tener un tamaño de 1280 x 720 píxeles y estar en formato PNG. Agregue un título para cada captura de pantalla.

### <a name="videos"></a>Vídeos

Agregue hasta cinco vídeos que muestren la oferta (este paso es opcional). Deben estar hospedados en un servicio de vídeo externo. Escriba el nombre de cada vídeo, su dirección web y una imagen en miniatura en formato PNG del vídeo de 1280 x 720 píxeles.

Para ver más recursos sobre las listas de marketplace, consulte [Procedimientos recomendados para las listas de ofertas de marketplace](gtm-offer-listing-best-practices.md).

Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña del menú de navegación izquierdo, **Vista previa de la audiencia**.

## <a name="next-steps"></a>Pasos siguientes

- [Establecer la audiencia preliminar](iot-edge-preview-audience.md)
