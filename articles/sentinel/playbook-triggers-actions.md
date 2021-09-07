---
title: Uso de desencadenadores y acciones en cuadernos de estrategias de Azure Sentinel | Microsoft Docs
description: Obtenga información más detallada sobre cómo proporcionar a los cuadernos de estrategias el acceso a la información de las alertas e incidentes de Azure Sentinel, y use esa información para realizar acciones correctivas.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 045178a30088ccfd8b76d70d210e29c5a9253ac1
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294378"
---
# <a name="use-triggers-and-actions-in-azure-sentinel-playbooks"></a>Uso de desencadenadores y acciones en cuadernos de estrategias de Azure Sentinel

En este documento se explican los tipos de desencadenadores y acciones del [conector de Azure Sentinel de Logic Apps](/connectors/azuresentinel/), que los cuadernos de estrategias pueden usar para interactuar con Azure Sentinel y la información en las tablas del área de trabajo. Además, se muestra cómo obtener tipos específicos de información de Azure Sentinel que es probable que necesite.

Este documento, junto con nuestra guía para la [Autenticación de cuadernos de estrategias en Azure Sentinel](authenticate-playbooks-to-sentinel.md), es un complemento de nuestra otra documentación del cuaderno de estrategias: [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Azure Sentinel](tutorial-respond-threats-playbook.md). Estos tres documentos se referirán entre sí de un lado a otro.

Para ver una presentación de los cuadernos de estrategias, consulte [Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md).

Para la especificación completa del conector de Azure Sentinel, consulte la [documentación del conector de Logic Apps](/connectors/azuresentinel/).

## <a name="permissions-required"></a>Permisos necesarios

