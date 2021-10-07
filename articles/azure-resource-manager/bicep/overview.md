---
title: Lenguaje Bicep para implementar recursos de Azure
description: Describe el lenguaje Bicep para implementar la infraestructura en Azure. Ofrece una experiencia de creación mejorada sobre el uso de JSON para desarrollar plantillas.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ace417bb8a71a277bbcb4ef178c6aaf24b97533b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831944"
---
# <a name="what-is-bicep"></a>¿Qué es Bicep?

Bicep es un lenguaje específico del dominio (DSL) que usa sintaxis declarativa para implementar recursos de Azure. Brinda sintaxis concisa, seguridad de tipos confiable y compatibilidad con la reutilización de código. Creemos que Bicep ofrece la mejor experiencia de creación para sus soluciones de infraestructura como código en Azure.

Puede usar Bicep en lugar de JSON para desarrollar las plantillas de Azure Resource Manager (plantillas de ARM). La sintaxis JSON para crear una plantilla de ARM puede ser detallada y exigir expresiones complicadas. La sintaxis de Bicep reduce esa complejidad y mejora la experiencia de desarrollo. Bicep es una abstracción transparente del código JSON de plantillas de ARM, y no pierde ninguna de las funcionalidades de las plantillas JSON. Durante la implementación, la CLI de Bicep convierte un archivo de Bicep en un archivo JSON de plantilla de ARM.

Bicep no está pensado como lenguaje general de programación para escribir aplicaciones. Un archivo de Bicep declara recursos de Azure y propiedades de estos recursos, sin escribir una secuencia de comandos de programación para crear recursos.

Los tipos de recursos, las versiones de API y las propiedades que son válidos en una plantilla de ARM son válidos en un archivo de Bicep.

