---
title: Configuración de ofertas privadas de ISV a revendedor en la nube en el Centro de partners de Microsoft
description: Configure ofertas privadas de ISV a revendedor en la nube en el Centro de partners de Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emerb19
ms.author: emerb
ms.date: 10/07/2021
robots: noindex
ms.openlocfilehash: bb663f42be1e7d1c798420ef483cf52c34c84aff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048299"
---
# <a name="isv-to-cloud-reseller-private-offers"></a>Ofertas privadas de ISV a revendedor en la nube

> [!IMPORTANT]
> Esta característica actualmente está en su versión preliminar pública.

## <a name="overview"></a>Información general

Las ofertas privadas permiten a los proveedores de software independientes (ISV) y a los revendedores en la nube (asociados de CSP) aumentar sus ingresos mediante la creación de márgenes personalizados con límite de tiempo que se adaptan a las necesidades empresariales de cada entidad.

Como ISV, puede especificar el margen y la duración para crear un precio mayorista para el asociado de CSP. Cuando el asociado realiza una venta a un cliente, Microsoft le realizará sus pagos con el precio mayorista.

Como asociado de CSP, puede descubrir todos los márgenes disponibles mediante la interfaz de usuario o la API del Centro de partners. Para cualquier venta que realice, recibirá la factura de Microsoft al precio mayorista. Seguirá estableciendo el precio al cliente y facturando al cliente fuera de Marketplace. Obtenga información sobre la experiencia del asociado de CSP para ofertas privadas en [Descubrir los márgenes configurados por los ISV](/partner-center/csp-commercial-marketplace-margins).

:::image type="content" source="media/isv-csp-cloud/isv-private-offer-experience.png" alt-text="Muestra la progresión de la experiencia de ofertas privadas de ISV.":::

:::image type="content" source="media/isv-csp-cloud/csp-private-offer-experience.png" alt-text="Muestra la progresión de la experiencia de ofertas privadas de CSP.":::

> [!NOTE]
> Si solo publica la oferta en Microsoft AppSource (por lo que no está disponible mediante Azure Marketplace) y opta por que sea vendida por los asociados del programa Proveedor de soluciones en la nube (CSP), puede haber un retraso con la presentación de la oferta para que los asociados de CSP puedan comprar en su portal. Póngase en contacto con el [equipo de soporte técnico](./support.md) si cree que la oferta no está disponible para que el asociado de CSP realice transacciones.

## <a name="prerequisites-to-create-a-private-offer-for-cloud-resellers"></a>Requisitos previos para crear una oferta privada para revendedores en la nube

Debe cumplir estos requisitos previos para crear una oferta privada para revendedores en la nube:

1. Ha creado una cuenta de Marketplace comercial en el Centro de partners.
2. La cuenta está inscrita en el programa Marketplace comercial.
3. Ha publicado una oferta de transacción y disponible públicamente para Azure Marketplace.
4. La oferta ha optado por participar en el canal de revendedores en la nube.
5. Está creando una oferta privada para un revendedor en la nube que forma parte del programa Proveedor de soluciones en la nube (CSP) de Microsoft.

### <a name="supported-offer-types"></a>Tipos de oferta admitidos

Se pueden crear ofertas privadas para todos los tipos de ofertas de Marketplace para las que se puedan realizar transacciones. Esto incluye SaaS, Azure Virtual Machines y Aplicaciones de Azure.

> [!NOTE]
> Los márgenes se aplican a todas las dimensiones de medidor personalizadas que puede usar la oferta. Los márgenes solo se aplican a los cargos de software establecidos por usted, no a los cargos de hardware de la infraestructura de Azure asociada.

## <a name="private-offers-dashboard"></a>Panel de ofertas privadas

El panel **Private offers** (Ofertas privadas) del menú de navegación izquierdo del Centro de partners es la ubicación centralizada para crear y administrar ofertas privadas. Este panel tiene dos pestañas:

