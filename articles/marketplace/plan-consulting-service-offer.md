---
title: Planificación de una oferta de servicios de consultoría para el marketplace comercial de Microsoft
description: Cómo planear una nueva oferta de servicios de consultoría para Microsoft AppSource o Azure Marketplace con el programa de marketplace comercial en el Centro de partners de Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 3ffca17efa59a0544e06bb3bd4fbae1d812f2823
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819220"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>Planificación de una oferta de servicios de consultoría en el marketplace comercial

En este artículo se presentan los distintos requisitos y opciones para publicar ofertas de servicios de consultoría en el marketplace comercial de Microsoft. Lea este artículo para preparar su oferta para publicación con el Centro de partners.

## <a name="whats-a-consulting-service"></a>¿Qué es un servicio de consultoría?

Los servicios de consultoría son interacciones con los clientes, ya sea de manera virtual o en persona, que complementan y amplían el uso que los clientes hacen de los productos de Microsoft. Con un servicio de consultoría, los clientes pueden evaluar e implementar soluciones que mejoren sus objetivos comerciales. Elija el ámbito, la duración y la estructura de precios (precio fijo o gratis) de su servicio.

Las ofertas de servicios de consultoría se publican como descripciones del tipo **Ponerse en contacto conmigo**. Esto implica que los clientes se pondrán en contacto con usted directamente a partir de la información que proporcione en la oferta. Aunque Microsoft hospeda la oferta en el marketplace comercial, usted administra todas las interacciones con los clientes. Tiene la responsabilidad de prestar el servicio, la facturación, la generación de facturas y la recopilación del cliente.

## <a name="primary-products-and-online-stores"></a>Productos principales y tiendas en línea

Cada servicio de consultoría debe centrarse en uno de estos productos de Microsoft, denominado **producto principal** de la oferta:

:::row:::
    :::column:::
        Azure

        Dynamics 365 Business Central

        Dynamics 365 Commerce

        Dynamics 365 Customer Insights

        Dynamics 365 Customer Service

        Dynamics 365 Field Service

        Dynamics 365 Finance

        Dynamics 365 Marketing
    :::column-end:::
    :::column:::
        Dynamics 365 Project Service Automation

        Dynamics 365 Sales

        Dynamics 365 Supply Chain Management

        Microsoft 365

        PowerApps

        Power Automate

        Power BI

        Power Virtual Agents
    :::column-end:::
:::row-end:::

El producto principal que seleccione en el Centro de partners determinará si la oferta se va a publicar en Microsoft AppSource o Azure Marketplace.

* Si el producto principal del servicio de consultoría es Azure, la oferta se incluirá en Azure Marketplace.
* Si el producto principal no es de Azure, la oferta se incluirá en AppSource.