| Componentes del conector o roles | Desencadenadores | Acciones "Get" | Actualizar incidente,<br>agregar un comentario |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Lector de Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Azure Sentinel [Respondedor](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[Colaborador](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Más información sobre permisos en Azure Sentinel](./roles.md).

## <a name="azure-sentinel-triggers-summary"></a>Resumen de desencadenadores de Azure Sentinel

Aunque el conector de Azure Sentinel se puede usar de varias maneras, los componentes del conector se pueden dividir en dos flujos, cada uno desencadenado por una aparición diferente de Azure Sentinel:

| Desencadenador | Nombre completo del desencadenador en<br>Diseñador de Logic Apps | Cuándo se debe usar | Limitaciones conocidas 
| --------- | ------------ | -------------- | -------------- | 
| **Desencadenador de incidentes** | "When Azure Sentinel incident creation rule was triggered (Preview)" (Cuando se desencadenó la regla de creación de incidentes de Azure Sentinel [versión preliminar]) | Se recomienda para la mayoría de los escenarios de automatización de incidentes.<br><br>El cuaderno de estrategias recibe objetos de incidentes, incluidas las entidades y las alertas. El uso de este desencadenador permite adjuntar el cuaderno de estrategias a una **regla de automatización**, por lo que se puede desencadenar al crear un incidente en Azure Sentinel y se pueden aplicar todas las [ventajas de las reglas de automatización](./automate-incident-handling-with-automation-rules.md) al incidente. | Los cuadernos de estrategias con este desencadenador no se pueden ejecutar manualmente desde Azure Sentinel.<br><br>Los cuadernos de estrategias con este desencadenador no admiten la agrupación de alertas, lo que significa que solo recibirán la primera alerta enviada con cada incidente.
| **Desencadenador de alertas** | When a response to an Azure Sentinel alert is triggered (Cuando se desencadena una respuesta a una alerta de Azure Sentinel) | Aconsejable para los cuadernos de estrategias que deben ejecutarse en alertas manualmente desde el portal de Azure Sentinel o para las reglas de análisis **programadas** que no generan incidentes para sus alertas. | Este desencadenador no se puede usar para automatizar las respuestas de las alertas generadas por las reglas de análisis de **seguridad de Microsoft**.<br><br>Las **reglas de automatización** no pueden llamar a los cuadernos de estrategias que usan este desencadenador. |
|

Los esquemas usados por estos dos flujos no son idénticos.
El procedimiento recomendado es usar el flujo del **desencadenador de incidentes de Azure Sentinel**, que es aplicable a la mayoría de los escenarios.

### <a name="incident-dynamic-fields"></a>Campos dinámicos de incidentes

El objeto **Incidente** recibido de **When Azure Sentinel incident creation rule was triggered** (Cuando se desencadenó la regla de creación de incidentes de Azure Sentinel) incluye los siguientes campos dinámicos:

- Propiedades del incidente (se muestra como "Incidente: nombre de campo")

- Alertas (matriz)

  - Propiedades de alerta (se muestra como "Alerta: nombre de campo")

    Al seleccionar una propiedad de alerta como **Alerta: \<property name>** , se genera automáticamente un bucle *Para cada uno*, ya que un incidente puede incluir varias alertas.

- Entidades (matriz de todas las entidades de una alerta)

- Campos de información del área de trabajo (se aplica al área de trabajo de Sentinel donde se creó el incidente)
  - Id. de suscripción
  - Nombre del área de trabajo
  - Id. del área de trabajo
  - Definición de un nombre de grupo de recursos

## <a name="azure-sentinel-actions-summary"></a>Resumen de acciones de Azure Sentinel

| Componente | Cuándo se debe usar |
| --------- | -------------- |
| **Alert - Get Incident** (Alerta: obtener incidente) | En cuadernos de estrategias que comienzan con el desencadenador de alertas. Resulta útil para obtener las propiedades del incidente o recuperar el valor de **Incident ARM ID** (Id. de ARM del incidente) que se usará con las acciones **Actualizar incidente** o **Add comment to incident** (Agregar comentario a incidente). |
| **Obtener incidente** | Al desencadenar un cuaderno de estrategias desde un origen externo o con un desencadenador que no es de Sentinel. Identifique con un **Incident ARM ID** (Id. de ARM del incidente). Recupera las propiedades y los comentarios del incidente. |
| **Actualizar incidente** | Para cambiar el **Estado** de un incidente (por ejemplo, al cerrar el incidente), asigne un **Propietario**, agregue o quite una etiqueta o cambie su **Gravedad**, **Título** o **Descripción**.
| **Add comment to incident** (Agregar comentario a incidente) | Para enriquecer el incidente con información recopilada de orígenes externos; para auditar las acciones realizadas por el cuaderno de estrategias en las entidades; para proporcionar información adicional valiosa para la investigación de incidentes. |
| **Entities - Get \<*entity type*\> (Entidades: obtener)** | En los cuadernos de estrategias que funcionan en un tipo de entidad específico (**IP**, **Cuenta**, **Host**, **Dirección URL** o **FileHash**) que se conoce en el momento de la creación del cuaderno de estrategias, debe poder analizarlo y trabajar en sus campos únicos. |
|

## <a name="work-with-incidents---usage-examples"></a>Utilización de incidentes: ejemplos de uso

> [!TIP] 
> Las acciones **Actualizar incidente** y **Add comment to incident** (Agregar comentario a incidente) requieren el valor de **Incident ARM ID** (Id. de ARM del incidente). <br>
Use la acción **Alert - Get Incident** (Alerta: obtener incidente) de antemano para obtener el valor de **Incident ARM ID** (Id. de ARM del incidente).

### <a name="update-an-incident"></a>Actualización de un incidente
-  El cuaderno de estrategias se desencadena **cuando se crea un incidente**.

    ![Ejemplo sencillo de flujo de actualización de desencadenador de incidentes](media/playbook-triggers-actions/incident-simple-flow.png)

-  El cuaderno de estrategias se desencadena **cuando se genera una alerta**.

    ![Ejemplo sencillo de flujo de actualización de incidente de desencadenador de alertas](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>Uso de la información de incidente.

Cuaderno de estrategias básico para enviar detalles de incidentes por correo:
-  El cuaderno de estrategias se desencadena **cuando se crea un incidente**.

    ![Ejemplo sencillo de flujo de obtención de desencadenador de incidentes](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  El cuaderno de estrategias se desencadena **cuando se genera una alerta**.

    ![Ejemplo sencillo de flujo de obtención de incidente de desencadenador de alertas](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>Adición de un comentario al incidente

-  El cuaderno de estrategias se desencadena **cuando se crea un incidente**.

    ![Ejemplo sencillo de adición de comentario del desencadenador de incidentes](media/playbook-triggers-actions/incident-comment.png)

-  El cuaderno de estrategias se desencadena **cuando se genera una alerta**.

    !["Ejemplo sencillo de adición de comentario del desencadenador de alertas"](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>Utilización de tipos de entidad específicos

El campo dinámico **Entidades** es una matriz de objetos JSON, cada uno de los cuales representa una entidad. Cada tipo de entidad tiene su propio esquema, en función de sus propiedades únicas.

La acción **"Entities - Get \<entity name>" (Entidades: obtener)**  permite hacer lo siguiente:

- Filtrar la matriz de entidades por el tipo solicitado.
- Analice los campos específicos de este tipo para que se puedan usar como campos dinámicos en otras acciones.

La entrada es el campo dinámico **Entidades**.

La respuesta es una matriz de entidades, donde se analizan las propiedades especiales y se pueden usar directamente en un bucle *Para cada uno*.

Los tipos de entidad admitidos actualmente son:

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [Host](/connectors/azuresentinel/#entities---get-hosts)
- [Cuenta](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [FileHash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="Lista de acciones de entidades":::

Para otros tipos de entidad, se puede lograr una funcionalidad similar mediante las acciones integradas de Logic Apps:

- Filtrar la matriz de entidades por el tipo solicitado mediante [**Filtrar matriz**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action).

- Analizar los campos específicos de este tipo, para que se puedan usar como campos dinámicos en otras acciones mediante [**Análisis del archivo JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="work-with-custom-details"></a>Utilización de detalles personalizados

El campo dinámico **Alert custom details** (Detalles personalizados de alerta), disponible en el **desencadenador de incidentes**, es una matriz de objetos JSON, cada uno de los cuales representa un detalle personalizado de una alerta. Los [detalles personalizados](surface-custom-details-in-alerts.md), que recuperará, son pares de clave-valor que le permiten obtener información de los eventos de la alerta para que se puedan representar, realizar un seguimiento y analizar como parte del incidente.

Puesto que este campo de la alerta es personalizable, su esquema depende del tipo de evento que se va a exponer. Tendrá que proporcionar datos de una instancia de este evento para generar el esquema que determinará cómo se analizará el campo de detalles personalizados.

Observe el ejemplo siguiente:

![Detalles personalizados definidos en la regla de análisis.](./media/playbook-triggers-actions/custom-details-values.png)

En estos pares de clave-valor, la clave (la columna de la izquierda) representa los campos personalizados que se crean y el valor (la columna de la derecha) representa los campos de los datos de evento que rellenan los campos personalizados.

Puede proporcionar el siguiente código JSON para generar el esquema. En el código se muestran los nombres de clave como matrices y los valores (que se muestran como valores reales, no como la columna que contiene los valores) como elementos de las matrices.

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. Agregue un nuevo paso mediante la acción integrada **Analizar JSON**. Puede escribir "analizar json" en el campo Buscar para encontrarlo.

1. Busque y seleccione **Alert custom details** (Detalles personalizados de alerta) en la lista **Contenido dinámico**, en el desencadenador de incidentes.

    ![Seleccione "Alert custom details" (Detalles personalizados de alerta) en "Contenido dinámico".](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    Esto creará un bucle **Para cada uno**, ya que un incidente contiene una matriz de alertas.

1. Haga clic en el vínculo **Usar una carga de ejemplo para generar el esquema**.

    ![Seleccione el vínculo "Usar una carga de ejemplo para generar el esquema"](./media/playbook-triggers-actions/generate-schema-link.png)

1. Proporcione una carga de ejemplo. Para encontrar una carga de ejemplo, busque en Log Analytics (hoja **Registros**) otra instancia de esta alerta y copie el objeto de detalles personalizado (en **Propiedades extendidas**). En la captura de pantalla siguiente, se usa el código JSON mostrado anteriormente.

    ![Escriba una carga JSON de ejemplo.](./media/playbook-triggers-actions/sample-payload.png)

1. Los campos personalizados están listos para usarse en campos dinámicos de tipo **Matriz**. Aquí puede ver la matriz y sus elementos, tanto en el esquema como en la lista que aparece en **Contenido dinámico**, que hemos descrito anteriormente.

    ![Campos del esquema listos para usarse.](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido más sobre el uso de los desencadenadores y las acciones de los cuadernos de estrategias de Azure Sentinel para responder a las amenazas. 
- Más información en [Búsqueda de amenazas con Azure Sentinel](hunting.md).