1. **Customers** (Clientes): información sobre las ofertas privadas para clientes una vez que la característica entre en versión preliminar privada en otoño de 2021.
2. **Cloud Resellers** (Revendedores en la nube): abre el panel de la oferta privada para revendedores en la nube, que le permite:

    - Crear nuevas ofertas privadas
    - Ver el estado de todas sus ofertas privadas
    - Clonar ofertas privadas existentes
    - Retirar ofertas privadas
    - Eliminar ofertas privadas

## <a name="create-a-new-private-offer-for-cloud-resellers"></a>Crear una nueva oferta privada para revendedores en la nube

1. Inicie sesión en el Centro de datos.
2. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
3. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
4. Seleccione **+ New Private offer** (+ Nueva oferta privada).
5. Escriba el nombre de la oferta privada. Se trata de un nombre descriptivo que usará para hacer referencia a la oferta privada dentro del Centro de partners. Este nombre no será visible para los revendedores en la nube.  

### <a name="offer-setup"></a>Configuración de la oferta

La página de configuración de la oferta le permite definir los términos de la oferta privada, el contacto de notificación, los precios y los revendedores en la nube.

1. **Private offer terms** (Términos de la oferta privada) determina la duración durante la cual los revendedores en la nube pueden descubrir y vender su oferta privada.

    - Para que la oferta privada comience inmediatamente, elija "As soon as possible" (Lo antes posible) para **Start Date** (Fecha de inicio). Si se cumplen todos los requisitos previos, la oferta privada estará disponible en un plazo de 15 minutos después de enviarla. Si una oferta privada se extiende a un cliente existente de un producto de pago por uso, el precio privado se aplicará durante todo el mes.
    - Para que la oferta privada comience en un mes venidero, seleccione **Specific month** (Mes específico) y haga una selección. La fecha de inicio de esta opción siempre será la primera del mes.
    - Elija el mes en el campo **End date** (Fecha de finalización) de la oferta privada. Siempre será la última fecha del mes.

2. Proporcione hasta cinco correos electrónicos en **Notification Contacts** (Contactos para notificaciones) para recibir actualizaciones por correo electrónico sobre el estado de la oferta privada. Estos correos electrónicos se envían cuando la oferta privada cambia a **Activa**, **Finalizada** o **Retirada**.

3. Configure un porcentaje de margen en **Pricing** (Precios) para hasta 10 ofertas o planes en una oferta privada. El margen que recibe el revendedor en la nube será un porcentaje del precio de lista del plan en Marketplace.

    - Seleccione **+ Add Offers/plans** (+ Agregar ofertas y planes) para elegir las ofertas y planes para los que desea proporcionar una oferta privada.
    - Elija esta opción para proporcionar una oferta privada en el nivel de oferta (todos los planes actuales y futuros de esa oferta tendrán un precio privado asociado) o en el nivel de plan (solo el plan seleccionado tendrá un precio privado asociado).
    - Elija hasta 10 ofertas o planes y, a continuación, seleccione **Add** (Agregar).
    - Escriba el porcentaje de margen que se proporcionará a los revendedores en la nube. El margen que proporcione se calculará como un porcentaje del precio de lista del plan en Marketplace.

    > [!NOTE]
    > Solo las ofertas o planes para las que se pueden realizar transacciones en Microsoft AppSource o Azure Marketplace aparecen en el menú de selección.

4. Seleccione los **revendedores en la nube** a los que autoriza para vender su oferta privada.

    1. Seleccione **+ Add Cloud Resellers** (+ Agregar revendedores en la nube).
    2. Busque el revendedor en la nube por nombre o identificador de inquilino. O bien, busque mediante la aplicación de filtros como regiones, aptitudes o competencias.
    3. Elija los revendedores en la nube y seleccione **Add** (Agregar).

    > [!NOTE]
    > - Solo puede seleccionar revendedores en la nube que formen parte del programa Proveedor de soluciones en la nube (CSP) de Microsoft. 
    > - Una vez que finaliza la oferta privada, los revendedores en la nube que autorice pueden seguir vendiendo la oferta de Marketplace al precio de lista.
    > - Las ofertas privadas se pueden ampliar a un máximo de 400 inquilinos de revendedores en la nube.