Para realizar el seguimiento del estado del trabajo en Bicep, consulte el [repositorio de proyectos de Bicep](https://github.com/Azure/bicep).

Para obtener información sobre Bicep, consulte el vídeo siguiente.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Introducción

Para empezar a trabajar con Bicep:

1. **Instale las herramientas**. Consulte [Configuración de entornos de desarrollo e implementación de Bicep](./install.md). O bien, puede usar el [repositorio VS Code Devcontainer/Codespaces](https://github.com/Azure/vscode-remote-try-bicep) para obtener un entorno de creación preconfigurado.
2. **Complete el artículo de [inicio rápido](./quickstart-create-bicep-use-visual-studio-code.md) y los [módulos de Bicep en Microsoft Learn](./learn-bicep.md)** .

Para descompilar en Bicep una plantilla de ARM existente, consulte [Descompilación de plantillas de ARM en Bicep](./decompile.md). Para ver el archivo de Bicep y el de JSON equivalente en paralelo, consulte [Bicep Playground](https://bicepdemo.z22.web.core.windows.net/).

Para obtener información sobre los recursos que están disponibles en el archivo de Bicep, vea [Referencia de recursos de Bicep](/azure/templates/)

Puede encontrar ejemplos de Bicep en el [repositorio de Bicep en GitHub](https://github.com/Azure/bicep/tree/main/docs/examples).

## <a name="benefits-of-bicep-versus-other-tools"></a>Ventajas de Bicep frente a otras herramientas

Bicep proporciona las siguientes ventajas con respecto a otras opciones:

- **Compatibilidad con todos los tipos de recursos y versiones de API:** Bicep admite inmediatamente todas las versiones preliminares y de GA para los servicios de Azure. En cuanto un proveedor de recursos introduce nuevos tipos de recursos y versiones de API, puede usarlos en el archivo de Bicep. No tiene que esperar a que se actualicen las herramientas antes de usar los nuevos servicios.
- **Sintaxis simple**: En comparación con la plantilla JSON equivalente, los archivos de Bicep son más concisos y fáciles de leer. Bicep no requiere ningún conocimiento previo de los lenguajes de programación. La sintaxis de Bicep es declarativa y especifica qué recursos y propiedades de recursos quiere implementar.
- **Experiencia de creación:** al usar VS Code para crear los archivos de Bicep, se obtiene una experiencia de creación de primera clase. El editor proporciona una completa seguridad de tipos, IntelliSense y validación de sintaxis.
- **Modularidad:** puede dividir el código de Bicep en elementos administrables mediante los [módulos](./modules.md). El módulo implementa un conjunto de recursos relacionados. Los módulos permiten reutilizar el código y simplificar el desarrollo. Agregue el módulo a un archivo de Bicep cada vez que necesite implementar esos recursos.
- **Integración con los servicios de Azure:** Bicep se integra con los servicios de Azure como Azure Policy, las especificaciones de plantilla y los planos técnicos.
- **No hay archivos de estado ni estado que administrar**: todo el estado se almacena en Azure. Los usuarios pueden colaborar y tener la confianza de que sus actualizaciones se controlan según lo previsto. Use la [operación what-if](./deploy-what-if.md) para obtener una versión preliminar de los cambios antes de implementar la plantilla.
- **Sin costo ni código abierto**: Bicep es completamente gratuito. No tiene que pagar por las funcionalidades prémium. Asimismo, también es compatible con el Soporte técnico de Microsoft.

## <a name="bicep-improvements"></a>Mejoras de Bicep

Bicep ofrece una sintaxis más sencilla y concisa en comparación con el JSON equivalente. No se usan expresiones entre `[...]` corchetes. En su lugar, se llama directamente a las funciones y se obtienen los valores de los parámetros y las variables. Se asigna un nombre simbólico a cada recurso implementado, lo que facilita la referencia a ese recurso en la plantilla.

Por ejemplo, el siguiente JSON devuelve un valor de salida de una propiedad de recurso.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

La expresión de salida equivalente en Bicep es más fácil de escribir. En el ejemplo siguiente se devuelve la misma propiedad con el nombre simbólico **publicIP** para un recurso que está definido en la plantilla:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Para ver una comparación completa de la sintaxis, consulte [Comparación de JSON y Bicep para plantillas](compare-template-syntax.md).

Bicep administra automáticamente las dependencias entre los recursos. Puede evitar configurar `dependsOn` cuando el nombre simbólico de un recurso se use en otra declaración de recursos.

La estructura del archivo de Bicep es más flexible que la plantilla JSON. Puede declarar parámetros, variables y salidas en cualquier parte del archivo. En JSON, tiene que declarar todos los parámetros, variables y salidas dentro de las secciones correspondientes de la plantilla.

## <a name="faq"></a>Preguntas más frecuentes

**¿Por qué crear un nuevo lenguaje en lugar de usar uno existente?**

Puede considerar Bicep como una revisión del lenguaje de plantillas de ARM existente en lugar de un nuevo lenguaje. La sintaxis ha cambiado, pero la funcionalidad básica y el entorno en tiempo de ejecución siguen siendo los mismos.

Antes de desarrollar Bicep, se consideró el uso de un lenguaje de programación existente. Se decidió que el público de destino encontraría más fácil aprender Bicep en lugar de empezar a trabajar con otro lenguaje.

**¿Por qué no centrar la energía en Terraform u otras ofertas de infraestructura como código de terceros?**

Los distintos usuarios prefieren distintos lenguajes y herramientas de configuración. Queremos asegurarnos de que todas estas herramientas ofrezcan una gran experiencia en Azure. Bicep es parte de ese esfuerzo.

Si está satisfecho con Terraform, no hay ninguna razón para cambiar. Microsoft se compromete a asegurarse de que Terraform en Azure sea lo mejor posible.

En el caso de los clientes que han seleccionado plantillas de ARM, creemos que Bicep mejora la experiencia de creación. Bicep también ayuda a realizar la transición de los clientes que no han adoptado una infraestructura como código.

**¿Está listo para su uso en producción?**

Sí. A partir de la versión 0.3, Bicep es compatible con los planes de soporte técnico de Microsoft. Bicep tiene paridad con lo que se puede lograr con las plantillas de ARM. Actualmente no se están planeando cambios importantes, pero es posible que sea necesario crearlos en el futuro.

**¿Está Bicep dirigido solo a Azure?**

Actualmente, no existen planes de ampliar Bicep más allá de Azure. Queremos admitir completamente Azure y optimizar la experiencia de implementación.

Para satisfacer ese objetivo, es necesario trabajar con algunas API que están fuera de Azure. Esperamos proporcionar puntos de extensibilidad para esos escenarios.

**¿Qué ocurre con mis plantillas de ARM existentes?**

Siguen funcionando exactamente como siempre lo han hecho. No es necesario hacer ningún cambio. Seguiremos admitiendo el lenguaje JSON subyacente para las plantillas de ARM. Los archivos de Bicep se compilan en JSON, y ese JSON se envía a Azure para su implementación.

Cuando esté listo, puede [descompilar los archivos JSON en Bicep](./decompile.md).

## <a name="known-limitations"></a>Limitaciones conocidas

- Bicep diferencia las nuevas líneas. Por ejemplo:

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
      ...
    }
    ```

    No se puede escribir como:

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
        if (newOrExisting == 'new') {
          ...
        }
    ```

- No se admite el concepto de apiProfile, que se usa para asignar un único apiProfile a un valor de apiVersion establecido para cada tipo de recurso.
- No se admiten las funciones definidas por el usuario.

## <a name="next-steps"></a>Pasos siguientes

Comience con el [inicio rápido](./quickstart-create-bicep-use-visual-studio-code.md).
