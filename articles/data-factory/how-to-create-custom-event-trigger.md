---
title: Creación de desencadenadores de eventos personalizados en Azure Data Factory
description: Aprenda a crear un desencadenador en Azure Data Factory que ejecute una canalización en respuesta a un evento personalizado publicado en Event Grid.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: dcad59c6434f6751cba8633868a9dc9969ffc0ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610548"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory"></a>Creación de un desencadenador de eventos personalizado para ejecutar una canalización en Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La arquitectura dirigida por eventos (EDA) es un patrón de integración de datos común que implica la producción, detección, consumo y reacción a los eventos. Los escenarios de integración de datos a menudo requieren que clientes de Azure Data Factory desencadenen canalizaciones cuando suceden ciertos eventos. La integración nativa de Data Factory con [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ahora abarca [temas personalizados](../event-grid/custom-topics.md). Los eventos se envían a un tema de Event Grid. Data Factory se suscribe al tema, escucha y, a continuación, desencadena canalizaciones en consecuencia.

> [!NOTE]
> La integración descrita en este artículo depende de [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Asegúrese de que el proveedor de la suscripción se registra con el proveedor de recursos de Event Grid. Para más información, consulte [Tipos y proveedores de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Debe poder realizar la acción `Microsoft.EventGrid/eventSubscriptions/`. Esta acción forma parte del rol integrado [Colaborador de EventSubscription EventGrid](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor).

Si combina parámetros de canalización y un desencadenador de eventos personalizado, puede analizar y hacer referencia a cargas `data` personalizadas en ejecuciones de canalización. Dado que el campo `data` de una carga de eventos personalizada es una estructura de clave-valor JSON de formato libre, puede controlar las ejecuciones de canalización controladas por eventos.

> [!IMPORTANT]
> Si falta una clave a la que se hace referencia en la parametrización en la carga del evento personalizado, `trigger run` producirá un error. Verá un error que indica que la expresión no se puede evaluar porque la propiedad `keyName` no existe. En este caso, no se desencadenará **ningún** `pipeline run` por el evento.

## <a name="set-up-a-custom-topic-in-event-grid"></a>Configuración de un tema personalizado en Event Grid

Para usar el desencadenador de eventos personalizado en Data Factory, *primero* debe configurar un [tema personalizado en Event Grid](../event-grid/custom-topics.md).

Vaya a Azure Event Grid y cree el tema. Para obtener más información sobre cómo crear el tema personalizado, consulte [Tutoriales del portal](../event-grid/custom-topics.md#azure-portal-tutorials) de Azure Event Grid y [tutoriales de la CLI](../event-grid/custom-topics.md#azure-cli-tutorials).

> [!NOTE]
> El flujo de trabajo es diferente del desencadenador de eventos de almacenamiento. Aquí, Data Factory no configura el tema automáticamente.

Data Factory espera que los eventos sigan el [esquema de eventos de Event Grid](../event-grid/event-schema.md). Asegúrese de que las cargas del evento tienen los siguientes campos:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>Uso de Data Factory para crear un desencadenador de eventos personalizado

1. Vaya a Azure Data Factory e inicie sesión.

1. Cambie a la pestaña **Editar**. Busque el icono de lápiz.

1. Seleccione **Desencadenador** en el menú y, después, seleccione **Nuevo/Editar**.

1. En la página para **agregar desencadenadores**, seleccione **Choose trigger** (Elegir desencadenador) y, después, seleccione **Nuevo**.

1. Seleccione **Eventos personalizados** para **Tipo**.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Captura de pantalla de la página de autor para crear un nuevo desencadenador de eventos personalizado en la interfaz de usuario de Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Seleccione el tema personalizado en la lista desplegable suscripción de Azure o introduzca manualmente el ámbito del tema del evento.

   > [!NOTE]
   > Para crear o modificar un desencadenador de eventos personalizado en Data Factory, debe usar una cuenta de Azure con el control de acceso basado en rol (RBAC de Azure) adecuado. No se requiere ningún permiso adicional. La entidad de servicio de Data Factory *no* requiere un permiso especial para su instancia de Event Grid. Para obtener más información sobre el control de acceso, consulte la sección [Control de acceso basado en roles](#role-based-access-control).

1. Las propiedades **El elemento "Asunto" empieza por** y **El elemento "Asunto" termina en** le permiten filtrar eventos del desencadenador. Todas las propiedades son opcionales.

1. Use **+ Nuevo** para agregar los **tipos de evento** por los que desea filtrar. La lista de desencadenadores de eventos personalizados usa una relación OR. Cuando se produce un evento personalizado con una propiedad `eventType` que coincide con otra de la lista, se desencadena una ejecución de canalización. El tipo de evento distingue mayúsculas de minúsculas. Por ejemplo, en la captura de pantalla siguiente, el desencadenador coincide con todos los eventos `copycompleted` o `copysucceeded` que tienen un asunto que comienza por *factories*.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Captura de pantalla de la página Editar desencadenador para explicar los tipos de eventos y el filtrado de asunto en la interfaz de usuario de Data Factory.":::

1. Un desencadenador de eventos personalizado puede analizar y enviar la carga de `data` personalizada a la canalización. Cree los parámetros de canalización y luego rellene los valores en la página **Parámetros**. Use el formato `@triggerBody().event.data._keyName_` para analizar la carga de datos y pasar los valores a los parámetros de canalización.
 
   Para ver una explicación detallada, consulte los artículos siguientes:
   - [Referencia de metadatos de desencadenador en canalizaciones](how-to-use-trigger-parameterization.md)
   - [Variables del sistema en el desencadenador de eventos personalizado](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Captura de pantalla de la configuración de parámetros de canalización.":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Captura de pantalla de la página de parámetros para hacer referencia a la carga de datos en el evento personalizado.":::

1. Después de que haya escrito los parámetros, seleccione **Aceptar**.

## <a name="advanced-filtering"></a>Filtrado avanzado

El desencadenador de eventos personalizado admite funcionalidades de filtrado avanzadas, de forma similar al [Filtrado avanzado de Event Grid](../event-grid/event-filtering.md#advanced-filtering). Estos filtros condicionales permiten que las canalizaciones se desencadenen en función de los _valores_ de la carga del evento. Por ejemplo, puede tener un campo en la carga del evento, denominado _Departamento_, y la canalización solo debe desencadenarse si el valor de _Departamento_ es igual al de _Finanzas_. También puede especificar una lógica compleja, como el campo _date_ en la lista [1, 2, 3, 4, 5], el campo _month_ que __no__ está en la lista [11, 12], el campo _tag_ contiene cualquiera de los valores ["Fiscal Year 2021", "FiscalYear2021", "FY2021"].

 :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-5-advanced-filters.png" alt-text="Captura de pantalla de la configuración de filtros avanzados para el desencadenador de eventos del cliente":::

A partir de hoy, el desencadenador de eventos personalizados admite un __subconjunto__ de [operadores de filtrado avanzado](../event-grid/event-filtering.md#advanced-filtering) en Event Grid. Se admiten las siguientes condiciones de filtro:

* NumberIn
* NumberNotIn
* NumberLessThan
* NumberGreaterThan
* NumberLessThanOrEquals
* NumberGreaterThanOrEquals
* BoolEquals
* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Haga clic en  **+Nuevo** para agregar nuevas condiciones de filtro. 

Además, los desencadenadores de eventos personalizados cumplen [las mismas limitaciones que Event Grid](../event-grid/event-filtering.md#limitations), entre las que se incluyen:

* 5 filtros avanzados y 25 valores de filtro en todos los filtros por desencadenador de evento personalizado
* 512 caracteres por valor de cadena
* 5 valores para los operadores in y not in
* Las claves no pueden tener un carácter `.` (punto) en ellas, por ejemplo, `john.doe@contoso.com`. Actualmente, las claves no admiten caracteres de escape.
* La misma clave se puede usar en más de un filtro.

Data Factory se basa en la versión más reciente de _disponibilidad general_ de la [API de Event Grid](../event-grid/whats-new.md). A medida que las nuevas versiones de API lleguen a la fase de disponibilidad general, Data Factory ampliará su compatibilidad con operadores de filtrado más avanzados.

## <a name="json-schema"></a>Esquema JSON

En la tabla siguiente se proporciona información general acerca de los elementos de esquema que están relacionados con los desencadenadores de eventos personalizados:

| Elemento JSON | Descripción | Tipo | Valores permitidos | Obligatorio |
|---|----------------------------|---|---|---|
| `scope` | El identificador de recursos de Azure Resource Manager del tema de cuadrícula del evento. | String | Identificador de Azure Resource Manager | Sí |
| `events` | El tipo de eventos que provocan la activación de este desencadenador. | Matriz de cadenas    |  | Sí, se espera al menos un valor. |
| `subjectBeginsWith` | El campo `subject` debe comenzar con el patrón proporcionado para que se active el desencadenador. Por ejemplo, _factories_ solo activa el desencadenador para los asuntos de eventos que comienzan por *factories*. | String   | | No |
| `subjectEndsWith` | El campo `subject` debe terminar con el patrón proporcionado para que se active el desencadenador. | String   | | No |
| `advancedFilters` | Lista de blobs de JSON, donde cada uno especifica una condición de filtro. Cada blob especifica `key`, `operatorType` y `values`. | Lista de blobs de JSON | | No |

## <a name="role-based-access-control"></a>Control de acceso basado en rol

Azure Data Factory utiliza el control de acceso basado en roles de Azure (RBAC) para prohibir el acceso no autorizado. Para funcionar correctamente, Data Factory requiere acceso para:
- Escuchar eventos.
- Suscribirse a las actualizaciones de eventos.
- Desencadenar canalizaciones vinculadas a eventos personalizados.

Para crear o actualizar correctamente un desencadenador de eventos personalizado, debe iniciar sesión en Data Factory con una cuenta de Azure que tenga el acceso adecuado. De lo contrario, se producirá un error en la operación con un error de _acceso denegado_.

Data Factory no requiere un permiso especial para su instancia de Event Grid. Igualmente, *no* es necesario asignar un permiso de RBAC de Azure especial a la entidad de servicio de Data Factory para la operación.

En concreto, necesita el permiso `Microsoft.EventGrid/EventSubscriptions/Write` para `/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics`.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información detallada sobre la [ejecución del desencadenador](concepts-pipeline-execution-triggers.md#trigger-execution).
* Aprenda a [hacer referencia a los metadatos de desencadenador en ejecuciones de canalización](how-to-use-trigger-parameterization.md).
