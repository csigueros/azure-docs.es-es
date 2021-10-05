---
title: Migración de recursos de Azure y plantillas de ARM JSON para usar Bicep
description: Aquí se describe el flujo de trabajo recomendado a la hora de migrar recursos de Azure y plantillas de ARM JSON para usar Bicep.
author: joshuawaddell
ms.author: jowaddel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: e9ec55ac41cb30a902b337184cc75a4f976700bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699824"
---
# <a name="migrate-to-bicep"></a>Migración a Bicep

La definición de los recursos de Azure en Bicep ofrece una serie de ventajas que incluyen: sintaxis más sencilla, modularización, administración automática de dependencias, validación de tipos e IntelliSense, y una experiencia de creación mejorada.

Si tiene plantillas de Azure Resource Manager (plantillas de ARM) JSON o recursos implementados y quiere migrarlos de forma segura a Bicep, se sugiere seguir un flujo de trabajo recomendado que consta de cinco fases:

:::image type="content" source="./media/migrate/five-phases.png" alt-text="Diagrama de las cinco fases para migrar recursos de Azure a Bicep: conversión, migración, refactorización, prueba e implementación." border="false":::

El primer paso del proceso es capturar los recursos de Azure como un archivo JSON, si aún no hay ninguno. Luego descompile el archivo JSON en un archivo de Bicep inicial, que se mejora mediante la refactorización. Cuando tenga un archivo de trabajo, pruebe e implemente mediante un proceso que minimice el riesgo de cambios importantes en el entorno de Azure.

:::image type="content" source="./media/migrate/migrate-bicep.png" alt-text="Diagrama del flujo de trabajo recomendado para migrar recursos de Azure a Bicep." border="false":::

En este artículo se resume este flujo de trabajo recomendado. Para obtener instrucciones detalladas, vea [Migración de recursos de Azure y plantillas de ARM JSON para usar Bicep](/learn/modules/migrate-azure-resources-bicep/) en Microsoft Learn.

## <a name="phase-1-convert"></a>Fase 1: Conversión

En la fase de _conversión_ de la migración de los recursos a Bicep, el objetivo es capturar una representación inicial de los recursos de Azure. El archivo de Bicep que se crea en esta fase está incompleto y no está listo para usarse, pero el archivo le proporciona un punto de partida para la migración.

La fase de conversión consta de dos pasos, que se completan en secuencia:

1. **Captura una representación JSON de los recursos de Azure.** Si tiene una plantilla JSON existente y la va a convertir a Bicep, el primer paso es sencillo: ya tiene la plantilla de origen. Si va a convertir recursos de Azure implementados mediante el portal u otra herramienta, debe *exportar* las definiciones de recursos y luego convertirlas a Bicep. Puede usar los cmdlets de Azure Portal, la CLI de Azure y Azure PowerShell para exportar recursos únicos, varios recursos y grupos de recursos completos.

1. **Convierta la representación JSON a Bicep mediante el comando _decompile_.** [Las herramientas de Bicep incluyen el comando `decompile` para convertir plantillas.](decompile.md) Puede invocar el comando `decompile` desde la CLI de Azure o desde la CLI de Bicep. El proceso de descompilación es un proceso de máximo esfuerzo y no garantiza una asignación completa de JSON a Bicep. Es posible que deba revisar el archivo de Bicep generado para cumplir con los procedimientos recomendados de su plantilla antes de usar el archivo para implementar recursos.

## <a name="phase-2-migrate"></a>Fase 2: Migrar

En la fase de _migración_ de los recursos a Bicep, el objetivo es crear el primer borrador del archivo de Bicep implementable y asegurarse de que define todos los recursos de Azure que están en el ámbito de la migración.

La fase de migración consta de tres pasos, que se completan en secuencia:

1. **Creación de un archivo de Bicep nuevo vacío.** Es recomendable crear un nuevo archivo de Bicep. El archivo que creó en la fase de _conversión_ es un punto de referencia que puede consultar, pero no debe tratarlo como una versión final ni implementarlo tal como está.

1. **Copia de los recursos de la plantilla descompilada.** Copie cada recurso del archivo de Bicep convertido al nuevo archivo de Bicep individualmente. Este proceso le ayuda a resolver los problemas por recurso y a evitar confusiones a medida que la plantilla aumenta de tamaño.

