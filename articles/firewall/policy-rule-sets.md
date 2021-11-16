---
title: Conjuntos de reglas de la directiva de Azure Firewall
description: La directiva de Azure Firewall tiene una jerarquía de grupos de colección de reglas, colecciones de reglas y reglas.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/9/2021
ms.author: victorh
ms.openlocfilehash: 5dcb1c84c91e295d7588acccd33ee157937f9fb8
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138683"
---
# <a name="azure-firewall-policy-rule-sets"></a>Conjuntos de reglas de la directiva de Azure Firewall

La directiva de firewall es un recurso de nivel superior que contiene configuración operativa y de seguridad para Azure Firewall. Puede usar la directiva de firewall para administrar conjuntos de reglas que el Azure Firewall usa para filtrar el tráfico. La directiva de firewall organiza, clasifica por orden de prioridad y procesa los conjuntos de reglas en función de una jerarquía con los siguientes componentes: grupos de colección de reglas, colecciones de reglas y reglas.

:::image type="content" source="media/policy-rule-sets/policy-rule-sets.png" alt-text="Jerarquía de conjunto de reglas de Azure Policy":::

## <a name="rule-collection-groups"></a>Grupos de colección de reglas

Un grupo de colección de reglas se usa para agrupar colecciones de reglas. Son la primera unidad que procesa el Azure Firewall y siguen un orden de prioridad basado en valores. Hay tres grupos de colección de reglas predeterminados y sus valores de prioridad están preestablecidos por diseño. Se procesan en el orden siguiente:


|Nombre de grupo de colección de reglas  |Priority  |
|---------|---------|
|Grupo de colección de reglas DNAT (traducción de direcciones de red de destino) predeterminado      |100|
|Grupo de colecciones de reglas de red predeterminado      |200|
|Grupo de colección de reglas de aplicación predeterminado      |300|

Aunque no puede eliminar los grupos de colección de reglas predeterminados ni modificar sus valores de prioridad, puede manipular su orden de procesamiento de una manera diferente. Si necesita definir un orden de prioridad diferente del diseño predeterminado, puede crear grupos de colección de reglas personalizados con los valores de prioridad deseados. En este escenario, no se usan los grupos de colección de reglas predeterminados, solo los que se crean para personalizar la lógica de procesamiento.  

Los grupos de colección de reglas contienen una o varias colecciones de reglas, que pueden ser de tipo DNAT, red o aplicación. Por ejemplo, puede agrupar las reglas que pertenecen a las mismas cargas de trabajo o a una red virtual en un grupo de colección de reglas. 

Los grupos de colección de reglas tienen un tamaño máximo de 2 Mb. Si necesita más de 2 Mb, puede dividir las reglas en varios grupos de colección de reglas. Una directiva de firewall puede contener 50 grupos de colección de reglas.


## <a name="rule-collections"></a>Colecciones de reglas

Una colección de reglas pertenece a un grupo de colección de reglas y contiene una o varias reglas. Son la segunda unidad procesada por el firewall y siguen un orden de prioridad basado en valores. Las colecciones de reglas deben tener una acción definida (permitir o denegar) y un valor de prioridad. La acción definida se aplica a todas las reglas de la colección de reglas. El valor de prioridad determina el orden en que se procesan las colecciones de reglas.
  
Hay tres tipos de colecciones de reglas:

- DNAT
- Red
- Application

Los tipos de colección de reglas deben coincidir con su categoría de grupo de colección de reglas primaria. Por ejemplo, una colección de reglas DNAT solo puede formar parte de un grupo de colección de reglas DNAT.

## <a name="rules"></a>Reglas

Una regla pertenece a una colección de reglas y especifica qué tráfico se permite o se deniega en la red. Son la tercera unidad que procesa el firewall y no siguen un orden de prioridad basado en valores. La lógica de procesamiento de las reglas sigue un enfoque descendente. Todo el tráfico que pasa a través del firewall se evalúa mediante las reglas definidas para una coincidencia de permiso o denegación. Si no hay ninguna regla que permita el tráfico, el tráfico se deniega de manera predeterminada.

En el caso de las reglas de aplicación, la [colección de reglas de infraestructura](infrastructure-fqdns.md) integrada procesa el tráfico antes de que se deniegue de manera predeterminada.

Existen tres tipos de reglas:

- DNAT
- Red
- Application

### <a name="dnat-rules"></a>Reglas DNAT

Las reglas DNAT permiten o deniegan el tráfico entrante a través de las IP públicas del firewall. Puede usar una regla DNAT cuando desee que una IP pública se traduzca en una IP privada. Las IP públicas de Azure Firewall se pueden usar para escuchar el tráfico entrante de Internet, filtrar el tráfico y traducir este tráfico en recursos internos de Azure.

### <a name="network-rules"></a>Reglas de red

Las reglas de red permiten o deniegan el tráfico entrante, saliente y este-oeste de la capa de red (L3) y la capa de transporte (L4).  
Puede usar una regla de red cuando desee filtrar el tráfico en función de las direcciones IP, los puertos y cualquier protocolo.


### <a name="application-rules"></a>Reglas de aplicación

Las reglas de aplicación permiten o deniegan el tráfico entrante, saliente y este-oeste de la capa de aplicación (L7). Puede usar una regla de aplicación cuando desee filtrar el tráfico en función de nombres de dominio completos (FQDN) y protocolos HTTP o HTTPS. 


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información acerca del procesamiento de las reglas de Azure Firewall, consulte el documento [Configuración de las reglas de Azure Firewall](rule-processing.md).
