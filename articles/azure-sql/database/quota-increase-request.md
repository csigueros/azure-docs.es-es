---
title: Solicitar un aumento de cuota
description: En esta página se describe cómo crear una solicitud de soporte técnico para aumentar las cuotas de Azure SQL Database y las instancias administradas de Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 06/04/2020
ms.openlocfilehash: a5530becdcbe150ddc7ab2608bdf964ac269ed99
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409026"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Solicitud de aumentos de cuota para Azure SQL Database y la instancia administrada de SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

En este artículo se explica cómo solicitar un aumento de cuota para Azure SQL Database y la instancia administrada de Azure SQL. También se explica cómo habilitar el acceso de la suscripción a una región y cómo solicitar la habilitación de hardware específico en una región.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Creación de una solicitud de soporte técnico

Use los pasos siguientes para crear una nueva solicitud de soporte técnico desde Azure Portal para SQL Database.

1. En el menú de [Azure Portal](https://portal.azure.com), seleccione **Ayuda y soporte técnico**.

   ![El vínculo de Ayuda y soporte técnico](./media/quota-increase-request/help-plus-support.png)

1. Seleccione **Ayuda y soporte técnico** y **Nueva solicitud de soporte técnico**.

    ![Creación de una solicitud de soporte técnico](./media/quota-increase-request/new-support-request.png)

1. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .

   ![Selección de un tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. En **Suscripción**, seleccione la suscripción cuya cuota quiere aumentar.

   ![Selección de una suscripción para el aumento de cuota](./media/quota-increase-request/select-subscription-support-request.png)

1. En **Tipo de cuota**, seleccione uno de los siguientes tipos de cuota:

   - **SQL Database** para las cuotas de bases de datos únicas y grupos elásticos.
   - **Instancia administrada de SQL Database** para las instancias administradas.

   Después, seleccione **Next: Soluciones >>** .

   ![Selección de un tipo de cuota](./media/quota-increase-request/select-quota-type.png)

1. En la ventana **Detalles**, seleccione **Escribir detalles** para especificar información adicional.

   ![Vínculo a Proporcionar detalles](./media/quota-increase-request/provide-details-link.png)

Cuando hace clic en **Proporcionar detalles** aparece la ventana **Detalles de la cuota**, que le permite agregar información adicional. En las secciones siguientes se describen las distintas opciones de tipos de cuota de **SQL Database** y **SQL Managed Instance**.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> Tipos de cuota de SQL Database

En las secciones siguientes se describen las opciones de aumento de cuota para los tipos de cuota de **SQL Database**:

- Unidades de transacción de base de datos (DTU) por servidor
- Servidores por suscripción
- Acceso de las regiones a suscripciones o hardware específico

### <a name="database-transaction-units-dtus-per-server"></a>Unidades de transacción de base de datos (DTU) por servidor

Siga estos pasos para solicitar un aumento de las DTU por servidor.

1. Seleccione el tipo de cuota **Unidades de transacción de base de datos (DTU) por servidor**.

1. En la lista **Recurso**, seleccione el recurso de destino.

1. En el campo **Nueva cuota**, escriba el nuevo límite de DTU que solicita.

   ![Detalles de la cuota de DTU](./media/quota-increase-request/quota-details-dtus.png)

Para más información, consulte [Límites de recursos para bases de datos únicas que usan el modelo de compra de DTU](resource-limits-dtu-single-databases.md) y [Límites de recursos para grupos elásticos que utilizan el modelo de compra de DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servidores por suscripción

Siga estos pasos para solicitar un aumento en el número de servidores por suscripción.

1. Seleccione el tipo de cuota **Servidores por suscripción**.

1. En la lista **Ubicación**, seleccione la región de Azure que se va a usar. La cuota es por suscripción en cada región.

1. En el campo **Nueva cuota**, escriba la solicitud del número máximo de servidores de esa región.

   ![Detalles de la cuota de servidores](./media/quota-increase-request/quota-details-servers.png)

Para más información, consulte [Límites y regulación de recursos de SQL Database](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Habilitar el acceso de suscripciones a una región

Algunos tipos de oferta no están disponibles en todas las regiones. Puede ver un error como el siguiente:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Si su suscripción requiere acceso en una región determinada, seleccione la opción **Acceso a la región**. En la solicitud, especifique los detalles de la oferta y la SKU que desea habilitar para la región. Para explorar las opciones de ofertas y SKU, consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Seleccione el tipo de cuota **Acceso a la región**.

1. En la lista **Seleccionar una ubicación**, seleccione la región de Azure que se va a usar. La cuota es por suscripción en cada región.

1. Especifique los detalles de **Modelo de compra** y **Consumo esperado**.

   ![Solicitar acceso a la región](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Solicitud de habilitación de hardware específico en una región

Si alguna generación de hardware que desee usar no está disponible en su región, siga estos pasos para solicitarla. Para obtener más información sobre las generaciones de hardware y la disponibilidad regional, vea [Generaciones de hardware para SQL Database](./service-tiers-sql-database-vcore.md#hardware-generations) o [Generaciones de hardware para SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations).

1. Seleccione el tipo de cuota **Otra solicitud de cuota**.

1. En el campo **Descripción**, especifique la solicitud, incluido el nombre de la generación de hardware y el nombre de la región en la que lo necesita.

   ![Solicitar hardware en una nueva región](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Enviar la solicitud

El paso final consiste en rellenar los detalles restantes de la solicitud de cuota de SQL Database. Después, seleccione **Next: Revisar y crear>>** y, después de revisar los detalles de la solicitud, haga clic en **Crear** para enviar la solicitud.

## <a name="next-steps"></a>Pasos siguientes

Una vez que se envía la solicitud, esta será revisada. Recibirá una respuesta en función de la información que haya proporcionado en el formulario.

Para más información sobre otros límites de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
