---
title: 'Creación de una instancia de Apache Kafka para Confluent Cloud mediante Azure Portal: soluciones de partners de Azure'
description: En este artículo se describe cómo usar Azure Portal para crear una instancia de Apache Kafka para Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9d2326d330dca9111a68edcc81c8c45f7525c1b0
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112521045"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-portal"></a>Inicio rápido: Introducción a Apache Kafka para Confluent Cloud mediante Azure Portal

En está guía de inicio rápido, usará Azure Portal para crear una instancia de Apache Kafka para Confluent Cloud.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una suscripción de Azure activa, cree una [cuenta gratuita](https://azure.microsoft.com/free/).
- Debe tener el rol _Propietario_ o _Colaborador_ para la suscripción de Azure. La integración entre Azure y Confluent solo la pueden configurar los usuarios con acceso de _Propietario_ o _Colaborador_. Antes de comenzar, [confirme que tiene el acceso adecuado](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Búsqueda de la oferta

Use Azure Portal para buscar la aplicación Apache Kafka on Confluent Cloud.

1. En un explorador web, vaya a [Azure Portal](https://portal.azure.com/) e inicie sesión.

1. Si ha visitado el **Marketplace** en una sesión reciente, seleccione el icono entre las opciones disponibles. En caso contrario, busque _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Icono de Marketplace.":::

1. En la página **Marketplace**, tiene dos opciones según el tipo de plan que desee. Puede registrarse para obtener un plan de pago por uso o un plan de compromiso. El plan de pago por uso está disponible públicamente. El plan de compromiso está disponible para los clientes que han sido aprobados para una oferta privada.

   - En el caso de los clientes de **pago por uso**, busque _Apache Kafka on Confluent Cloud_. Seleccione la oferta de Apache Kafka on Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Búsqueda de la oferta de Azure Marketplace.":::

   - Para los clientes con un plan de **compromiso**, seleccione el vínculo **View private offers** (Ver ofertas privadas). El compromiso requiere que se registre para una cantidad mínima de gastos. Utilice esta opción solo si sabe que necesita el servicio durante un tiempo prolongado.

     :::image type="content" source="media/view-private-offers.png" alt-text="Ver ofertas privadas.":::

     Busque _Apache Kafka on Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Selección de la oferta privada.":::

## <a name="create-resource"></a>Crear el recurso

Después de seleccionar la oferta de Apache Kafka on Confluent Cloud, está listo para configurar la aplicación.

1. Si seleccionó ofertas privadas en la sección anterior, tendrá dos opciones para los tipos de planes:

    - Confluent Cloud - Pay-as-you-go (Confluent Cloud: pago por uso)
    - Commitment - for commit plan (Compromiso: para un plan de compromiso)

   Si no seleccionó ofertas privadas, solo tendrá la opción de pago por uso.

   Elija el plan que va a usar y seleccione **Set up + subscribe** (Configurar y suscribirse).

    :::image type="content" source="media/setup-subscribe.png" alt-text="Configurar y suscribirse.":::

1. En la página Basic (Básico) de **Create Confluent Cloud Resource** (Crear recurso de Confluent Cloud), proporcione los valores siguientes. Cuando haya terminado, seleccione **Next: Etiquetas**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formulario para configurar el recurso de Confluent Cloud.":::

    | Propiedad | Descripción |
    | ---- | ---- |
    | **Suscripción** | En el menú desplegable, seleccione la suscripción de Azure en la que se va a realizar la implementación. Debe tener acceso de _Propietario_ o _Colaborador_. |
    | **Grupos de recursos** | Especifique si desea crear un grupo de recursos o utilizar uno existente. Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. Para más información, consulte [Información general del grupo de recursos de Azure](../../azure-resource-manager/management/overview.md). |
    | **Confluent organization name** (Nombre de la organización de Confluent) | Asigne un nombre al recurso de software como servicio (SaaS). |
    | **Región** | En el menú desplegable, seleccione una de estas regiones: <br/><br/> Este de Australia, Centro de Canadá, Centro de EE. UU., Este de EE. UU., Este de EE. UU. 2, Centro de Francia, Norte de Europa, Sudeste Asiático, Sur de Reino Unido, Centro-oeste de EE. UU., Oeste de Europa, Oeste de EE. UU. 2 |
    | **Plan** | Seleccione **Pay as you go** (Pago por uso) o **Commitment** (Compromiso). |
    | **Billing term** (Período de facturación) | Se rellena previamente según el plan de facturación seleccionado. |
    | **Precio** | Se rellena previamente según el plan de Confluent seleccionado. |

1. En **Tags** (Etiquetas), proporcione los pares de **nombre** y **valor** de las etiquetas que desea aplicar a los recursos. Después de escribir las etiquetas, seleccione **Review + Create** (Revisar y crear).

    :::image type="content" source="media/setup-tags.png" alt-text="Agregar etiquetas de proyecto.":::

1. Revise la configuración que ha proporcionado. Cuando esté preparado, seleccione **Guardar**.

1. Se tardan unos minutos en crear el recurso. Puede ver el estado de implementación en **Notifications** (Notificaciones). Una vez finalizada la implementación, seleccione el recurso para ver la página **Overview** (Información general).

    :::image type="content" source="media/deployment-status.png" alt-text="Estado de la implementación.":::

   Si recibe un error, consulte [Solución de problemas de las soluciones de Apache Kafka para Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración del recurso de Confluent Cloud](manage.md)
