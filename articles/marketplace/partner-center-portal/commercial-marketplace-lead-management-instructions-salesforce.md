---
title: 'Administración de clientes potenciales en Salesforce: Marketplace comercial de Microsoft'
description: Aprenda a usar Salesforce para configurar clientes potenciales para Microsoft AppSource y Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: f059e39c97f66d07caa529ec4f6592fd8efe689e
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284930"
---
# <a name="configure-lead-management-for-salesforce"></a>Configuración de la administración de clientes potenciales en Salesforce

En este artículo se describe cómo configurar el sistema de Salesforce para procesar clientes potenciales a partir de las ofertas en Microsoft AppSource y Azure Marketplace.

> [!NOTE]
> Marketplace no admite listas completadas previamente, como una lista de valores para el campo **País**. Asegúrese de que no haya ninguna lista configurada antes de continuar. Como alternativa, puede configurar un [punto de conexión HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o una [tabla de Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para recibir clientes potenciales.

## <a name="set-up-your-salesforce-system"></a>Configurar el sistema de Salesforce

1. Inicie sesión en Salesforce.
1. Vaya a la configuración de **Web-to-Lead** (Web para clientes potenciales). 
    
    Si usa la experiencia Lighting de Salesforce:
    1. Seleccione **Setup** (Configuración) en la página principal de Salesforce.

       ![Configuración de Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. En la página **Setup** (Configuración), vaya a **Platform Tools** > **Feature Settings** > **Marketing** > **Web-to-Lead** (Herramientas de la plataforma > Configuración de características > Marketing > Web para clientes potenciales).

        ![Web para clientes potenciales de Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Si usa la experiencia clásica de Salesforce:

    1. Seleccione **Setup** (Configuración) en la página principal de Salesforce.

       ![Configuración de Salesforce clásico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. En la página **Setup** (Configuración), seleccione **Build** > **Customize** > **Leads** > **Web-to-Lead** (Compilar > Personalizar > Clientes potenciales > Web para clientes potenciales).

        ![Web para clientes potenciales en Salesforce clásico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Los pasos restantes son los mismos en ambas experiencias de Salesforce.

1. En la página **Web-to-Lead Setup** (Configuración de web para clientes potenciales), seleccione el botón **Create Web-to-Lead Form** (Crear un formulario web para clientes potenciales).
1. En **Web-to-Lead Setup** (Configuración de web para clientes potenciales), seleccione **Create Web-to-Lead Form** (Crear formulario web para clientes potenciales).

    ![Web-to-Lead Setup (Configuración de web para clientes potenciales) en Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. En **Create Web-to-Lead Form** (Crear un formulario web para clientes potenciales), asegúrese de que la opción `Include reCAPTCHA in HTML` esté desactivada y seleccione **Generar**.

    ![Panel Create a Web-to-Lead Form (Crear un formulario web para clientes potenciales) en Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Se mostrará texto HTML. Busque el texto "oid" y copie el **valor "oid"** del texto HTML (solo el texto entre comillas) y guárdelo. Pegue este valor en el campo **Identificador de la organización** en el portal de publicación.

    ![Create a Web-to-Lead Form (Crear un formulario web para clientes potenciales) en Salesforce que muestra el valor "oid" HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Seleccione **Finished** (Finalizado).

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurar la oferta para enviar clientes potenciales a Salesforce

Cuando esté listo para configurar la información de administración de clientes potenciales de la oferta en el portal de publicación, siga estos pasos:

1. Inicie sesión en el [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165290).

1. Seleccione la oferta y vaya a la pestaña **Configuración de la oferta**.

1. En la sección **Clientes potenciales**, seleccione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Clientes potenciales":::

1. En la ventana emergente **Detalles de la conexión**, seleccione **Salesforce** como **Destino de clientes potenciales** y, en el campo **Identificador de la organización**, pegue el valor `oid` del formulario web para clientes potenciales que creó.

    ![Ventana emergente Detalles de la conexión: cuadro para validar el correo electrónico de contacto](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. En **Correo electrónico de contacto**, escriba las direcciones de correo electrónico de las personas de su empresa que deban recibir notificaciones por correo electrónico cuando se reciba un nuevo cliente potencial. Puede proporcionar varias direcciones de correo electrónico separadas con un punto y coma.

1. Seleccione **Aceptar**.

Para asegurarse de que se ha conectado correctamente a un destino de clientes potenciales, seleccione **Validar**. Si la validación es correcta, tendrá un cliente potencial de prueba en el destino de clientes potenciales.

>[!NOTE]
>Debe terminar de configurar el resto de las partes de la oferta y publicarla para poder recibir clientes potenciales para la oferta.
