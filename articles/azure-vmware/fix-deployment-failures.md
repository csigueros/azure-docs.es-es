---
title: Soporte técnico para los errores de implementación o aprovisionamiento de Azure VMware Solution
description: Consiga información de la nube privada de Azure VMware Solution para presentar una solicitud de servicio por errores de implementación o aprovisionamiento de Azure VMware Solution.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 58c7277d19a1b8a512eca0233548ebc388a2c459
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713208"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Apertura de una solicitud de soporte técnico por errores en la implementación o el aprovisionamiento de Azure VMware Solution

En este artículo se muestra cómo abrir una [solicitud de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support) y proporcionar información clave sobre un error de implementación o aprovisionamiento de Azure VMware Solution. 

Si se producen errores en la nube privada, debe abrir una solicitud de soporte técnico en Azure Portal. Para ello, debe obtener antes determinada información clave en Azure Portal:

- Id. de correlación
- Identificador de circuito de Azure ExpressRoute
- Mensajes de error

## <a name="get-the-correlation-id"></a>Obtención del identificador de correlación
 
Cuando crea una nube privada o cualquier otro recurso en Azure, se genera automáticamente un identificador de correlación para ese recurso. Para abrir una solicitud de soporte técnico y resolverla más rápidamente, incluya en ella el identificador de correlación de la nube privada.

En Azure Portal, tiene dos maneras de obtener el identificador de correlación de un recurso:

* Panel **Información general**
* Registros de implementación
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Obtención del identificador de correlación desde la información general del recurso

Este es un ejemplo de los detalles de una operación de implementación de nube privada con errores, donde se muestra el identificador de correlación seleccionado:

:::image type="content" source="media/fix-deployment-failures/failed-private-cloud-deployment.png" alt-text="Captura de pantalla que muestra una implementación de nube privada con errores, con el identificador de correlación seleccionado.":::

Para acceder a los resultados de la implementación en el panel **Información general** de una nube privada:

1. En Azure Portal, seleccione la nube privada.

1. En el menú de la izquierda, seleccione **Información general**.

Una vez iniciada la implementación, sus resultados se muestran en el panel **Información general** de la nube privada.

Copie y guarde el identificador de correlación de la implementación de la nube privada para incluirlo en la solicitud de servicio.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Obtención del identificador de correlación desde el registro de la implementación

Puede obtener el identificador de correlación de una implementación con errores si lo busca en el registro de actividad de la implementación en Azure Portal.

Para acceder al registro de la implementación:

1. En Azure Portal, seleccione la nube privada y, a continuación, seleccione el icono de notificaciones.

   :::image type="content" source="media/fix-deployment-failures/open-notifications.png" alt-text="Captura de pantalla que muestra el icono de notificaciones en Azure Portal.":::

1. En el panel **Notificaciones**, seleccione **Más eventos en el registro de actividad**:

    :::image type="content" source="media/fix-deployment-failures/more-events-in-activity-log.png" alt-text="Captura de pantalla que muestra el vínculo Más eventos en el registro de actividad seleccionado en el panel Notificaciones.":::

1. Para localizar la implementación con errores y su identificador de correlación, busque el nombre del recurso u otros datos usados para crearlo. 

    En el ejemplo siguiente se muestran los resultados de la búsqueda de un recurso de nube privada con el nombre pc03.
 
    :::image type="content" source="media/fix-deployment-failures/find-past-deployments.png" alt-text="Captura de pantalla que muestra los resultados de la búsqueda de un recurso de nube privada de ejemplo y el panel de creación o actualización de una nube privada.":::
 
1. En los resultados de la búsqueda del panel **Registro de actividad**, seleccione el nombre de la operación que produjo el error.

1. En el panel **Create or update a PrivateCloud** (Crear o actualizar una nube privada), seleccione la pestaña **JSON** y, a continuación, busque `correlationId` en el registro que se muestra. Copie el valor de `correlationId` para incluirlo en la solicitud de soporte técnico. 
 
