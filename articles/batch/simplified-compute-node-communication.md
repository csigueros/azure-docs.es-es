---
title: Uso de la comunicación simplificada de nodo de proceso
description: Obtenga información sobre cómo el servicio Azure Batch simplifica la forma en que se administra la infraestructura de grupos de Batch y cómo participar o no.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 9bd53bcab9ccb9403d8d851300ba987a23eacc11
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270106"
---
# <a name="use-simplified-compute-node-communication"></a>Uso de la comunicación simplificada de nodo de proceso

Un grupo de Azure Batch contiene uno o varios nodos de ejecución que ejecutan cargas de trabajo especificadas por el usuario en forma de tareas de Batch. Para habilitar la funcionalidad de Batch y la administración de la infraestructura de grupos de Batch, los nodos de ejecución se deben comunicar con el servicio Azure Batch.

En este documento se describen los próximos cambios en la comunicación del servicio Azure Batch con los nodos de ejecución de grupos de Batch, los cambios de configuración de red que pueden ser necesarios y cómo optar por que las cuentas de Batch participen o no en la nueva característica simplificada de comunicación de nodos de ejecución durante el período de versión preliminar pública.

> [!IMPORTANT]
> La compatibilidad con la comunicación simplificada de nodos de ejecución en Azure Batch está actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

No es necesario participar en este momento. Pero en el futuro, la comunicación simplificada de nodos de ejecución será necesaria para todas las cuentas de Batch. En ese momento, se proporcionará un aviso de retirada oficial, con la oportunidad de migrar los grupos de Batch antes de que esto suceda.

## <a name="compute-node-communication-changes"></a>Cambios en la comunicación de nodos de ejecución

El servicio Azure Batch simplifica la forma en que la infraestructura de grupos de Batch se administra en nombre de los usuarios. El nuevo método de comunicación reduce la complejidad y el ámbito de las conexiones de red entrantes y salientes necesarias en las operaciones de línea de base.

En los grupos de Batch de cuentas que no han optado por la comunicación simplificada de nodos de ejecución se necesitan las siguientes reglas de red en grupos de seguridad de red (NSG), rutas definidas por el usuario (UDR) y firewalls al [crear un grupo en una red virtual](batch-virtual-network.md):

- Entrada:
  - Puertos de destino 29876, 29877 mediante TCP de BatchNodeManagement.*región*

- Salida:
  - Puerto de destino 443 mediante TCP a Storage.*región*
  - Puerto de destino 443 mediante TCP a BatchNodeManagement.*región* para determinadas cargas de trabajo que necesitan comunicarse con el servicio Batch, como tareas del Administrador de trabajos

Con el nuevo modelo, en los grupos de Batch de cuentas que usan la comunicación simplificada de nodos de ejecución se necesitan las siguientes reglas de red en grupos de seguridad de red, UDR y firewalls:

- Entrada:
  - Ninguno

- Salida:
  - Puerto de destino 443 mediante TCP a BatchNodeManagement.*región*

Los requisitos de salida de una cuenta de Batch se pueden detectar mediante la [API List Outbound Network Dependencies Endpoints](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) (Enumerar puntos de conexión de dependencias de red de salida). Esta API notifica el conjunto base de dependencias, en función del modelo de comunicación del grupo de cuentas de Batch. Las cargas de trabajo específicas del usuario pueden necesitar reglas adicionales, como abrir el tráfico a otros recursos de Azure (como Azure Storage para paquetes de aplicación, Azure Container Registry, etc.) o puntos de conexión como el repositorio de paquetes de Microsoft para la funcionalidad de montaje del sistema de archivos virtual.  

## <a name="benefits-of-the-new-model"></a>Ventajas del nuevo modelo

Los usuarios de Azure Batch que [participen en el nuevo modelo de comunicación](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) se benefician de la simplificación de las reglas y las conexiones de red.

La comunicación simplificada de nodos de ejecución ayuda a reducir los riesgos de seguridad al eliminar el requisito de abrir puertos para la comunicación entrante desde Internet. Solo se necesita una regla de salida a una etiqueta de servicio conocida para la operación de línea de base.

