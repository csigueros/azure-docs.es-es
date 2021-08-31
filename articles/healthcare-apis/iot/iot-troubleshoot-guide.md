---
title: 'Conector de Azure IoT para FHIR (versión preliminar): Guía de solución de problemas y procedimientos'
description: Este artículo ayuda a los usuarios a solucionar problemas de mensajes comunes de error, incidencias y cómo copiar archivos de asignación.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 07/20/2021
ms.author: jasteppe
ms.openlocfilehash: 0edc58bd10cfdab0b7b6cff29c3513ab3c73025b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781100"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guía de solución de problemas del conector de Azure IoT para FHIR (versión preliminar)

En este artículo se proporcionan los pasos para solucionar condiciones y mensajes de error relacionados con el conector de IoT de Azure para Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;). También aprenderá a crear copias de las plantillas JSON de asignación de conversión del conector de IoT de Azure para FHIR (versión preliminar), como dispositivo y FHIR. Puede usar las copias del archivo JSON de asignaciones de conversión para editar y archivar fuera de Azure Portal.  

> [!NOTE]
> En el resto de este artículo, el conector de IoT de Azure para FHIR (versión preliminar) se denominará Conector de IoT.

> [!TIP]
> Si debe a abrir una incidencia de [soporte técnico de Azure](https://azure.microsoft.com/support/create-ticket/) para el conector de IoT, asegúrese de incluir copias de su archivo JSON de asignación de conversión para ayudar con el proceso de solución de problemas.

## <a name="device-and-fhir-conversion-mapping-json-template-validations"></a>Validaciones de plantillas JSON de asignación de conversión de dispositivo y FHIR

En esta sección se describe el proceso de validación que realiza el conector de IoT. El proceso de validación valida las plantillas JSON de asignación de conversión de dispositivo y FHIR antes de permitir que se guarden para su uso. Estos elementos son necesarios en el archivo JSON de asignación de conversión de dispositivo y FHIR.

**Asignación de dispositivos**

|Elemento|Obligatorio|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> Los elementos `Values[].ValueName and Values[].ValueExpression` solo son necesarios si tiene una entrada de valor en la matriz. Es válido no tener ningún valor asignado. Se usa cuando la telemetría que se envía es un evento. 
>
>Por ejemplo:
> 
>Cuando se coloca o se quita un dispositivo IoMT ponible, los elementos no tienen ningún valor, excepto un nombre con el que el conector de IoT coincide y emite. En la conversión FHIR, el conector de IoT lo asigna a un concepto codificable basado en el tipo semántico. Esto significa que no se rellena ningún valor real.

**Asignación de FHIR**

|Elemento|Obligatorio|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> Este es el único elemento de asignación FHIR necesario validado en este momento.

## <a name="error-messages-and-fixes"></a>Correcciones y mensajes de error

### <a name="iot-connector-resource"></a>Recurso del conector de IoT

|Message|Visualización|Condición|Fix| 
|-------|---------|---------|---|
|Se ha alcanzado el número máximo de tipos `iotconnectors` de recursos.|API y Azure Portal|Se ha alcanzado la cuota de la suscripción del conector de IoT de Azure y el valor predeterminado es 25 por suscripción.|Elimine una de las instancias existentes del conector de IoT. Use otra suscripción que no haya alcanzado la cuota de suscripción. Solicite un aumento de la cuota de suscripción.
|Asignación `deviceMapping` no válida. Errores de validación: {Lista de errores}|API y Azure Portal|El `properties.deviceMapping` proporcionado en la solicitud del conector de IoT de aprovisionamiento de recursos no es válido.|Corrija los errores del JSON de asignación proporcionado en la propiedad `properties.deviceMapping`.
|`fullyQualifiedEventHubNamespace` es null, está vacío o tiene un formato incorrecto.|API y Azure Portal|La solicitud de aprovisionamiento del conector de IoT `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` no es válida.|Actualice el `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` del conector de IoT con el formato correcto. Debe ser `{YOUR_NAMESPACE}.servicebus.windows.net`.
|Los recursos antecesores deben aprovisionarse por completo antes de que se pueda aprovisionar un recurso secundario.|API|El área de trabajo principal sigue aprovisionando.|Espere hasta que se haya completado el aprovisionamiento del área de trabajo principal y envíe de nuevo la solicitud de aprovisionamiento.
|La propiedad `Location` de los recursos secundarios debe coincidir con la propiedad `Location` de los recursos principales.|API|La propiedad `location` de la solicitud de aprovisionamiento del conector de IoT es diferente de la propiedad `location` del área de trabajo principal.|Establezca la propiedad `location` del conector de IoT en la solicitud de aprovisionamiento con el mismo valor que la propiedad `location` del área de trabajo principal.

### <a name="destination-resource"></a>Recurso de destino

|Message|Visualización|Condición|Fix| 
|-------|---------|---------|---|
|Se ha alcanzado el número máximo de tipos `iotconnectors/destinations` de recursos.|API y Azure Portal|Se ha alcanzado la cuota de recursos de destino del conector de IoT de y el valor predeterminado es 1 por conector de IoT.|Elimine la instancia existente del destino del conector de IoT. Solo se permite un recurso de destino por conector de IoT.
|El valor `fhirServiceResourceId` proporcionado no es válido.|API y Azure Portal|El `properties.fhirServiceResourceId` proporcionado en la solicitud de aprovisionamiento de recursos de destino no es un id. de recurso válido para una instancia de Azure API for FHIR.|Asegúrese de que el id. de recurso tenga el formato correcto y asegúrese de que el id. de recurso sea para una instancia de Azure API for FHIR. El formato debe ser `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`.
|Los recursos antecesores deben aprovisionarse por completo antes de que se pueda aprovisionar un recurso secundario.|API|El área de trabajo principal o el conector de IoT principal todavía se están aprovisionando.|Espere hasta que se complete el aprovisionamiento del área de trabajo o del conector de IoT principal y, después, envíe de nuevo la solicitud de aprovisionamiento.
|La propiedad `Location` de los recursos secundarios debe coincidir con la propiedad `Location` de los recursos principales.|API|La propiedad `location` de la solicitud de aprovisionamiento de destino es diferente de la propiedad `location` del conector de IoT principal.|Establezca la propiedad `location` del destino en la solicitud de aprovisionamiento con el mismo valor que la propiedad `location` del conector de IoT principal.

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-fhir-service"></a>¿Por qué los datos del conector de IoT de Azure para FHIR (versión preliminar) no aparecen en Azure API for FHIR?

|Posibles problemas|Correcciones|
|----------------|-----|
|Todavía se están procesando los datos.|Los datos salen hacia el servicio FHIR en lotes (cada 15 minutos aproximadamente).  Es posible que los datos se sigan procesando y que se necesite un tiempo adicional para que los datos se conserven en el servicio FHIR.|
|No se ha configurado el archivo JSON de asignación de conversión del dispositivo.|Configure y guarde el archivo JSON conforme de asignación de conversión de dispositivo.|
|No se ha configurado el archivo JSON de asignación de conversión de FHIR.|Configure y guarde el archivo JSON conforme de asignación de conversión de FHIR.|
|El mensaje del dispositivo no contiene una expresión esperada definida en la asignación del dispositivo.|Compruebe que las expresiones `JsonPath`definidas en la asignación del dispositivo coincidan con los tokens definidos en el mensaje del dispositivo.|
|No se ha creado un recurso de dispositivo en el servicio FHIR (tipo de resolución: solo Lookup)*.|Cree un recurso de dispositivo válido en el servicio FHIR. Asegúrese de que el recurso de dispositivo contenga un id. que coincida con el id. de dispositivo proporcionado en el mensaje entrante.|
|No se ha creado un recurso Patient en el servicio FHIR (tipo de resolución: solo Lookup)*.|Cree un recurso Patient válido en el servicio FHIR.|
|La referencia `Device.patient` no está establecida o la referencia no es válida (tipo de resolución: solo Lookup)*.|Asegúrese de que el recurso de dispositivo contenga una [referencia](https://www.hl7.org/fhir/device-definitions.html#Device.patient) válida a un recurso de paciente.| 

\* Consulte [Inicio rápido: Implementación del conector de IoT (versión preliminar) con Azure Portal](deploy-iot-connector-in-azure.md) para conocer una descripción funcional de los tipos de resolución del conector de Azure IoT para FHIR (por ejemplo: Lookup o Create).

### <a name="the-operation-performed-by-the-iot-connector"></a>La operación realizada por el conector de IoT

Esta propiedad representa la operación que estaba realizando el conector de IoT cuando se generó el error. Por lo general, una operación representa la fase del flujo de datos durante el procesamiento de un mensaje del dispositivo. A continuación se muestra una lista de valores posibles para esta propiedad.

> [!NOTE]
> Para obtener información sobre las distintas fases del flujo de datos en el conector de IoT, consulte [Conector de IoT para FHIR: flujo de datos](iot-data-flow.md).

|Fase del flujo de datos|Descripción|
|---------------|-----------|
|Configuración|La fase de flujo de datos de configuración es la operación específica para configurar la instancia del conector de IoT.|
|Normalización|La normalización es la fase de flujo de datos donde se normalizan los datos del dispositivo.|
|Agrupación|Fase de flujo de datos de agrupación donde se agrupan los datos normalizados.|
|FHIRConversion|Fase del flujo de datos en la que los datos normalizados y agrupados se transforman en un recurso FHIR.|
|Unknown|Desconocido es el tipo de operación desconocido cuando se produce un error.|

### <a name="the-severity-of-the-error"></a>La gravedad del error

Esta propiedad representa la gravedad del error generado. A continuación se muestra una lista de valores posibles para esta propiedad.

|severity|Descripción|
|---------------|-----------|
|Advertencia|Existen algunos problemas menores en el proceso del flujo de datos, pero el procesamiento del mensaje del dispositivo no se detiene.|
|Error|Este mensaje aparece cuando el procesamiento de un mensaje del dispositivo específico encontró un error y otros mensajes pueden seguir ejecutándose según lo previsto.|
|Crítico|Este error se produce cuando hay un problema en el nivel de sistema con el conector de IoT y no se espera que se procese ningún mensaje.|

### <a name="the-type-of-the-error"></a>El tipo de error

Esta propiedad indica una categoría para un error determinado, que básicamente representa una agrupación lógica para un tipo de error similar. A continuación se muestra una lista de valores posibles para esta propiedad.

|Tipo de error|Descripción|
|----------|-----------|
|`DeviceTemplateError`|Este tipo de error está relacionado con las plantillas de asignación de dispositivos.|
|`DeviceMessageError`|Este tipo de error se produce al procesar un mensaje de dispositivo específico.|
|`FHIRTemplateError`|Este tipo de error está relacionado con las plantillas de asignación de FHIR.|
|`FHIRConversionError`|Este tipo de error se produce al transformar un mensaje en un recurso de FHIR.|
|`FHIRResourceError`|Este tipo de error se está relacionado con los recursos existentes en el servidor FHIR al que hace referencia el conector de IoT.|
|`FHIRServerError`|Este tipo de error se produce al comunicarse con el servidor de FHIR.|
|`GeneralError`|Este tipo de error trata sobre todos los demás tipos de errores.|

### <a name="the-name-of-the-error"></a>El nombre del error

Esta propiedad proporciona el nombre de un error específico. A continuación puede ver la lista de todos los nombres de error con su descripción y sus tipos de errores asociados, la gravedad y las fases del flujo de datos.

|Nombre del error|Descripción|Tipos de error|Gravedad del error|Fases del flujo de datos|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|Error que se produce cuando se encuentran varios recursos de paciente o dispositivo en el servidor FHIR para los id. respectivos presentes en el mensaje del dispositivo.|`FHIRResourceError`|Error|`FHIRConversion`|
|`TemplateNotFoundException`|No hay configurada una plantilla de asignación de FHIR o dispositivo con la instancia de un conector de IoT.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|El id. de la correlación no se especifica en la plantilla de asignación de dispositivos. `CorrelationIdNotDefinedException` es un error condicional que se produce solo cuando la observación de FHIR debe agrupar las medidas del dispositivo mediante un identificador de correlación porque no está configurado correctamente.|`DeviceMessageError`|Error|Normalización|
|`PatientDeviceMismatchException`|Este error se produce cuando el recurso de dispositivo en el servidor de FHIR tiene una referencia a un recurso Patient. Este tipo de error significa que no coincide con el identificador de Patient presente en el mensaje.|`FHIRResourceError`|Error|`FHIRConversionError`|
|`PatientNotFoundException`|El recurso FHIR del dispositivo asociado con el identificador del dispositivo presente en el mensaje del dispositivo no hace referencia a ningún recurso FHIR del paciente. Tenga en cuenta que este error solo se produce cuando la instancia del conector de IoT está configurada con el tipo de resolución *Lookup*.|`FHIRConversionError`|Error|`FHIRConversion`|
|`DeviceNotFoundException`|No existe ningún recurso de dispositivo en el servidor FHIR asociado con el identificador de dispositivo presente en el mensaje del dispositivo.|`DeviceMessageError`|Error|Normalización|
|`PatientIdentityNotDefinedException`|Este error ocurre cuando la expresión de análisis del identificador del paciente del mensaje del dispositivo no está configurada en la plantilla de asignación del dispositivo o el identificador del paciente no está en el mensaje del dispositivo. Tenga en cuenta que este error solo se produce cuando el tipo de resolución del conector de IoT está establecido en *Create*.|`DeviceTemplateError`|Crítico|Normalización|
|`DeviceIdentityNotDefinedException`|Este error ocurre cuando la expresión de análisis del identificador del dispositivo del mensaje del dispositivo no está configurada en la plantilla de asignación de dispositivos o el identificador del dispositivo no está en el mensaje del dispositivo.|`DeviceTemplateError`|Crítico|Normalización|
|`NotSupportedException`|Error al recibir un mensaje del dispositivo con un formato no compatible.|`DeviceMessageError`|Error|Normalización|

## <a name="creating-copies-of-the-iot-connector-conversion-mapping-json"></a>Creación de copias del archivo JSON de asignación de conversión del conector de IoT

La copia de los archivos de asignación del conector de IoT puede ser útil para editar y archivar fuera del sitio web de Azure Portal.

Las copias de los archivos de asignación deben proporcionarse al servicio de soporte técnico de Azure al abrir una incidencia de soporte técnico para ayudar en el proceso de solución de problemas.

> [!NOTE]
> En este momento, JSON es el único formato admitido para los archivos de asignación de Dispositivo y FHIR.

> [!TIP]
> Obtenga más información sobre [el archivo JSON de asignación de conversión de Dispositivo y FHIR](how-to-use-fhir-mapping-iot.md) del conector de Azure IoT para FHIR.

1. Seleccione la hoja **"Conector IoT (versión preliminar)"** en el lado izquierdo del espacio de trabajo de Azure Healthcare APIs.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="Seleccione la hoja Conector de IoT." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. Seleccione el nombre del **conector de IoT** del que va a copiar el archivo JSON de asignación de conversión.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Este proceso también se puede usar para copiar y guardar el contenido del archivo JSON de **Configure FHIR mapping** (Configurar asignación de FHIR).

3. Seleccione el contenido del archivo JSON y realice una operación de copia (por ejemplo: presione **Ctrl + C**). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. Realice una operación de pegado (por ejemplo, presione **Ctrl + V**) en un nuevo archivo dentro de un editor como Microsoft Visual Studio Code o Bloc de notas. Asegúrese de guardar el archivo con la extensión **.json**.

> [!TIP]
> Si va a abrir una [incidencia de soporte técnico de Azure](https://azure.microsoft.com/support/create-ticket/) para el conector de Azure IoT para FHIR, asegúrese de incluir copias de su archivo JSON de asignación de conversión para ayudar con el proceso de solución de problemas.

## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Información general del conector de IoT de Azure](iot-connector-overview.md)

FHIR es una marca registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso de HL7.
