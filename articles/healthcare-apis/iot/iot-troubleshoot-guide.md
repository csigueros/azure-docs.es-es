---
title: 'Guía de solución de problemas del conector de IoT y procedimientos: API de Azure Healthcare'
description: Este artículo ayuda a los usuarios a solucionar problemas de mensajes de error comunes del conector de IoT, condiciones y cómo copiar archivos de asignación.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a3af00eaca90c2ecdbbdcc4f6cce5231ffefcc0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719454"
---
# <a name="iot-connector-troubleshooting-guide"></a>Guía de solución de problemas del conector de IoT

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general. 

> [!TIP]
> Al abrir una [vale de soporte técnico](https://azure.microsoft.com/support/create-ticket/) de Azure para el conector de IoT, incluya copias de las asignaciones de destino de dispositivos y FHIR para ayudar en el proceso de solución de problemas.

En este artículo se proporcionan los pasos para solucionar problemas de condiciones y mensajes de error comunes del conector de IoT. También aprenderá a crear copias de las asignaciones de destino device and Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) del conector de IoT. Además, puede usar las copias de asignación de destino de dispositivo y FHIR para editar y archivar fuera del Azure Portal. 

## <a name="device-and-fhir-destination-mappings-validations"></a>Validaciones de asignaciones de destino de dispositivos y FHIR

En esta sección se describe el proceso de validación que realiza el conector de IoT. El proceso de validación valida las asignaciones de destino del dispositivo y FHIR antes de permitir que se guarden para su uso. Estos elementos son necesarios en las asignaciones de destino de dispositivo y FHIR.

