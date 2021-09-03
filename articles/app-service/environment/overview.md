---
title: Información general sobre App Service Environment
description: Información general sobre App Service Environment
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 07/05/2021
ms.author: ccompy
ms.custom: references_regions
ms.openlocfilehash: d08645c3250490935c11fb983208a30d426dcce0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723036"
---
# <a name="app-service-environment-overview"></a>Información general sobre App Service Environment 
> [!NOTE]
> Este artículo trata sobre App Service Environment v3, que se usa con planes de App Service v2 aislados.
> 

Azure App Service Environment es una característica de Azure App Service que proporciona un entorno completamente aislado y dedicado para ejecutar de forma segura las aplicaciones de App Service a gran escala. Esta funcionalidad puede hospedar sus:

- Aplicaciones web de Windows
- Aplicaciones web de Linux
- Contenedores de Docker
- Functions

Las instancias de App Service Environment (ASE) son adecuadas para cargas de trabajo de aplicaciones que necesitan:

- Gran escala.
- Aislamiento y acceso a redes seguro
- Alta utilización de memoria
- Muchas solicitudes por segundo (RPS). Puede crear varias instancias de ASE en una o varias regiones de Azure. Esta flexibilidad hace que las instancias de ASE sean perfectas para aplicaciones sin estado de escalado horizontal con un requisito de RPS elevado.

Los ASE hospedan aplicaciones de un solo cliente y lo hacen en una de sus redes virtuales. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de la aplicación. Las aplicaciones pueden establecer conexiones seguras a alta velocidad por redes virtuales a los recursos corporativos locales.

## <a name="usage-scenarios"></a>Escenarios de uso

App Service Environment tiene muchos casos de uso, incluidos:

- Aplicaciones de línea de negocio internas
- Aplicaciones que necesitan más de 30 instancias de ASP
- Sistema de un solo inquilino para satisfacer los requisitos internos de seguridad o cumplimiento
- Hospedaje de aplicaciones aisladas de red
- Aplicaciones de múltiples niveles

Hay numerosas características de red que permiten que las aplicaciones de App Service multiinquilino lleguen a recursos aislados de red o se aíslen de la red por sí mismas. Estas características se habilitan en el nivel de aplicación.  Con una instancia de ASE, no se requiere configuración adicional alguna en las aplicaciones para que estén en la red virtual. Las aplicaciones se implementan en un entorno con aislamiento de red que ya está en una red virtual. En la parte superior del ASE que hospeda las aplicaciones con aislamiento de red, también es un sistema de un solo inquilino. No hay otros clientes que usen el ASE. Si realmente necesita un caso de aislamiento completo, también puede implementar el ASE en hardware dedicado. 

## <a name="dedicated-environment"></a>Entorno dedicado

Una instancia de ASE es una implementación de un solo inquilino de Azure App Service que se ejecuta en la red virtual. 

Las aplicaciones se hospedan en planes de App Service, que se crean en una instancia de App Service Environment. Un plan de App Service es básicamente un perfil de aprovisionamiento para un host de aplicación. A medida que se escale el plan de App Service, se crearán más hosts de aplicación con todas las aplicaciones de ese plan de App Service en cada host. Un solo ASEv3 puede tener un total de hasta 200 instancias de un plan de App Service en todos los planes de App Service combinados. Un único plan de App Service v2 aislado puede tener hasta 100 instancias por sí solo. 

## <a name="virtual-network-support"></a>Compatibilidad con redes virtuales

La característica ASE es una implementación de Azure App Service en una única subred de la red virtual (VNet) de Azure Resource Manager de un cliente. Al implementar una aplicación en un ASE, esta se expone en la dirección de entrada asignada al ASE. Si el ASE se implementa con una dirección VIP interna, la dirección de entrada de todas las aplicaciones será una dirección en la subred de ASE. Si el ASE se implementa con una dirección VIP externa, la dirección de entrada será una dirección que lleva a Internet y las aplicaciones estarán en un espacio DNS público. 

El número de direcciones que ASEv3 usa en la subred variará en función del número de instancias que tenga junto con la cantidad de tráfico. Hay roles de infraestructura que se escalan automáticamente en función del número de planes de App Service y de la carga. El tamaño recomendado para la subred de ASEv3 es un bloque CIDR /24 con 256 direcciones, ya que puede hospedar un ASEv3 escalado horizontalmente hasta su límite.

Las aplicaciones de un ASE no requieren que se habilite ninguna característica para acceder a los recursos de la misma red virtual en la que se encuentra el ASE. Si la red virtual del ASE está conectada a otra red, las aplicaciones del ASE pueden acceder a los recursos de esas redes extendidas. La configuración del usuario puede bloquear el tráfico en la red. 

