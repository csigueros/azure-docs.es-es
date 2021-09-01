---
title: Creación de una oferta de servicio administrado en Azure Marketplace
description: Cree una oferta de servicio administrado para Azure Marketplace
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/12/2021
ms.openlocfilehash: 0d5c178010012546bfcf786be7106f980289d6eb
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204488"
---
# <a name="create-a-managed-service-offer-for-the-commercial-marketplace"></a>Creación de una oferta de servicio administrado en el marketplace comercial

En este artículo se explica cómo crear una oferta de servicio administrado para el marketplace comercial de Microsoft mediante el Centro de partners.

Para publicar una oferta de servicio administrado, debe haber obtenido una competencia Gold o Silver de Microsoft en la plataforma en la nube. Si aún no lo ha hecho, lea la sección [Planeamiento de una oferta de servicio administrado para el marketplace comercial](./plan-managed-service-offer.md). Le ayudará a preparar los recursos que necesitará al crear la oferta en el Centro de partners.

## <a name="create-a-new-offer"></a>Crear una nueva oferta

1. Inicie sesión en el [Centro de partners](https://partner.microsoft.com/dashboard/home).
2. En el menú de navegación izquierdo, seleccione **Marketplace comercial** > **Información general**.
3. En la pestaña Información general, seleccione **+ Nueva oferta** > **Servicio administrado**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Muestra el menú de navegación izquierdo.":::

4. En el cuadro de diálogo **Nueva oferta**, escriba un valor para **Id. de oferta**. Se trata de un identificador único para cada oferta de su cuenta. Este identificador es visible en la dirección URL de la oferta del marketplace comercial y de las plantillas de Azure Resource Manager, si procede. Por ejemplo, si escribe test-offer-1 en este cuadro, la dirección web de la oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    - Cada oferta de su cuenta debe tener un identificador único.
    - Use solo letras minúsculas y números. Puede incluir guiones y caracteres de subrayado, pero no espacios, y está limitado a 50 caracteres.
    - El identificador de oferta no se puede cambiar después de seleccionar **Crear**.

5. Escriba un **Alias de la oferta**. Este es el nombre que se usa para la oferta en el Centro de partners. No se puede ver en las tiendas en línea y es diferente del nombre de oferta que se muestra a los clientes.
6. Para generar la oferta y continuar, seleccione **Crear**.

## <a name="setup-details"></a>Detalles de la configuración

Esta sección no se aplica a este tipo de oferta.

## <a name="customer-leads"></a>Clientes potenciales

Conecte el sistema de administración de relaciones con clientes (CRM) con la oferta del marketplace comercial para que pueda recibir la información de contacto de un cliente cuando este muestre interés en el servicio de consultorías. Esta conexión se puede modificar en cualquier momento durante la creación de la oferta o con posterioridad a esta. Para obtener instrucciones detalladas, vea [Clientes potenciales a partir de la oferta en el marketplace comercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Para configurar la administración de clientes potenciales en el Centro de partners:

1. En el Centro de partners, vaya a la pestaña **Configuración de la oferta**.
2. En **Clientes potenciales**, seleccione el vínculo **Conectar**.
3. En el cuadro de diálogo **Detalles de conexión**, seleccione un destino de cliente potencial de la lista.
4. Rellene los campos que aparecen. Para más información, consulte los siguientes artículos:

    - [Configuración de la oferta para enviar clientes potenciales a la tabla de Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    - [Configuración de la oferta para enviar clientes potenciales a Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (antes Dynamics CRM Online)
    - [Configuración de la oferta para enviar clientes potenciales al punto de conexión HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    - [Configuración de la oferta para enviar clientes potenciales al Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    - [Configurar la oferta para enviar clientes potenciales a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Para validar la configuración proporcionada, seleccione la opción **Validate link** (Validar vínculo).
6. Cuando haya configurado los detalles de conexión, seleccione **Conectar**.
7. Seleccione **Guardar borrador**.

Después de enviar su oferta para publicarla en el Centro de partners, se validará la conexión y se le enviará un cliente potencial de prueba. Mientras obtiene una vista previa de la oferta antes de publicarla, pruebe la conexión de los clientes potenciales cuando tratan de adquirir la oferta en el entorno de versión preliminar.

> [!TIP]
> Asegúrese de que la conexión con el destino de clientes potenciales permanece actualizada para no perder ningún cliente potencial.

Seleccione **Guardar borrador** antes de pasar a la siguiente pestaña: **Propiedades**.

## <a name="next-step"></a>Paso siguiente

- Configuración de [propiedades](create-managed-service-offer-properties.md) de la oferta