## <a name="copy-error-messages"></a>Copia de mensajes de error

Para ayudar a resolver el problema de implementación, incluya los mensajes de error que se muestren en Azure Portal. Seleccione un mensaje de advertencia para ver un resumen de los errores:
 
:::image type="content" source="media/fix-deployment-failures/summary-of-errors.png" alt-text="Captura de pantalla que muestra los detalles del error en la pestaña Resumen del panel de errores, con el icono de copia seleccionado.":::

Para copiar el mensaje de error, seleccione el icono de copia. Guarde el mensaje copiado para incluirlo en la solicitud de soporte técnico.
 
## <a name="get-the-expressroute-id-uri"></a>Obtención del identificador (URI) de ExpressRoute
 
Quizá intentó escalar o emparejar una nube privada existente con el circuito ExpressRoute de la nube privada y se produjo un error. En ese caso, necesitará el identificador de ExpressRoute para incluirlo en la solicitud de soporte técnico.

Para copiar el identificador de ExpressRoute:

1. En Azure Portal, seleccione la nube privada.
1. En el menú de la izquierda, en **Administrar**, seleccione **Conectividad**. 
1. En el panel derecho, seleccione la pestaña **ExpressRoute**.
1. Seleccione el icono de copia de **Id. de ExpressRoute** y guarde el valor para usarlo en la solicitud de soporte técnico.
 
:::image type="content" source="media/expressroute-global-reach/expressroute-id.png" alt-text="Captura de pantalla que muestra el identificador de ExpressRoute que se copiará en el Portapapeles."::: 
 
## <a name="pre-validation-failures"></a>Errores anteriores a la validación

Si el error se produjo en la comprobación anterior a la validación de la nube privada (antes de la implementación), no se habrá generado ningún identificador de correlación. En este caso, puede incluir la siguiente información en la solicitud de soporte técnico:

- Mensajes de error. Estos mensajes pueden ser útiles en muchos casos de errores, por ejemplo, en problemas relacionados con la cuota. Es importante copiar estos mensajes e incluirlos en la solicitud de soporte técnico, como se describe en este artículo.
- Información utilizada para crear la nube privada de Azure VMware Solution, que incluye:
  - Location
  - Resource group
  - Nombre del recurso

## <a name="create-your-support-request"></a>Creación de una solicitud de soporte técnico

Para obtener información general sobre cómo crear una solicitud de soporte técnico, consulte [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Para crear una solicitud de soporte técnico por errores en la implementación o el aprovisionamiento de Azure VMware Solution, siga estos pasos:

1. En Azure Portal, seleccione el icono **Ayuda** y, a continuación, **Nueva solicitud de soporte técnico**.

    :::image type="content" source="media/fix-deployment-failures/open-support-request.png" alt-text="Captura de pantalla del panel Nueva solicitud de soporte técnico en Azure Portal.":::

1. Escriba o seleccione la información necesaria:

   1. En la pestaña **Aspectos básicos**:

      1. En **Tipo de problema**, seleccione **Configuration and Setup Issues** (Problemas de instalación y configuración).

      1. En **Subtipo de problema**, seleccione **Aprovisionar una nube privada**.

   1. Haga clic en la pestaña **Detalles**:

      1. Escriba o seleccione la información necesaria.

      1. Pegue el identificador de correlación o el identificador de ExpressRoute donde se solicite esta información. Si no ve un cuadro de texto específico para estos valores, péguelos en el cuadro de texto donde se le pide que **proporcione detalles sobre el problema**.

    1. Pegue los detalles de los errores, incluidos los mensajes de error que ha copiado, en el cuadro de texto donde se le pide que **proporcione detalles sobre el problema**.

1. Revise las entradas y, a continuación, seleccione **Crear** para crear la solicitud de soporte técnico.
