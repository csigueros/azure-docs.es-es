---
title: Información sobre los procedimientos recomendados para el desarrollo de archivos Bicep
description: Describe las prácticas que deben seguirse al crear archivos Bicep para que su funcionamiento sea correcto y sean fáciles de mantener.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 7dae6525b457c544c98c59ffd1195f9ec31e2ae7
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540852"
---
# <a name="best-practices-for-bicep"></a>Procedimientos recomendados para Bicep

En este artículo se recomiendan los procedimientos que deben seguirse al desarrollar archivos Bicep. Con estos procedimientos, es más fácil entender y usar los archivos Bicep.

## <a name="parameters"></a>Parameters

* Use una buena nomenclatura para las declaraciones de parámetros. Los nombres adecuados permiten que las plantillas se lean y comprendan fácilmente. Asegúrese de usar nombres claros y descriptivos que sean coherentes con la nomenclatura.

* Piense detenidamente en los parámetros que usa la plantilla. Intente usar parámetros para la configuración que cambia entre las implementaciones. Las variables y los valores codificados de forma rígida se pueden usar para la configuración que no cambia entre las implementaciones.

* Tenga en cuenta los valores predeterminados que use. Asegúrese de que cualquier usuario pueda implementarlos sin problema. Por ejemplo, considere la posibilidad de usar SKU y planes de tarifa de bajo costo para que alguien que implemente la plantilla en un entorno de prueba no incurra en un costo elevado innecesariamente.

* Use el decorador `@allowed` con moderación. Si lo usa demasiado, puede bloquear las implementaciones válidas. Dado que los servicios de Azure agregan instancias de SKU y contribuyen al aumento del tamaño, puede que la lista de permitidos no esté actualizada. Por ejemplo, permitir solo SKU Premium v3 puede tener sentido en producción, pero le impide usar la misma plantilla en entornos que no sean de producción.

* Es conveniente proporcionar descripciones de los parámetros. Intente que las descripciones sean útiles y proporcione información importante sobre lo que la plantilla necesita que sean los valores de parámetros.

  También puede usar comentarios de tipo `//` para incluir información.

* Puede colocar las declaraciones de parámetros en cualquier lugar del archivo de plantilla, aunque normalmente es conveniente colocarlas en la parte superior del archivo para que el código Bicep sea fácil de leer.

* Se recomienda especificar la longitud de caracteres mínima y máxima para los parámetros que controlan la nomenclatura. Estas limitaciones ayudan a evitar errores más adelante, durante la implementación.

Para obtener más información sobre los parámetros de Bicep, consulte [Parámetros en Bicep](parameters.md).

## <a name="variables"></a>Variables

* Use mayúsculas y minúsculas combinadas para los nombres de variables, como `myVariableName`.

* Cuando defina una variable, no es necesario el [tipo de datos](data-types.md). Las variables deducen el tipo del valor de resolución.

* Puede usar las funciones de Bicep para crear una variable.

* Después de definir una variable en el archivo Bicep, se hace referencia al valor mediante el nombre de la variable.

Para obtener más información sobre las variables de Bicep, consulte [Variables de Bicep](variables.md).

## <a name="naming"></a>Nomenclatura

* La [función uniqueString()](bicep-functions-string.md#uniquestring) es útil para crear nombres de recursos únicos globalmente. Cuando se proporcionan los mismos parámetros, devuelve siempre la misma cadena. Pasar el identificador del grupo de recursos significa que la cadena es la misma en cada implementación para el mismo grupo de recursos, pero es distinta cuando se implementa en diferentes grupos de recursos o suscripciones.

* A veces, la función `uniqueString()` crea cadenas que comienzan por un número. Algunos recursos de Azure, como las cuentas de almacenamiento, no permiten que sus nombres comiencen por números. Esto significa que es una buena idea usar la interpolación de cadenas para crear nombres de recursos. Puede agregar un prefijo a la cadena única.

* A menudo, es una buena idea usar expresiones de plantilla para crear nombres de recursos. Muchos tipos de recursos de Azure tienen reglas sobre los caracteres permitidos y la longitud de sus nombres. Incorporar la creación de nombres de recursos en la plantilla significa que cualquier persona que use la plantilla no tiene que acordarse de seguir estas reglas.

## <a name="resource-definitions"></a>Definiciones de recursos

* En lugar de insertar expresiones complejas directamente en las propiedades de los recursos, use variables para que incluyan las expresiones. Este enfoque hacen que el archivo Bicep sea más fácil de leer y entender. Evita colapsar las definiciones de recursos con lógica.

* Pruebe a usar las propiedades de recursos como salidas, en lugar de realizar suposiciones sobre cómo se comportarán los recursos. Por ejemplo, si necesita enviar la dirección URL a una aplicación de App Service, use la propiedad defaultHostname de la aplicación en lugar de crear una cadena para la dirección URL por su cuenta. A veces, estas suposiciones no son correctas en entornos diferentes o los recursos cambian la forma en la que funcionan. Es más seguro que el propio recurso le indique sus propiedades.

* Es recomendable usar una versión de API reciente para cada recurso. Las nuevas características de los servicios de Azure a veces solo están disponibles en las versiones de API más recientes.

* Cuando sea posible, evite usar las funciones [reference](./bicep-functions-resource.md#reference) y [resourceId](./bicep-functions-resource.md#resourceid) en el archivo Bicep. Puede acceder a cualquier recurso de Bicep mediante el nombre simbólico. Por ejemplo, si define una cuenta de almacenamiento con el nombre simbólico toyDesignDocumentsStorageAccount, puede acceder a su identificador de recurso mediante la expresión `toyDesignDocumentsStorageAccount.id`. Al usar el nombre simbólico, se crea una dependencia implícita entre los recursos.

* Si el recurso no está implementado en el archivo Bicep, todavía puede obtener una referencia simbólica al recurso mediante la palabra clave `existing`.

## <a name="child-resources"></a>Recursos secundarios

* Evite anidar demasiadas capas. El anidamiento excesivo dificulta la lectura y el uso del código Bicep.

* Es mejor evitar la creación de nombres de recursos para los recursos secundarios. Las ventajas que Bicep proporciona se perderán cuando comprenda las relaciones entre los recursos. Use la propiedad `parent` o el anidamiento en su lugar.

## <a name="outputs"></a>Outputs

* Asegúrese de que no crea salidas para datos confidenciales. Cualquier persona que tenga acceso al historial de implementación puede acceder a los valores de salida. Estas personas no son aptas para la administración de los secretos.

* En lugar de pasar valores de propiedad a través de salidas, use la palabra clave `existing` para buscar propiedades de recursos que ya existen. Es un procedimiento recomendado buscar claves de otros recursos de esta manera en lugar de pasarlas por las salidas. Siempre se obtienen los datos más actualizados.

Para obtener más información sobre las salidas de Bicep, consulte [Salidas en Bicep](outputs.md).

## <a name="tenant-scopes"></a>Ámbitos de inquilino

No se pueden crear directivas ni asignaciones de roles en el [ámbito del inquilino](deploy-to-tenant.md). Sin embargo, si necesita conceder acceso o aplicar directivas en toda la organización, puede implementar estos recursos en el grupo de administración raíz.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Bicep, consulte el [inicio rápido de Bicep](quickstart-create-bicep-use-visual-studio-code.md).
* Para obtener información sobre las partes de un archivo Bicep, consulte [Nociones sobre la estructura y la sintaxis de los archivos Bicep](file.md).
