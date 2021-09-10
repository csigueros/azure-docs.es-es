---
title: Conexión de varias nubes privadas de Azure VMware Solution en la misma región (versión preliminar)
description: Aprenda a crear una conexión de red entre dos o más nubes privadas de Azure VMware Solution ubicadas en la misma región.
ms.topic: how-to
ms.date: 07/02/2021
ms.openlocfilehash: 562980ce52216f892cb10689206211e23f3c8886
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586732"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region-preview"></a>Conexión de varias nubes privadas de Azure VMware Solution en la misma región (versión preliminar)

La característica **AVS Interconnect** (Interconexión de AVS) le permite crear una conexión de red entre dos o más nubes privadas de Azure VMware Solution ubicadas en la misma región. Crea un vínculo de enrutamiento entre las redes de administración y carga de trabajo de las nubes privadas para permitir la comunicación de red entre las nubes.

Puede conectar una nube privada a varias nubes privadas, ya que las conexiones no son transitivas. Por ejemplo, si la _nube privada 1_ está conectada a la _nube privada 2_ y la _nube privada 2_ está conectada a la _nube privada 3_, las nubes privadas 1 y 3 no se comunicarán hasta que se conecten directamente.

Solo puede conectar nubes privadas de la misma región. Para conectar nubes privadas de regiones diferentes, [use Global Reach de ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) para conectar las nubes privadas de la misma manera que conecta la nube privada al circuito local. 

>[!IMPORTANT]
>La característica AVS Interconnect (Preview) (Interconexión de AVS [versión preliminar]) se encuentra actualmente en versión preliminar pública.  
>Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
>Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regiones admitidas

La característica AVS Interconnect (Preview) (Interconexión de AVS [versión preliminar]) está disponible en todas las regiones excepto en Centro-sur de EE. UU. (SAT20), Norte de Europa (DUB21), Sudeste Asiático (SG2) y Oeste de Reino Unido (CWL20). 

## <a name="prerequisites"></a>Requisitos previos

- Acceso de escritura a cada nube privada que vaya a conectar.
- El espacio de direcciones IP enrutado en cada nube debe ser único y no se puede superponer.

>[!NOTE]
>La característica **AVS interconnect** (Interconexión de AVS) no comprueba si hay espacio IP superpuesto de la manera en que lo hace el emparejamiento nativo de red virtual de Azure antes de crear el emparejamiento. Por lo tanto, es su responsabilidad asegurarse de que no haya superposición entre las nubes privadas.
>
>En entornos de Azure VMware Solution, es posible configurar implementaciones de IP superpuestas y no enrutadas en segmentos NSX que no se enrutan a Azure.  Estas implementaciones no provocan problemas con la característica de interconexión de AVS, ya que solo se realiza el enrutamiento entre el NSX T0 de cada nube privada.


## <a name="add-connection-between-private-clouds"></a>Adición de conexión entre las nubes privadas

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Conectividad**.

2. Seleccione la pestaña **AVS Interconnect** (Interconexión de AVS) y, luego, **Agregar**.

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="Captura de pantalla que muestra la pestaña AVS Interconnect (Interconexión de AVS) en Conectividad." border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. Seleccione la información y la nube privada de Azure VMware Solution de la nueva conexión.

   >[!NOTE]
   >Solo puede conectarse a nubes privadas de la misma región. Para conectar nubes privadas de regiones diferentes, [use Global Reach de ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) para conectar las nubes privadas de la misma manera que conecta la nube privada al circuito local. 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="Captura de pantalla que muestra la información necesaria para agregar una conexión a otra nube privada." border="true":::


4. Active la casilla **I confirm** (Confirmo) para confirmar que no hay espacios IP enrutados superpuestos en las dos nubes privadas. 

5. Seleccione **Crear**.  Puede comprobar el estado después de crear la conexión.

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="Captura de pantalla que muestra la información de notificación para la conexión en curso y una conexión existente." border="true":::

   Verá todas las conexiones en **AVS Private Cloud** (Nube privada de AVS).
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="Captura de pantalla que muestra la pestaña AVS Interconnect (Interconexión de AVS) en Conectividad y dos conexiones de nube privada establecidas." border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>Eliminación de la conexión entre nubes privadas

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Conectividad**.

2. Vaya a la conexión que quiere quitar, a la derecha, y seleccione **Eliminar** (papelera); luego, elija **Sí**.


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha conectado varias nubes privadas de la misma región, puede que quiera obtener información sobre:

- [Traslado de recursos de Azure VMware Solution a otra región](move-azure-vmware-solution-across-regions.md)
- [Movimiento de una suscripción de Azure VMware Solution a otra](move-ea-csp-subscriptions.md)