Para obtener más información sobre las diferencias entre AppSource y Azure Marketplace, consulte [Tiendas en línea del marketplace comercial](./overview.md#commercial-marketplace-online-stores).

## <a name="eligibility-requirements"></a>Requisitos de elegibilidad

Para demostrar a los clientes su experiencia en un campo, debe cumplir un conjunto de requisitos de idoneidad antes de publicar una oferta de servicios de consultoría. Los requisitos dependen del producto en el que se centra la oferta. La lista completa de requisitos de idoneidad para cada producto principal se encuentra en las [directivas de certificación para servicios de consultoría](/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> En el caso de algunos productos primarios, debe tener una competencia Gold o Silver de Microsoft en el área de su solución. Para más información, consulte las [competencias de Microsoft Partner Network](https://partner.microsoft.com/membership/competencies).

## <a name="service-type-and-duration"></a>Tipo y duración del servicio

El marketplace comercial admite cinco tipos de servicios de consultoría:

* **Evaluación**: una evaluación del entorno del cliente para determinar la aplicabilidad de una solución y estimar el costo y cronograma de la implementación.
* **Sesión informativa**: introducción a una solución o servicio mediante marcos, demostraciones y ejemplos de clientes.
* **Implementación**: una instalación completa que da como resultado una solución que funciona completamente.
* **Prueba de concepto**: una implementación de ámbito limitado para determinar si una solución cumple los requisitos del cliente.
* **Taller**: una interacción interactiva que se lleva a cabo en las instalaciones del cliente. Puede implicar entrenamiento, sesiones informativas, evaluaciones o demostraciones basados en los datos o el entorno del cliente.

El servicio debe tener una duración predeterminada de hasta 12 meses. La duración del servicio debe definirse explícitamente en la descripción de la oferta.

## <a name="customer-leads"></a>Clientes potenciales

Debe conectar su oferta al sistema de administración de relaciones con clientes (CRM) para recopilar información del cliente. El cliente le pedirá permiso para compartir su información. Estos datos del cliente, junto con el nombre y el identificador de la oferta, además de la tienda en línea donde la encontró, se envían al sistema CRM que haya configurado. El marketplace comercial admite varios tipos de sistemas CRM, junto con la opción de usar una tabla de Azure o de configurar un punto de conexión HTTPS con Power Automate.

Puede agregar o modificar una conexión CRM en cualquier momento durante o después de la creación de la oferta. Para obtener instrucciones detalladas, vea [Clientes potenciales a partir de la oferta en el marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="offer-listing-details"></a>Detalles de la descripción de la oferta

Al crear la oferta de servicio de consultoría en el Centro de partners, introducirá texto, imágenes, documentos y otros detalles de la oferta. Esto es lo que verán los clientes cuando encuentren su oferta en AppSource o Azure Marketplace. Observe el ejemplo siguiente:

![Muestra cómo se ve una oferta de servicio de consultoría en las tiendas en línea](./media/example-consulting-service.png)

**Descripciones destacadas**

1. Logotipo
2. Tipo de servicio
3. Competencias
4. Áreas de solución (Azure Marketplace)/productos (AppSource)
5. Industrias
6. País/región
7. Nombre de la oferta
8. Resumen de resultados de búsqueda
9. Descripción
10. Capturas de pantallas o vídeos
11. Documentos relacionados

> [!NOTE]
> Si va a prestar su servicio en un idioma distinto del inglés, la descripción de la oferta puede estar en ese idioma, pero la descripción debe comenzar o finalizar con la frase en inglés "This service is available in &lt;idioma del contenido de la oferta>". También puede proporcionar documentos complementarios en un idioma distinto al utilizado en la descripción de la oferta.

Para facilitar la creación de la oferta, prepare algunos de estos elementos con antelación. Los elementos siguientes son obligatorios a menos que se indique lo contrario.

**Name**: este nombre aparecerá como título de la descripción de la oferta en el marketplace comercial. El nombre puede ser una marca comercial. No puede contener emojis (a menos que sean símbolos de marca comercial y copyright), y no puede tener más de 50 caracteres. El nombre debe incluir el tipo de servicio y la duración de la oferta para maximizar la optimización del motor de búsqueda (SEO). El formato requerido es *Nombre: duración + tipo*. No incluya el nombre de su empresa, a menos que sea también el nombre del producto. Estos son algunos ejemplos:

|No diga |Diga . |
|---|---|
|Getting Started with Azure IoT in Manufacturing |Manufacturing IoT: Evaluación de 2 días |
|Workshop on Smart Toasters |Smart Toasters: Taller de 1 semana |
|Prueba de concepto de migración de SQL Server de Contoso |SQL Migration: 3-Wk Proof of Concept |
| | |

**Resumen de los resultados de la búsqueda**: Describa el propósito u objetivo de la oferta en 200 caracteres como máximo. Este resumen se usa en los resultados de búsqueda de la oferta del marketplace comercial. No debe ser idéntico al título. Considere la posibilidad de incluir las palabras clave principales para SEO.

**Descripción**: esta descripción se mostrará en la oferta del marketplace comercial. Considere la posibilidad de incluir una propuesta de valor, ventajas clave, base de usuarios prevista y cualquier categoría o asociación de sector.

Al escribir la descripción, siga estos criterios según el tipo de servicio:

|Tipo de servicio |Requisitos de la descripción |
|---|---|
|Evaluación |Incluya una agenda detallada para las evaluaciones que duran más de un día y explique lo que el cliente puede esperar. |
|Sesión informativa |Explique lo que el cliente puede esperar.|
|Implementación |Incluya una agenda detallada para las implementaciones que duran más de un día y describa los cambios de ingeniería, los artefactos técnicos o de otro tipo que los clientes puedan esperar como resultado de la interacción. |
|Prueba de concepto |Describa los cambios de ingeniería, los artefactos técnicos o de otro tipo que los clientes puedan esperar como resultado de la interacción. |
|Taller |Incluya una agenda diaria, semanal o mensual detallada en función de la duración elegida para la oferta. Explique cuáles son los objetivos de aprendizaje y otros resultados del taller. |
| | |

Estas son algunas sugerencias para escribir la descripción:

* Describa claramente la propuesta de valor de la oferta en las primeras frases, incluidas:
    * Tipo de usuario que se beneficia de la oferta.
    * Necesidades o problemas del cliente que resuelve la oferta.
* Recuerde que estas primeras frases podrían mostrarse en los resultados de la búsqueda.
* Si el precio de la oferta es una estimación, explique qué variables determinarán el precio final.
* Use vocabulario específico del sector.

Puede usar etiquetas HTML para dar formato a la descripción. En este cuadro puede escribir hasta 2000 caracteres de texto, incluidos los espacios y las etiquetas HTML. Para obtener información sobre el formato HTML, consulte el artículo sobre las [etiquetas HTML admitidas en las descripciones de las ofertas del marketplace comercial](./supported-html-tags.md).

**Palabras clave de búsqueda** (opcional): escriba al menos tres palabras clave de búsqueda que los clientes puedan usar para buscar su oferta en las tiendas en línea. No es necesario incluir el **nombre** y **descripción** de la oferta.

**Duración**: su oferta de servicio de consultoría debe tener una duración predeterminada de hasta 12 meses.

**Información de contacto**: en el Centro de partners, se le pedirá que proporcione el nombre, la dirección de correo electrónico y el número de teléfono de dos personas de la empresa (usted puede ser uno de los dos contactos). Usaremos esta información para comunicarnos con usted sobre la oferta. Esta información no se muestra a los clientes, pero se proporcionará a los asociados de Proveedor de soluciones en la nube (CSP).

**Documentos complementarios**: cargue al menos un documento PDF y hasta tres orientados al cliente que proporcionen información sobre su oferta. Por ejemplo, podrían ser notas del producto o folletos.

**Elementos multimedia: logotipos**: proporcione un archivo PNG para el logotipo de tamaño grande. El Centro de partners lo usará para crear un logotipo pequeño. Opcionalmente, puede reemplazar el logotipo pequeño por una imagen diferente.

* Grande (de 216 x 216 a 350 x 350 píxeles, obligatorio)
* Pequeño (48 x 48 píxeles, opcional)

El logotipo grande aparece en la página de la descripción de la oferta en Azure Marketplace o AppSource. El logotipo pequeño aparece en los resultados de la búsqueda de Azure Marketplace o en la página principal y en la página de resultados de búsqueda de AppSource.

Siga estas instrucciones para los logotipos:

* Asegúrese de que la imagen no esté borrosa ni extendida.
* El diseño de Azure tiene una paleta de colores simple. Utilice pocos colores primarios y secundarios en el logotipo.
* Si usa un fondo transparente, asegúrese de que el logotipo y el texto no son blancos, negros ni azules.
* Evite los degradados en el logotipo o en el fondo. No coloque texto en el logotipo, ni siquiera el nombre de su empresa o de la marca.

**Elementos multimedia de Marketplace: capturas de pantalla**: Agregue al menos una imagen y hasta cinco que sean demostrativas de la oferta. Todas las imágenes deben tener el tamaño 1280 x 720 píxeles y estar en formato PNG.

**Elementos multimedia de marketplace: vídeos** (opcional): puede agregar hasta cuatro vídeos que muestren la oferta. Los vídeos deben estar hospedados en YouTube o Vimeo y deben tener una miniatura (archivo PNG de 1280 x 720).

## <a name="pricing-and-availability"></a>Precios y disponibilidad

La oferta de servicio de consultoría puede estar disponible en uno o varios países o regiones. En el Centro de partners, puede decidir el precio de cada mercado que seleccione. Para obtener una lista completa de los mercados y las monedas admitidos, consulte [Disponibilidad geográfica y compatibilidad con monedas del marketplace comercial](./marketplace-geo-availability-currencies.md).


## <a name="next-steps"></a>Pasos siguientes

* [Creación de una oferta de servicios de consultoría en el marketplace comercial](./create-consulting-service-offer.md)
* [Procedimientos recomendados para la publicación de ofertas](./gtm-offer-listing-best-practices.md)