1. **Identifique y vuelva a crear los recursos que falten.** No todos los tipos de recursos de Azure se pueden exportar a través de Azure Portal, la CLI de Azure o Azure PowerShell. Por ejemplo, las extensiones de máquina virtual como DependencyAgentWindows y MMAExtension (Microsoft Monitoring Agent) son tipos de recursos que no se pueden exportar. Deberá volver a crear los recursos que no se hayan exportado, como las extensiones de máquina virtual, en el nuevo archivo de Bicep. Hay varias herramientas y enfoques que puede usar para volver a crear recursos, como [Azure Resource Explorer](/azure/azure-resource-manager/templates/view-resources?azure-portal=true#use-resource-explorer), la [documentación de referencia de Bicep y las plantillas de ARM](/azure/templates/?azure-portal=true) y el sitio [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates?azure-portal=true).

## <a name="phase-3-refactor"></a>Fase 3: Refactorización

En la fase de _refactorización_ de la migración de los recursos a Bicep, el objetivo es mejorar la calidad del código de Bicep. Estas mejoras pueden incluir cambios, como agregar comentarios de código, que ajusten la plantilla a los estándares de su plantilla.

La fase de refactorización consta de ocho pasos, que se pueden completar en cualquier orden:

1. **Revisión de las versiones de la API de los recursos.** Al exportar recursos de Azure, es posible que la plantilla exportada no tenga la versión más reciente de la API para un tipo de recurso. Si necesita propiedades específicas para futuras implementaciones, actualice la API a la versión adecuada. Se recomienda revisar las versiones de la API de cada recurso exportado.

1. **Revise las sugerencias de linter en el nuevo archivo de Bicep.** Al crear archivos de Bicep mediante la [extensión de Bicep para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep&azure-portal=true), [linter de Bicep](linter.md) se ejecuta automáticamente y resalta sugerencias y errores en el código. Muchos de los errores y las sugerencias incluyen una opción para aplicar una corrección rápida al problema. Revise estas recomendaciones y ajuste su archivo de Bicep.

1. **Revise los parámetros, las variables y los nombres simbólicos.** Es posible que los nombres de los parámetros, las variables y los nombres simbólicos generados por el descompilador no coincidan con su convención de nomenclatura estándar. Revise los nombres generados y realice los ajustes necesarios.

1. **Simplifique expresiones.** Es posible que el proceso de descompilación no siempre aproveche algunas de las características de Bicep. Revise las expresiones generadas en la conversión y simplifíquelas. Por ejemplo, la plantilla descompilada puede incluir una función `concat()` o `format()` que se podría simplificar con la [interpolación de cadenas](bicep-functions-string.md#concat). Revise las sugerencias del linter y realice los ajustes necesarios.

1. **Revise los recursos secundarios y de extensión.** Con Bicep, hay varias maneras de declarar [recursos secundarios](child-resource-name-type.md) y [recursos de extensión](scope-extension-resources.md), incluida la concatenación de los nombres de los recursos, el uso de la palabra clave `parent` y el uso de recursos anidados. Considere la posibilidad de revisar estos recursos después de la descompilación y asegúrese de que la estructura cumple sus estándares. Por ejemplo, asegúrese de que no usa la concatenación de cadenas para crear los nombres de los recursos secundarios; debe usar la propiedad `parent` o un recurso anidado. Del mismo modo, se puede hacer referencia a las subredes como propiedades de una red virtual o como un recurso independiente.

1. **Modularice.** Si va a convertir una plantilla que tiene muchos recursos, considere la posibilidad de dividir los tipos de recursos individuales en [módulos](modules.md) por razones de simplicidad. Los módulos de Bicep ayudan a reducir la complejidad de las implementaciones y a aumentar la capacidad de reutilización del código de Bicep.

    > [!NOTE]
    > Es posible usar las plantillas JSON como módulos en una implementación de Bicep. Bicep tiene la capacidad de reconocer módulos de JSON y hacer referencia a ellos de forma similar a como se usan los módulos de Bicep.

1. **Agregue comentarios y descripciones.** Un código de Bicep en condiciones es _autodocumentado_; Bicep le permite agregar comentarios y atributos `@description()` al código para ayudarle a documentar la infraestructura. Bicep admite los comentarios de una sola línea mediante una secuencia de caracteres `//` y los comentarios de varias líneas que comienzan con `/*` y terminan con `*/`. Puede agregar comentarios a líneas específicas del código y a secciones de código.

1. **Siga los procedimientos recomendados de Bicep.** Asegúrese de que el archivo de Bicep sigue las recomendaciones estándar. Revise el documento de referencia de los [procedimientos recomendados de Bicep](best-practices.md) para comprobar si se ha pasado algo por alto.

## <a name="phase-4-test"></a>Fase 4: Prueba

En la fase de _prueba_ de la migración de sus recursos a Bicep, el objetivo es comprobar la integridad de las plantillas migradas y realizar una implementación de prueba.

La fase de prueba consta de dos pasos que se realizan en secuencia:

1. **Ejecución de la operación what-if de la implementación de la plantilla de ARM.** Para comprobar las plantillas convertidas antes de la implementación, puede usar la [operación what-if de implementación de plantillas de Azure Resource Manager](../templates/deploy-what-if.md). Compara el estado actual de su entorno con el estado deseado que se define en la plantilla. La herramienta genera la lista de cambios que se producirán *sin* aplicar los cambios a su entorno. Puede usar what-if en las implementaciones de modo incremental y completo. Incluso si tiene pensado implementar la plantilla con el modo incremental, es recomendable ejecutar la operación what-if en modo completo.

1. **Realización de una implementación de prueba.** Antes de introducir la plantilla de Bicep convertida en producción, considere la posibilidad de ejecutar varias implementaciones de prueba. Si tiene varios entornos (por ejemplo, desarrollo, pruebas y producción), se recomienda intentar implementar la plantilla primero en uno de los entornos que no son de producción. Después de la implementación, compare los recursos originales con las nuevas implementaciones de recursos para mantener la coherencia.

## <a name="phase-5-deploy"></a>Fase 5: Implementación

En la fase de _implementación_ de la migración de sus recursos a Bicep, el objetivo es implementar el archivo de Bicep final en producción.

La fase de implementación consta de cuatro pasos, que se completan en secuencia:

1. **Preparación de un plan de reversión.** La capacidad de recuperarse de una implementación con errores es fundamental. Desarrolle un plan de reversión por si se incorporan cambios importantes a los entornos. Haga un inventario de los tipos de recursos que se implementan, como máquinas virtuales, aplicaciones web y bases de datos. También se debe tener en cuenta el plano de datos de cada recurso. ¿Existe alguna forma de recuperar una máquina virtual y sus datos? ¿Existe alguna forma de recuperar una base de datos tras su eliminación? Un plan de reversión bien desarrollado ayuda a reducir el tiempo de inactividad al mínimo si surgen problemas derivados de una implementación.

1. **Ejecución de la operación what-if en producción.** Antes de implementar el archivo de Bicep final en producción, ejecute la operación what-if en el entorno de producción, asegúrese de usar los valores de parámetro de producción y considere la posibilidad de documentar los resultados.

1. **Implemente de forma manual.** Si va a usar la plantilla convertida en una canalización, como [Azure DevOps](add-template-to-azure-pipelines.md) o [Acciones de GitHub](deploy-github-actions.md), considere la posibilidad de ejecutar primero la implementación desde el equipo local. Es mejor comprobar la funcionalidad de la plantilla antes de agregarla a la canalización de producción. De este modo, puede responder rápidamente si hay un problema.

1. **Ejecute pruebas de humo.** Una vez completada la implementación, se recomienda ejecutar una serie de *pruebas de humo*: comprobaciones sencillas que validan que su aplicación o carga de trabajo funciona correctamente. Por ejemplo, compruebe si su aplicación web es accesible a través de canales de acceso normal, como la red pública de Internet o a través de una VPN corporativa. En el caso de las bases de datos, intente realizar una conexión de base de datos y ejecutar una serie de consultas. Con las máquinas virtuales, inicie sesión en la máquina virtual y asegúrese de que todos los servicios están en funcionamiento.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el descompilador de Bicep, vea [Descompilación del código JSON de plantilla de ARM en Bicep](decompile.md).
