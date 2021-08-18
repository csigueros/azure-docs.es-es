---
title: 'Administración de clientes potenciales en Marketo: marketplace comercial de Microsoft'
description: Aprenda a usar un sistema CRM de Marketo para administrar clientes potenciales de Microsoft AppSource y Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: b0a9aecec9e11a41b22b3f1639f653c14a6e7fce
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285092"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Uso de Marketo para administrar clientes potenciales de marketplace comercial

En este artículo se describe cómo configurar el sistema CRM de Marketo para procesar clientes potenciales de las ofertas en Microsoft AppSource and Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configuración del sistema CRM de Marketo

1. Inicie sesión en Marketo.

1. Seleccione **Design Studio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Seleccione **Nuevo formulario**.

    ![Nuevo formulario de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Rellene los campos obligatorios del cuadro de diálogo **New Form** (Nuevo formulario) y, luego, seleccione **Create** (Crear).

    ![Crear nuevo formulario de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  En la página **Field Details** (Detalles de campo), seleccione **Finish** (Finalizar).

    ![Finalizar formulario de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Apruébelo y ciérrelo.

1. En la pestaña **MarketplaceLeadBackend**, seleccione **Embed Code** (Insertar código). 

    ![Inserción de código de Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Código para insertar de Marketo muestra un código similar al ejemplo siguiente.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copie los valores de los campos siguientes que se muestran en el formulario para insertar código. Usará estos valores para configurar la oferta con el fin de recibir clientes potenciales en el siguiente paso. Use el ejemplo siguiente como guía para obtener los identificadores que necesita del ejemplo de inserción de código de Marketo.

    - Id. de servidor: **ys12**
    - Id. de Munchkin: **123-PQR-789**
    - Id. de formulario: **1179**

    Otra forma de calcular estos valores:

    - El identificador de servidor se encuentra en la instancia de Marketo, por ejemplo, `serverID.marketo.com`.
    - Para obtener el identificador de Munchkin de la suscripción, vaya al menú **Admin** (Administración)  > **Munchkin** en el campo **Munchkin Account ID** (Id. de cuenta de Munchkin); también, lo puede obtener de la primera parte del subdominio del host de la API REST de Marketo: `https://{Munchkin ID}.mktorest.com`.
    - El identificador de formulario es el identificador del formulario de inserción de código que creó en el paso 7 para enrutar los clientes potenciales desde Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configuración de la oferta para enviar clientes potenciales al Marketo

Cuando esté listo para configurar la información de administración de clientes potenciales de la oferta en el portal de publicación, siga estos pasos: 

1. Inicie sesión en el [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165290).

1. Seleccione la oferta y vaya a la pestaña **Configuración de la oferta**.

1. En la sección **Clientes potenciales**, seleccione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Clientes potenciales":::

1. En la ventana emergente **Detalles de la conexión**, seleccione **Marketo** como **destino de clientes potenciales**.

    ![Elección de un destino de clientes potenciales](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Proporcione los valores de **Id. de servidor**, **Munchkin account ID** (Id. de cuenta de Munchkin) e **Id. de formulario**.

    > [!NOTE]
    > Debe terminar de configurar el resto de las partes de la oferta y publicarla para poder recibir clientes potenciales para la oferta. 

1. En **Correo electrónico de contacto**, escriba las direcciones de correo electrónico de las personas de su empresa que deban recibir notificaciones por correo electrónico cuando se reciba un nuevo cliente potencial. Puede proporcionar varias direcciones de correo electrónico separadas con punto y coma.

1. Seleccione **Aceptar**.

   Para asegurarse de que se ha conectado correctamente a un destino de clientes potenciales, seleccione **Validar**. Si la validación es correcta, tendrá un cliente potencial de prueba en el destino de clientes potenciales.

   ![Ventana emergente Detalles de la conexión](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