La versión multiinquilino de Azure App Service incluye numerosas características que permiten a las aplicaciones conectarse a las distintas redes. Esas características de red permiten que las aplicaciones actúen como si estuvieran implementadas en una red virtual. Las aplicaciones de una instancia de ASEv3 no requieren configuración alguna para estar en la red virtual. Una de las ventajas de usar un ASE a través del servicio multiinquilino es que cualquier control de acceso de red para las aplicaciones hospedadas en el ASE es totalmente externo a la configuración de la aplicación. Con las aplicaciones del servicio multiinquilino, debe habilitar las características aplicación por aplicación y usar RBAC o una directiva para evitar cambios de configuración. 

## <a name="feature-differences"></a>Diferencias de características

ASEv3 tiene algunas diferencias en comparación con las versiones anteriores de ASE. Con ASEv3:

- No existen dependencias de red en la red virtual del cliente. Puede proteger todos los datos entrantes y salientes como considere oportuno. El tráfico saliente también puede enrutarse según sus preferencias. 
- La implementación puede habilitarse para la redundancia de zona. Esta última solo puede establecerse durante la creación de ASEv3 y únicamente en regiones en las que todas las dependencias de ASEv3 cuenten con redundancia de zona. 
- Puede implementarse en un grupo host dedicado. Las implementaciones de grupos host no tienen redundancia de zona. 
- El escalado es mucho más rápido que con ASEv2. Aunque el escalado todavía no es inmediato como en el servicio multiinquilino, es mucho más rápido.
- Ya no se requieren ajustes de escalado de front-end. Los servidores front-end de ASEv3 se escalan automáticamente para satisfacer las necesidades y se implementan en hosts mejorados. 
- El escalado ya no bloquea otras operaciones de escala de la instancia de ASEv3. Solo puede haber una operación de escala en vigor para una combinación de sistema operativo y tamaño. Por ejemplo, mientras se escala un plan de App Service pequeño de Windows, puede iniciar una operación de escalado para que se ejecute al mismo tiempo en uno mediano de Windows o en cualquier otro que no sea pequeño de Windows. 
- Puede accederse a las aplicaciones de una instancia de ASEv3 VIP interna a través del emparejamiento global. Esto no era posible con ASEv2. 

Algunas características que estaban disponibles en versiones anteriores de ASE no están disponibles en ASEv3. En ASEv3, no se puede:

- enviar tráfico SMTP. Puede tener alertas que se desencadenen por correo electrónico, pero la aplicación no puede enviar tráfico saliente en el puerto 25
- implementar las aplicaciones con FTP
- usar la depuración remota con las aplicaciones
- actualizar desde ASEv2, todavía
- supervisar el tráfico con Network Watcher o un flujo de NSG
- configurar un enlace TLS/SSL basado en IP con las aplicaciones

## <a name="pricing"></a>Precios 

ASEv3 aplica un modelo de precios distinto en función del tipo de implementación de ASE que tenga. A continuación se indican los tres modelos de precios: 

- **ASEv3**: si ASE está vacío, se aplica un cargo como si tuviera un ASP con una instancia de Windows I1v2. El cargo de única instancia no es adicional, solo se aplica si el ASE está completamente vacío.
- **ASEv3 de zona de disponibilidad**: hay un cargo mínimo por nueve instancias de Windows I1v2. No hay ningún cargo adicional por compatibilidad con zonas de disponibilidad si tiene nueve o más instancias de plan de App Service. 
- **ASEv3 de host dedicado**: con una implementación de host dedicado, se le cobran dos hosts dedicados según nuestros precios al crear una instancia de ASEv3 y, a continuación, un pequeño porcentaje de la tarifa Isolated V2 por núcleo a medida que escala.

Los precios de las instancias reservadas para Isolated v2 estarán disponibles después de la disponibilidad general.  

## <a name="regions"></a>Regions

ASEv3 está disponible en las regiones siguientes.

|Regiones ASEv3 de host dedicados y normales|   Regiones ASEv3 de AZ|
|---------------------------------------|------------------|
|Este de Australia|    Este de Australia|
|Sudeste de Australia|Sur de Brasil|
|Sur de Brasil   |Centro de Canadá|
|Centro de Canadá|Centro de EE. UU.|
|Centro de la India  |Este de EE. UU.|
|Centro de EE. UU. |Este de EE. UU. 2|
|Este de Asia  | Centro de Francia|
|Este de EE. UU.    | Centro-oeste de Alemania|
|Este de EE. UU. 2| Norte de Europa|
|Centro de Francia | Centro-sur de EE. UU.|
|Centro-oeste de Alemania   |   Sudeste de Asia|
|Centro de Corea del Sur  | Sur de Reino Unido 2|
|Norte de Europa   | Oeste de Europa|
|Este de Noruega    | Oeste de EE. UU. 2 |
|Norte de Sudáfrica| |  
|Centro-sur de EE. UU.   | |
|Sudeste de Asia| |
|Norte de Suiza  | | 
|Sur de Reino Unido 2| |    
|Oeste de Reino Unido| |
|Centro-Oeste de EE. UU.    | | 
|Oeste de Europa    | |
|Oeste de EE. UU.    | | 
|Oeste de EE. UU. 2| |