El nuevo modelo también proporciona un control de filtración de datos más preciso, ya que la comunicación saliente con Storage.*región* ya no es necesaria. Puede bloquear explícitamente la comunicación saliente a Azure Storage si es necesario para el flujo de trabajo (por ejemplo, en cuentas de almacenamiento de AppPackage, otras cuentas de almacenamiento para archivos de recursos o de salida, u otros escenarios similares).

Incluso si actualmente las cargas de trabajo no se ven afectadas por los cambios (como se describe en la sección siguiente), todavía puede que le interese [participar en el uso de la comunicación simplificada de nodos de ejecución](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication). Esto garantizará que las cargas de trabajo de Batch estén listas para cualquier mejora futura que habilite este modelo.

## <a name="scope-of-impact"></a>Ámbito del impacto

En muchos casos, este nuevo modelo de comunicación no afectará directamente a las cargas de trabajo de Batch. Pero la comunicación simplificada de nodos de ejecución tendrá un impacto en los casos siguientes:

- Los usuarios que especifican Virtual Network como parte de la creación de un grupo de Batch y realizan una o ambas de las siguientes acciones:
   - Deshabilitan explícitamente las reglas de tráfico de red salientes incompatibles con la comunicación simplificada de nodos de ejecución.
   - Usan UDR y reglas de firewall que no son compatibles con la comunicación simplificada de nodos de ejecución.
- Usuarios que habilitan firewalls de software en nodos de ejecución y deshabilitan explícitamente el tráfico saliente en reglas de firewall de software que no son compatibles con la comunicación simplificada de nodos de ejecución.

Si se le aplica alguno de estos casos y quiere participar en la versión preliminar, siga los pasos descritos en la sección siguiente para asegurarse de que las cargas de trabajo de Batch pueden seguir funcionando en el nuevo modelo.

### <a name="required-network-configuration-changes"></a>Cambios necesarios en la configuración de red

Para los usuarios afectados, se necesita el siguiente conjunto de pasos para migrar al nuevo modelo de comunicación:

1. Asegúrese de que la configuración de red de los grupos de Batch (NSG, UDR, firewalls, etc.) incluye una unión de los modelos (es decir, las reglas de red antes de simplificar la comunicación de nodos de ejecución y después). Como mínimo, estas reglas serían las siguientes:
   - Entrada:
     - Puertos de destino 29876, 29877 mediante TCP de BatchNodeManagement.*región*
   - Salida:
     - Puerto de destino 443 mediante TCP a Storage.*región*
     - Puerto de destino 443 mediante TCP a BatchNodeManagement.*región*