### <a name="review-and-submit"></a>Revisión y envío

Esta página es donde puede revisar toda la información que ha proporcionado.

Una vez enviadas, las ofertas privadas no se pueden modificar. Asegúrese de que la información sea precisa.

Cuando esté listo, haga clic en **Submit** (Enviar). Se le redirigirá al panel, donde podrá ver el estado de la oferta privada. Los contactos de notificación recibirán un correo electrónico una vez que la oferta privada esté activa.

## <a name="view-private-offers-status"></a>Visualización del estado de las ofertas privadas

Para ver el estado de la ofertas privada:

1. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
2. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
3. Examine la columna **Estado**.

El estado de la oferta privada será uno de los siguientes:

- **Borrador**: ha iniciado el proceso de creación de una oferta privada, pero aún no la ha enviado.
- **En curso**: ha enviado una oferta privada y actualmente se está publicando en nuestros sistemas.
- **Activa**: los revendedores en la nube pueden descubrir y realizar transacciones de la oferta privada.
- **Finalizada**: la oferta privada ha expirado o ha superado su fecha de finalización.

## <a name="clone-a-private-offer"></a>Clonación de una oferta privada

La clonación de una oferta privada le ayuda a crear una nueva oferta privada rápidamente.

1. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
2. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
3. Active la casilla de la oferta privada que desea clonar.
4. Seleccione **Clonar**.
5. Escriba el nombre de la nueva oferta privada.
6. Seleccione **Clonar**.
7. Edite los detalles de la página de configuración de la oferta según sea necesario.
8. **Envíe** la nueva oferta privada.

## <a name="withdraw-a-private-offer"></a>Retirada de una oferta privada

Al retirar una oferta privada, los revendedores en la nube ya no recibirán inmediatamente un margen y todas las compras futuras se realizarán al precio de lista.

> [!IMPORTANT]
> Las ofertas privadas solo se pueden retirar si ningún revendedor en la nube la ha vendido a un cliente.

Para retirar una oferta privada:

1. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
2. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
3. Active la casilla de la oferta privada que desea retirar.
4. Seleccione **Withdraw** (Retirar).
5. Seleccione **Request withdraw** (Solicitar retirada).
6. Los contactos de notificación recibirán un correo electrónico si la oferta privada se ha retirado correctamente.

## <a name="delete-a-private-offer"></a>Eliminación de una oferta privada

Para eliminar una oferta privada en estado de borrador:

1. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
2. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
3. Active la casilla de la oferta privada que desea eliminar.
4. Seleccione **Eliminar**.
1. Seleccione **Confirmar**.

## <a name="find-additional-details"></a>Búsqueda de detalles adicionales

Mientras la publicación de la oferta privada está en curso, puede ver detalles adicionales sobre su estado actual:

1. Seleccione **Private offers** (Ofertas privadas) en el menú de navegación izquierdo para abrir el panel.
2. Seleccione la pestaña **Cloud Resellers** (Revendedores en la nube).
3. Seleccione el hipervínculo "In Progress" (En curso) de la oferta privada en la columna **Status** (Estado).

Los detalles adicionales serán uno de los siguientes:

- **Cloud Reseller authorization in progress** (Autorización del revendedor en la nube en curso): actualmente estamos autorizando al revendedor de nube especificado para que pueda vender su oferta.
- **Private offer publish in progress** (Publicación de la oferta privada en curso): actualmente estamos publicando el precio privado del revendedor en la nube determinado.
- **Live** (Activa): la oferta privada ahora está activa para este revendedor en la nube.

## <a name="reporting-on-private-offers"></a>Informes sobre ofertas privadas

El importe del pago y la tarifa de agencia que cobra Microsoft se basan en el precio después de aplicar el margen para los elementos de línea que tienen un margen asociado.

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes](./isv-csp-faq.yml) sobre la configuración de ofertas privadas de ISV a revendedor en la nube