> [!TIP]
> Consulte la herramienta [Data Mapper](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) del conector IoMT para editar, probar y solucionar problemas de las asignaciones de destino de dispositivos y FHIR del conector ioT. Exporte las asignaciones para cargar en el conector de IoT Azure Portal use con la versión [de código abierto](https://github.com/microsoft/iomt-fhir) del conector de IoT.

**Asignaciones de dispositivos**

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
>Cuando se coloca o quita un dispositivo IoMT portátil, los elementos no tienen ningún valor, excepto un nombre que el conector de IoT coincide y emite. En la conversión de FHIR, el conector de IoT lo asigna a un concepto con capacidad de código basado en el tipo semántico. Esto significa que no se rellena ningún valor real.

**Asignaciones de destino de FHIR**

|Elemento|Obligatorio|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> Este es el único elemento de asignación de destino de FHIR necesario validado en este momento.

## <a name="error-messages-and-fixes"></a>Correcciones y mensajes de error

### <a name="iot-connector-resource"></a>Recurso del conector de IoT

|Message|Visualización|Condición|Fix| 
|-------|---------|---------|---|
|Se ha alcanzado el número máximo de tipos `iotconnectors` de recursos.|API y Azure Portal|Se alcanza la cuota de suscripción del conector de IoT (el valor predeterminado es 25 por suscripción).|Elimine una de las instancias existentes del conector de IoT. Use otra suscripción que no haya alcanzado la cuota de suscripción. Solicite un aumento de la cuota de suscripción.
|Asignación `deviceMapping` no válida. Errores de validación: {Lista de errores}|API y Azure Portal|El `properties.deviceMapping` proporcionado en la solicitud de aprovisionamiento de recursos del conector de IoT no es válido.|Corrija los errores del JSON de asignación proporcionado en la propiedad `properties.deviceMapping`.
|`fullyQualifiedEventHubNamespace` es null, está vacío o tiene un formato incorrecto.|API y Azure Portal|La solicitud de aprovisionamiento del conector de IoT `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` no es válida.|Actualice el conector de IoT `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` al formato correcto. Debe ser `{YOUR_NAMESPACE}.servicebus.windows.net`.
|Los recursos antecesores deben aprovisionarse por completo antes de que se pueda aprovisionar un recurso secundario.|API|El área de trabajo principal sigue aprovisionando.|Espere hasta que se haya completado el aprovisionamiento del área de trabajo principal y envíe de nuevo la solicitud de aprovisionamiento.
|La propiedad `Location` de los recursos secundarios debe coincidir con la propiedad `Location` de los recursos principales.|API|La propiedad de solicitud de aprovisionamiento del conector de IoT `location` es diferente de la propiedad workspace `location` primaria.|Establezca la `location` propiedad del conector de IoT en la solicitud de aprovisionamiento en el mismo valor que la propiedad workspace `location` primaria.

### <a name="destination-resource"></a>Recurso de destino

|Message|Visualización|Condición|Fix| 
|-------|---------|---------|---|
|Se ha alcanzado el número máximo de tipos `iotconnectors/destinations` de recursos.|API y Azure Portal|Se alcanza la cuota de recursos de destino del conector de IoT y el valor predeterminado es 1 por conector de IoT.|Elimine la instancia existente del recurso de destino del conector de IoT. Solo se permite un recurso de destino por conector de IoT.
|El valor `fhirServiceResourceId` proporcionado no es válido.|API y Azure Portal|El proporcionado en la solicitud de aprovisionamiento de recursos de destino no es un identificador de recurso válido para una instancia del servicio FHIR de `properties.fhirServiceResourceId` las API de Azure Healthcare.|Asegúrese de que el identificador de recurso tiene el formato correcto y asegúrese de que el identificador de recurso es para una instancia de FHIR de Las API de Azure Healthcare. El formato debe ser `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`.
|Los recursos antecesores deben aprovisionarse por completo antes de que se pueda aprovisionar un recurso secundario.|API|El área de trabajo primaria o el conector de IoT primario siguen aprovisionamiento.|Espere hasta que se complete el aprovisionamiento del área de trabajo principal o del conector de IoT primario y, a continuación, envíe de nuevo la solicitud de aprovisionamiento.
|La propiedad `Location` de los recursos secundarios debe coincidir con la propiedad `Location` de los recursos principales.|API|La propiedad Solicitud de `location` aprovisionamiento de destino es diferente de la propiedad principal del conector de `location` IoT.|Establezca la `location` propiedad de Destination en la solicitud de aprovisionamiento en el mismo valor que la propiedad principal del conector de `location` IoT.

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>¿Por qué no se muestran los datos del conector de IoT en el servicio FHIR?

|Posibles problemas|Correcciones|
|----------------|-----|
|Todavía se están procesando los datos.|Los datos salen hacia el servicio FHIR en lotes (cada 15 minutos aproximadamente).  Es posible que los datos se sigan procesando y que se necesite un tiempo adicional para que los datos se conserven en el servicio FHIR.|
|No se ha configurado la asignación de dispositivos.|Configure y guarde la asignación de dispositivos conforme.|
|No se ha configurado la asignación de destino de FHIR.|Configure y guarde la asignación de destino de FHIR conforme.|
|El mensaje del dispositivo no contiene una expresión esperada definida en la asignación de dispositivos.|Compruebe `JsonPath` las expresiones definidas en los tokens de coincidencia de asignación de dispositivos definidos en el mensaje del dispositivo.|
|No se ha creado un recurso de dispositivo en el servicio FHIR (tipo de resolución: solo Lookup)*.|Cree un recurso de dispositivo válido en el servicio FHIR. Asegúrese de que el recurso de dispositivo contenga un id. que coincida con el id. de dispositivo proporcionado en el mensaje entrante.|
|No se ha creado un recurso Patient en el servicio FHIR (tipo de resolución: solo Lookup)*.|Cree un recurso Patient válido en el servicio FHIR.|
|La referencia `Device.patient` no está establecida o la referencia no es válida (tipo de resolución: solo Lookup)*.|Asegúrese de que el recurso de dispositivo contenga una [referencia](https://www.hl7.org/fhir/device-definitions.html#Device.patient) válida a un recurso de paciente.| 

*Guía [de inicio rápido de referencia:](deploy-iot-connector-in-azure.md) Implementación del conector de IoT mediante Azure Portal para obtener una descripción funcional de los tipos de resolución del conector de IoT (por ejemplo: Búsqueda o creación).

### <a name="the-operation-performed-by-iot-connector"></a>Operación realizada por el conector de IoT

Esta propiedad representa la operación que realiza el conector de IoT cuando se ha producido el error. Por lo general, una operación representa la fase del flujo de datos durante el procesamiento de un mensaje del dispositivo. A continuación se muestra una lista de valores posibles para esta propiedad.

> [!NOTE]
> Para obtener información sobre las distintas fases del flujo de datos en el conector de IoT, consulte [Flujo de datos del conector de IoT.](iot-data-flow.md)

|Fase del flujo de datos|Descripción|
|---------------|-----------|
|Configuración|La fase de flujo de datos de configuración es la operación específica para configurar la instancia del conector de IoT.|
|Normalización|La normalización es la fase de flujo de datos donde se normalizan los datos del dispositivo.|
|Agrupación|Fase de flujo de datos de agrupación donde se agrupan los datos normalizados.|
|FHIRConversion|FHIRConversion es la fase de flujo de datos donde los datos agrupados normalizados se transforman en un recurso de FHIR.|
|Unknown|Desconocido es el tipo de operación desconocido cuando se produce un error.|

### <a name="the-severity-of-the-error"></a>La gravedad del error

Esta propiedad representa la gravedad del error generado. A continuación se muestra una lista de valores posibles para esta propiedad.

|severity|Descripción|
|---------------|-----------|
|Advertencia|Existen algunos problemas menores en el proceso del flujo de datos, pero el procesamiento del mensaje del dispositivo no se detiene.|
|Error|Este mensaje aparece cuando el procesamiento de un mensaje del dispositivo específico encontró un error y otros mensajes pueden seguir ejecutándose según lo previsto.|
|Crítico|Este error se produce cuando existe algún problema de nivel de sistema con el conector de IoT y no se espera que se procese ningún mensaje.|

### <a name="the-type-of-error"></a>Tipo de error

Esta propiedad indica una categoría para un error determinado, que básicamente representa una agrupación lógica para un tipo de error similar. A continuación se muestra una lista de valores posibles para esta propiedad.

|Tipo de error|Descripción|
|----------|-----------|
|`DeviceTemplateError`|Este tipo de error está relacionado con la asignación de dispositivos.|
|`DeviceMessageError`|Este tipo de error se produce al procesar un mensaje de dispositivo específico.|
|`FHIRTemplateError`|Este tipo de error está relacionado con la asignación de destino de FHIR|
|`FHIRConversionError`|Este tipo de error se produce al transformar un mensaje en un recurso de FHIR.|
|`FHIRResourceError`|Este tipo de error está relacionado con los recursos existentes en el servicio FHIR a los que hace referencia el conector de IoT.|
|`FHIRServerError`|Este tipo de error se produce al comunicarse con el servicio FHIR.|
|`GeneralError`|Este tipo de error trata sobre todos los demás tipos de errores.|

### <a name="the-name-of-the-error"></a>El nombre del error

Esta propiedad proporciona el nombre de un error específico. A continuación puede ver la lista de todos los nombres de error con su descripción y sus tipos de errores asociados, la gravedad y las fases del flujo de datos.

|Nombre del error|Descripción|Tipos de error|Gravedad del error|Fases del flujo de datos|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|Este error se produce cuando se encuentran varios recursos de pacientes o dispositivos en el servicio FHIR para los identificadores respectivos presentes en el mensaje del dispositivo.|`FHIRResourceError`|Error|`FHIRConversion`|
|`TemplateNotFoundException`|Asignación de destino de dispositivo o FHIR que no está configurada con la instancia del conector de IoT.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|El identificador de correlación no se especifica en la asignación de dispositivos. `CorrelationIdNotDefinedException` es un error condicional que se produce solo cuando la observación de FHIR debe agrupar las medidas del dispositivo mediante un identificador de correlación porque no está configurado correctamente.|`DeviceMessageError`|Error|Normalización|
|`PatientDeviceMismatchException`|Este error se produce cuando el recurso de dispositivo en el servicio FHIR tiene una referencia a un recurso de paciente. Este tipo de error significa que no coincide con el identificador de Patient presente en el mensaje.|`FHIRResourceError`|Error|`FHIRConversionError`|
|`PatientNotFoundException`|El recurso FHIR del dispositivo asociado con el identificador del dispositivo presente en el mensaje del dispositivo no hace referencia a ningún recurso FHIR del paciente. Tenga en cuenta que este error solo se producirá cuando la instancia del conector de IoT esté configurada con el *tipo de* resolución de búsqueda.|`FHIRConversionError`|Error|`FHIRConversion`|
|`DeviceNotFoundException`|No existe ningún recurso de dispositivo en el servicio FHIR asociado al identificador de dispositivo presente en el mensaje del dispositivo.|`DeviceMessageError`|Error|Normalización|
|`PatientIdentityNotDefinedException`|Este error se produce cuando la expresión para analizar el identificador de pacientes del mensaje del dispositivo no está configurada en la asignación de dispositivos o el identificador de paciente no está presente en el mensaje del dispositivo. Tenga en cuenta que este error solo se produce cuando el tipo de resolución del conector de IoT está establecido en *Crear*.|`DeviceTemplateError`|Crítico|Normalización|
|`DeviceIdentityNotDefinedException`|Este error se produce cuando la expresión para analizar el identificador del dispositivo del mensaje del dispositivo no está configurada en la asignación de dispositivos o el identificador de dispositivo no está presente en el mensaje del dispositivo.|`DeviceTemplateError`|Crítico|Normalización|
|`NotSupportedException`|Error al recibir un mensaje del dispositivo con un formato no compatible.|`DeviceMessageError`|Error|Normalización|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>Creación de copias de las asignaciones de destino de dispositivo y FHIR del conector de IoT

Copiar asignaciones de conectores de IoT puede ser útil para editar y archivar fuera del sitio Azure Portal web.

Las copias de asignación se deben proporcionar al soporte técnico de Azure al abrir una vale de soporte técnico para ayudar con el proceso de solución de problemas.

> [!NOTE]
> JSON es el único formato admitido para las asignaciones de destino de dispositivo y FHIR en este momento.

> [!TIP]
> Más información sobre las asignaciones de [dispositivos](how-to-use-device-mappings.md) del conector de IoT y las [asignaciones de destino de FHIR](how-to-use-fhir-mappings.md)

1. Seleccione **"Conectores de IoT"** en el lado izquierdo del área de trabajo de las API de atención sanitaria.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="Seleccione Conectores de IoT." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. Seleccione el nombre del conector **de IoT** desde el que va a copiar las asignaciones de destino de dispositivo y FHIR.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Conector de IoT2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > Este proceso también se puede usar para copiar y guardar el contenido de la asignación de destino de FHIR **"Destino".**

3. Seleccione el contenido del archivo JSON y realice una operación de copia (por ejemplo: presione **Ctrl + C**). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Conector de IoT4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. Realice una operación de pegado (por ejemplo, presione **Ctrl + V**) en un nuevo archivo dentro de un editor como Microsoft Visual Studio Code o Bloc de notas. Asegúrese de guardar el archivo con la **extensión .json.**

> [!TIP]
> Si va a abrir una entrada de soporte técnico de [Azure](https://azure.microsoft.com/support/create-ticket/) para el conector de IoT, asegúrese de incluir copias de las asignaciones de destino de dispositivo y FHIR para ayudar con el proceso de solución de problemas.

## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Introducción al conector de IoT](iot-connector-overview.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