1. Si tiene otros escenarios de entrada o salida necesarios para el flujo de trabajo, tendrá que asegurarse de que las reglas reflejan estos requisitos.
1. [Participe en la comunicación simplificada de nodos de ejecución](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) como se describe a continuación.
1. Use una de las opciones siguientes a fin de actualizar las cargas de trabajo para usar el nuevo modelo de comunicación. Con independencia del método que use, tenga en cuenta que los grupos sin direcciones IP públicas no se ven afectados y actualmente no pueden usar la comunicación simplificada de nodos de ejecución. Vea la sección [Limitaciones actuales](#current-limitations).
   1. Cree grupos y compruebe que funcionan correctamente. Migre la carga de trabajo a los nuevos grupos y elimine los grupos anteriores.
   1. Cambie el tamaño de todos los grupos existentes a cero nodos y vuelva a escalar horizontalmente.
1. Después de confirmar que todos los grupos anteriores se han eliminado o escalado a cero, y se han escalado horizontalmente, consulte la [API List Outbound Network Dependencies Endpoints](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) para confirmar que no existe ninguna regla de salida para Azure Storage para la región (excepto las cuentas de almacenamiento automático si están vinculadas a la cuenta de Batch).
1. Como mínimo, modifique toda la configuración de red aplicable a las reglas de comunicación simplificada de nodos de API (tenga en cuenta las reglas adicionales necesarias como se ha descrito anteriormente):
   - Entrada:
     - Ninguno
   - Salida:
     - Puerto de destino 443 mediante TCP a BatchNodeManagement.*región*

Si sigue estos pasos, pero más adelante quiere dejar de usar la comunicación simplificada de nodos de ejecución, deberá hacer lo siguiente:

1. [No participe en la comunicación simplificada de nodos de ejecución](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) como se describe a continuación.
1. Migre la carga de trabajo a grupos nuevos, o bien cambie el tamaño de los grupos existentes y vuelva a escalar horizontalmente (vea el paso 4 anterior).
1. Confirme que todos los grupos ya no usan la comunicación simplificada de nodos de ejecución mediante la [API List Outbound Network Dependencies Endpoints](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints). Debería ver una regla de salida para Azure Storage para la región (independientemente de las cuentas de almacenamiento automático vinculadas a la cuenta de Batch).

## <a name="opt-your-batch-account-in-or-out-of-simplified-compute-node-communication"></a>Participación o no participación de la cuenta de Batch en la comunicación simplificada de nodos de ejecución

Para que una cuenta de Batch participe o no en la comunicación simplificada de nodos de ejecución, [cree una solicitud de soporte técnico en Azure Portal](../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!IMPORTANT]
> Al participar (o no participar) en la comunicación simplificada de nodos de ejecución, el cambio solo afecta al comportamiento futuro. Los grupos de Batch que contengan nodos de ejecución distintos de cero creados antes de la solicitud no se verán afectados y usarán el modelo que estuviera activo antes de la solicitud. Vea los pasos de migración para obtener más información sobre cómo migrar los grupos existentes con independencia de la participación.

Use las opciones siguientes al crear la solicitud.

:::image type="content" source="media/simplified-compute-node-communication/support-request-opt-in.png" alt-text="Captura de pantalla de una solicitud de soporte técnico para participar en la comunicación simplificada de nodos de ejecución.":::

1. En **Tipo de problema**, seleccione **Técnico**.
1. En **Tipo de servicio**, seleccione **Servicio de Batch**.
1. En **Recurso**, seleccione la cuenta de Batch para esta solicitud.
1. En **Resumen**:
   - Para participar, escriba "Habilitar la comunicación simplificada de nodos de ejecución".
   - Para no participar, escriba "Deshabilitar la comunicación simplificada de nodos de ejecución".
1. En **Tipo de problema**, seleccione **Cuentas de Batch**.
1. En **Subtipo de problema**, seleccione **Other issues with Batch Accounts** (Otros problemas con cuentas de Batch).
1. Seleccione **Siguiente** y de nuevo **Siguiente** ir a la página **Detalles adicionales**.
1. En **Detalles adicionales**, puede especificar opcionalmente que quiere habilitar todas las cuentas de Batch de la suscripción o de varias suscripciones. Si lo hace, asegúrese de incluir aquí los identificadores de suscripción.
1. Realice cualquier otra selección necesaria en la página y, después, seleccione **Siguiente**.
1. Revise los detalles de la solicitud y, después, seleccione **Crear** para crear la solicitud de soporte técnico.

Una vez que se haya enviado la solicitud, se le notificará cuando la cuenta participe (o no).

## <a name="current-limitations"></a>Limitaciones actuales

Las siguientes son limitaciones conocidas para las cuentas que participan en la comunicación simplificada de nodos de ejecución:

- Actualmente no se admite la [creación de grupos sin direcciones IP públicas](batch-pool-no-public-ip-address.md) para las cuentas que participan.
- Los grupos creados previamente sin direcciones IP públicas no usarán la comunicación simplificada de nodos de ejecución, incluso si la cuenta de Batch participa.
- [Las cuentas de Batch](private-connectivity.md) privadas pueden participar en la comunicación simplificada de nodos de ejecución, pero los grupos de Batch creados por estas cuentas de Batch deben tener direcciones IP públicas para poder usarla.
- Los grupos de configuración del servicio en la nube no se admiten actualmente para la comunicación simplificada de nodos de ejecución y, por lo general, están en desuso. Se recomienda usar Configuración de la máquina virtual para los grupos de Batch. Para más información, consulte [Migración de la configuración del grupo de Batch de Cloud Services a máquina virtual](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre los [grupos en redes virtuales](batch-virtual-network.md).
- Obtenga información sobre cómo [crear un grupo con direcciones IP públicas especificadas](create-pool-public-ip.md).
