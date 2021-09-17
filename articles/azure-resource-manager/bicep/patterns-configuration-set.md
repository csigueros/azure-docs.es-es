---
title: Patrón de conjunto de configuración
description: Se describe el patrón de conjunto de configuración.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: b2ccac6f646304d7d530e616a57c8490e17aba22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446711"
---
# <a name="configuration-set-pattern"></a>Patrón de conjunto de configuración

En lugar de definir una gran cantidad de parámetros individuales, cree conjuntos predefinidos de valores. Durante la implementación, seleccione el conjunto de valores que se usará.

## <a name="context-and-problem"></a>Contexto y problema

Un único archivo Bicep suele definir muchos recursos. Es posible que cada recurso tenga que usar una configuración diferente en función del entorno en el que se va a implementar. Por ejemplo, puede compilar un archivo Bicep que implemente un plan y una aplicación de App Service, así como una cuenta de almacenamiento. Cada uno de estos recursos tiene varias opciones que afectan a su costo, disponibilidad y resistencia. En entornos de producción, quiere usar un conjunto de configuración que dé prioridad a la alta disponibilidad y resistencia. En entornos que no son de producción, quiere usar un conjunto de configuración diferente que dé prioridad a la reducción de costos.

Puede crear parámetros para cada opción de configuración, pero esto tiene algunas desventajas:

- Este enfoque crea una carga para los usuarios de la plantilla, ya que necesitan comprender los valores que se van a usar para cada recurso y el impacto de establecer cada parámetro.
- El número de parámetros de la plantilla aumenta con cada nuevo recurso que define.
- Los usuarios pueden seleccionar combinaciones de valores de parámetro que no se han probado o que no funcionarán correctamente.

## <a name="solution"></a>Solución

Cree un único parámetro para especificar el tipo de entorno. Use una variable para seleccionar automáticamente la configuración de cada recurso en función del valor del parámetro.

> [!NOTE]
> Este enfoque a veces se denomina _dimensionamiento de camiseta_. Al comprar una camiseta, no se obtienen muchas opciones para su longitud, ancho, mangas, etc. Basta con elegir entre tamaños pequeños, medianos y grandes, y el diseñador de camisetas ha predefinido esas medidas en función de ese tamaño.

## <a name="example"></a>Ejemplo

Supongamos que tiene una plantilla que se puede implementar en dos tipos de entorno: no producción y producción. Según el tipo de entorno, la configuración que necesita es diferente:

| Propiedad | Entornos que no son de producción | Entornos de producción |
|-|-|-|
| **plan de App Service** |
| Nombre de SKU | S2 | P2V3 |
| Capacidad (número de instancias) | 1 | 3 |
| **Aplicación de App Service** |
| Always On | Disabled | habilitado |
| **Cuenta de almacenamiento** |
| Nombre de SKU | Standard_LRS | Standard_ZRS |

Puede usar el patrón de conjunto de configuración para esta plantilla.

Acepte un único parámetro que indique el tipo de entorno, como producción o no producción. Use el decorador de parámetros `@allowedValues` para asegurarse de que los usuarios de la plantilla solo proporcionen los valores que espera:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

A continuación, cree una _variable de mapa_, que es un objeto que define la configuración específica en función del tipo de entorno. Observe que la variable tiene dos objetos denominados `Production` y `NonProduction`. Estos nombres coinciden con los valores permitidos para el parámetro en el ejemplo anterior:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

Al definir los recursos, use el mapa de configuración para definir las propiedades del recurso:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>Consideraciones

- En la variable de mapa, considere la posibilidad de agrupar las propiedades por recurso para simplificar su definición.
- En la variable de mapa, puede definir ambos valores de propiedad individuales (como la propiedad `alwaysOn` en el ejemplo) o variables de objeto que establecen una propiedad de objeto (como las propiedades de SKU del ejemplo).
- Considere la posibilidad de usar un conjunto de configuración con [condiciones de recursos](conditional-resource-deployment.md). Esto permite que el código de Bicep implemente determinados recursos para entornos específicos y no en otros.

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre el patrón de archivo de variables compartido.](patterns-shared-variable-file.md)
