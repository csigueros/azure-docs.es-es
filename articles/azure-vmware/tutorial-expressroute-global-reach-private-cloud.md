---
title: Emparejamiento de entornos locales con Azure VMware Solution
description: Aprenda a emparejar ExpressRoute Global Reach con una nube privada en Azure VMware Solution.
ms.topic: tutorial
ms.custom: contperf-fy21q4, contperf-fy22q1
ms.date: 07/28/2021
ms.openlocfilehash: b930aab15ef9af8e43919af1671f44c20e6b2075
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730231"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>Emparejamiento de entornos locales con Azure VMware Solution

Después de implementar la nube privada de Azure VMware Solution, la conectará al entorno local. Global Reach de ExpressRoute conecta su entorno local y la nubes privada de Azure VMware Solution. La conexión de Global Reach de ExpressRoute se establece entre el circuito ExpressRoute de la nube privada y una conexión existente de ExpressRoute con los entornos locales. 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Diagrama que muestra la conectividad de red local de ExpressRoute Global Reach." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

>[!NOTE]
>Puede conectarse mediante VPN, pero ese tema no se detalla en esta guía de inicio rápido.

En este artículo, hará lo siguiente:

> [!div class="checklist"]
> * Creación de una clave de autorización de ExpressRoute en el circuito local de ExpressRoute
> * Emparejamiento de la nube privada con el circuito ExpressRoute local
> * Comprobación de la conectividad de red local

Cuando haya terminado, siga los pasos recomendados al final para continuar con los pasos de esta guía de introducción.

## <a name="prerequisites"></a>Requisitos previos

- Revise la documentación sobre cómo [habilitar la conectividad en distintas suscripciones de Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

- Un circuito de ExpressRoute independiente y funcional utilizado para conectar entornos locales con Azure, que es el _circuito 1_ a efectos de emparejamiento.

- Asegúrese de que todas las puertas de enlace, incluido el servicio del proveedor de ExpressRoute, admitan un número de sistema autónomo (ASN) de 4 bytes. Azure VMware Solution utiliza ASN públicos de 4 bytes para anunciar rutas.

>[!NOTE]
>Si anuncia una ruta predeterminada a Azure (0.0.0.0/0), asegúrese de que se anuncia una ruta más específica que contenga las redes locales además de la ruta predeterminada para habilitar el acceso de administración a Azure VMware Solution. La red de administración de Azure VMware Solution descartará una única ruta 0.0.0.0/0 para garantizar un funcionamiento correcto del servicio.

## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>Creación de una clave de autorización de ExpressRoute en el circuito local de ExpressRoute

El propietario del circuito crea una autorización, que crea una clave de autorización que puede usar un usuario del circuito para conectar sus puertas de enlace de red virtual al circuito ExpressRoute. Una autorización solo es válida para una conexión.

> [!NOTE]
> Cada conexión requiere una autorización independiente.

1. En la hoja **Circuitos de ExpressRoute**, en Configuración, seleccione **Autorizaciones**.

1. Escriba el nombre de la clave de autorización y seleccione **Guardar**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Seleccione Autorizaciones y escriba el nombre de la clave de autorización.":::

   Una vez creada, la nueva clave aparece en la lista de claves de autorización del circuito.

1. Copie la clave de autorización y el identificador de ExpressRoute. Los usará en el paso siguiente para completar el emparejamiento.

## <a name="peer-private-cloud-to-on-premises"></a>Emparejamiento de una nube privada con el entorno local 
Ahora que ha creado una clave de autorización para el circuito ExpressRoute de la nube privada, puede emparejarlo con el circuito ExpressRoute local. El emparejamiento se lleva a cabo desde el circuito local de ExpressRoute en **Azure Portal**. Utilizará el identificador de recurso (id. de circuito de ExpressRoute) y la clave de autorización del circuito ExpressRoute de la nube privada para finalizar el emparejamiento.

1. En la nube privada, en Administrar, seleccione **Conectividad** > **ExpressRoute Global Reach** > **Agregar**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Captura de pantalla que muestra la pestaña Global Reach de ExpressRoute en la nube privada de Azure VMware Solution.":::

1. Escriba el identificador de ExpressRoute y la clave de autorización creada en la sección anterior.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo para especificar la información de conexión.":::   

1. Seleccione **Crear**. La nueva conexión se muestra en la lista de conexiones de la nube local.

>[!TIP]
>Para eliminar o desconectar una conexión de la lista, seleccione **Más**.  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Captura de pantalla que muestra cómo desconectar o eliminar una conexión local en Azure VMware Solution.":::


## <a name="verify-on-premises-network-connectivity"></a>Comprobación de la conectividad de red local

En el **enrutador perimetral local**, ahora debería ver el lugar donde ExpressRoute conecta los segmentos de red NSX-T y los segmentos de administración de Azure VMware Solution.

>[!IMPORTANT]
>Todos los usuarios tienen un entorno diferente, aunque algunos deberán permitir que estas rutas se propaguen de nuevo a la red local.  

## <a name="next-steps"></a>Pasos siguientes
Continúe con el siguiente tutorial para instalar el complemento VMware HCX en la nube privada de Azure VMware Solution.

> [!div class="nextstepaction"]
> [Instalación de VMware HCX](install-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
