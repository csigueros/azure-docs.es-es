---
title: Operación $validate en recursos de FHIR con perfiles en Azure API for FHIR
description: Operación $validate de recursos de FHIR con perfiles
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 06/07/2021
ms.author: ginle
ms.openlocfilehash: 72fc5ab9c906453f88b510520b40fd03d41aa2ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780731"
---
# <a name="how-to-validate-fhir-resources-against-profiles-in-azure-api-for-fhir"></a>Cómo validar los recursos de FHIR con perfiles en Azure API for FHIR

HL7 FHIR define una manera estándar e interoperable de almacenar e intercambiar datos sanitarios. Incluso dentro de la especificación FHIR base, puede ser útil definir reglas o extensiones adicionales en función del contexto en el que se usa FHIR. Para estos usos específicos del contexto de FHIR, se emplean **perfiles de FHIR** para la capa adicional de especificaciones.

El [perfil de FHIR](https://www.hl7.org/fhir/profiling.html) describe contexto adicional, como restricciones o extensiones, en un recurso representado como `StructureDefinition`. El estándar HL7 FHIR define un conjunto de recursos base, y estos recursos base estándar tienen definiciones genéricas. El perfil de FHIR permite limitar y personalizar las definiciones de recursos mediante restricciones y extensiones.

Azure API for FHIR permite validar recursos con perfiles para ver si los recursos se ajustan a los perfiles. Este artículo le guía por los conceptos básicos del perfil de FHIR y cómo usar `$validate` para validar recursos con los perfiles al crear y actualizar recursos.

## <a name="fhir-profile-the-basics"></a>Perfil de FHIR: aspectos básicos

Un perfil establece contexto adicional en el recurso, normalmente representado como un recurso `StructureDefinition`. `StructureDefinition` define un conjunto de reglas sobre el contenido de un recurso o un tipo de datos, como qué campos tiene un recurso y qué valores pueden tomar estos campos. Por ejemplo, los perfiles pueden restringir la cardinalidad (mediante el establecimiento de la cardinalidad máxima en 0 para descartar el elemento, entre otros), restringir el contenido de un elemento a un único valor fijo o definir las extensiones necesarias para el recurso. También puede especificar restricciones adicionales en un perfil existente. Un objeto `StructureDefinition` se identifica mediante su dirección URL canónica:

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

En el campo `{profile}`, especifique el nombre del perfil.

Por ejemplo:

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` es un perfil base que requiere información sobre la dirección registrada del nacimiento del paciente.
- `http://hl7.org/fhir/StructureDefinition/bmi` es otro perfil base que define cómo representar observaciones del índice de masa corporal (IMC).
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` es un perfil de US Core que establece las expectativas mínimas para un recurso `AllergyIntolerance` asociado a un paciente e identifica campos obligatorios, como extensiones y conjuntos de valores.

Cuando un recurso se ajusta a un perfil, su perfil se especifica en un recurso dentro del campo `profile`.

```json
{
  "resourceType" : "Patient",
  "id" : "ExamplePatient1",
  "meta" : {
    "lastUpdated" : "2020-10-30T09:48:01.8512764-04:00",
    "source" : "Organization/PayerOrganizationExample1",
    "profile" : [
      "http://hl7.org/fhir/us/carin-bb/StructureDefinition/C4BB-Patient"
    ]
  },
```

### <a name="base-profile-and-custom-profile"></a>Perfil base y perfil personalizado

Hay dos tipos de perfiles: perfil base y perfil personalizado. Un perfil base es un objeto `StructureDefinition` base al que debe ajustarse un recurso, y se ha definido mediante recursos base como `Patient` o `Observation`. Por ejemplo, un perfil de índice de masa corporal (IMC) `Observation` comenzaría de la siguiente forma:

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

Un perfil personalizado es un conjunto de restricciones adicionales sobre un perfil base, que restringen o agregan parámetros de recursos que no forman parte de la especificación base. El perfil personalizado resulta útil porque le permite personalizar sus propias definiciones de recursos mediante la especificación de las restricciones y extensiones en el recurso base existente. Por ejemplo, es posible que quiera crear un perfil que muestre instancias del recurso `AllergyIntolerance` basadas en los sexos de `Patient`, en cuyo caso crearía un perfil personalizado sobre un perfil `Patient` existente con perfil `AllergyIntolerance`.

> [!NOTE]
> Los perfiles personalizados deben compilarse sobre el recurso base y no pueden estar en conflicto con tal recurso. Por ejemplo, si un elemento tiene una cardinalidad de 1..1, el perfil personalizado no puede hacerla opcional.

Los perfiles personalizados también se especifican mediante varias guías de implementación. Algunas guías de implementación comunes son:

|Nombre |URL
|---- |----
Us Core |<https://www.hl7.org/fhir/us/core/>
CARIN Blue Button |<http://hl7.org/fhir/us/carin-bb/>
Da Vinci Payer Data Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>Acceso a perfiles y almacenamiento de perfiles

### <a name="storing-profiles"></a>Almacenamiento de perfiles

Para almacenar perfiles en el servidor, puede realizar una solicitud `POST`:

```rest
POST http://<your FHIR service base URL>/{Resource}
```

Donde el campo `{Resource}` se reemplazará por `StructureDefinition`, y se aplicaría `POST` a su recurso `StructureDefinition` hacia el servidor en formato `JSON` o `XML`. Por ejemplo, si deseara almacenar el perfil `us-core-allergyintolerance`, haría lo siguiente:

```rest
POST http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

Donde se almacenaría y recuperaría el perfil de US Core Allergy Intolerance:

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

La mayoría de los perfiles tienen el tipo de recurso `StructureDefinition`, pero también pueden ser de los tipos `ValueSet` y `CodeSystem`, que son recursos de [terminología](http://hl7.org/fhir/terminologies.html). Por ejemplo, si aplica `POST` a un perfil `ValueSet` en un formulario JSON, el servidor devolverá el perfil almacenado con el objeto `id` asignado para el perfil, tal como lo haría con `StructureDefinition`. A continuación se muestra un ejemplo que se obtiene al cargar un perfil de [Condition Severity](https://www.hl7.org/fhir/valueset-condition-severity.html), que especifica los criterios para una clasificación de gravedad de condición o diagnóstico:

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

Puede ver que `resourceType` es de tipo `ValueSet`, y el objeto `url` del perfil también especifica que es de tipo `ValueSet`: `"http://hl7.org/fhir/ValueSet/condition-severity"`.

### <a name="viewing-profiles"></a>Visualización de perfiles

Puede acceder a los perfiles personalizados existentes en el servidor mediante una solicitud `GET`. Todos los perfiles válidos, como los perfiles con direcciones URL canónicas válidas de las guías de implementación, deben ser accesibles mediante la consulta:

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

Donde el campo `{canonicalUrl}` se reemplazaría por la dirección URL canónica del perfil.

Por ejemplo, si desea ver el perfil de recursos `Goal` de US Core:

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

Esto devolverá el recurso `StructureDefinition` para el perfil de US Core Goal, que comenzará de la siguiente forma:

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

Nuestro servidor de FHIR no devuelve instancias `StructureDefinition` para los perfiles base, pero se pueden encontrar fácilmente en el sitio web de HL7, como:

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>Perfiles en la instrucción de funcionalidad

`Capability Statement` enumera todos los comportamientos posibles del servidor de FHIR que se usarán como una instrucción de la funcionalidad del servidor, como definiciones de estructura y conjuntos de valores. Azure API for FHIR actualiza la instrucción de funcionalidad con información sobre los perfiles cargados y almacenados de las formas siguientes:

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

Estos mostrarán toda la especificación del perfil que describe la compatibilidad general con el recurso, incluidas las restricciones de cardinalidad, enlaces, extensiones u otras restricciones. Por lo tanto, cuando aplica `POST` a un perfil en el formato de `StructureDefinition`, y aplica `GET` a los metadatos de recursos para ver la instrucción de funcionalidad completa, verá junto al parámetro `supportedProfiles` todos los detalles sobre el perfil que ha cargado.

Por ejemplo, si aplica `POST` a un perfil de US Core Patient, que empieza de este modo:

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

Y envía una solicitud `GET` para su `metadata`:

```rest
GET http://<your FHIR service base URL>/metadata
```

Se le devolverá un valor de `CapabilityStatement` que incluye la siguiente información sobre el perfil de US Core Patient que ha cargado en el servidor de FHIR:

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>Validación de recursos con los perfiles

Los recursos de FHIR, como `Patient` o `Observation`, pueden expresar su conformidad con perfiles específicos. Esto permite a nuestro servidor de FHIR **validar** los recursos dados con los perfiles asociados o los perfiles especificados. Validar un recurso con perfiles significa comprobar si el recurso se ajusta a los perfiles, incluidas las especificaciones enumeradas en `Resource.meta.profile` o en una guía de implementación.

Hay dos maneras de validar el recurso. En primer lugar, puede usar la operación `$validate` en un recurso que ya está en el servidor de FHIR. En segundo lugar, puede aplicarle `POST` en el servidor como parte de una operación `Update` o `Create` del recurso. En ambos casos, puede decidir a través de la configuración del servidor de FHIR qué hacer cuando el recurso no se ajusta al perfil deseado.

### <a name="using-validate"></a>Uso de $validate

La operación `$validate` comprueba si el perfil proporcionado es válido y si el recurso se ajusta al perfil especificado. Como se mencionó en las [especificaciones de HL7 FHIR](https://www.hl7.org/fhir/resource-operation-validate.html), también puede especificar `mode` para `$validate`, como `create` y `update`:

- `create`: el servidor comprueba que el contenido del perfil es único respecto a los recursos existentes y que es aceptable que se cree como un nuevo recurso.
- `update`: comprueba que el perfil es una actualización con respecto al recurso existente designado (por ejemplo, que no se realizan cambios en los campos inmutables).

El servidor siempre devolverá `OperationOutcome` como resultados de la validación.

#### <a name="validating-an-existing-resource"></a>Validación de un recurso existente

Para validar un recurso existente, use `$validate` en una solicitud `GET`:

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

Por ejemplo:

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

En el ejemplo anterior, validaría el recurso `Patient` `a6e11662-def8-4dde-9ebc-4429e68d130e` existente. Si es válido, recibirá un valor `OperationOutcome` como el siguiente:

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

Si el recurso no es válido, recibirá un código de error y un mensaje de error con detalles sobre por qué el recurso no es válido. Un error `4xx` o `5xx` significa que no se pudo realizar la validación en sí, y se desconoce si el recurso es válido o no. Un ejemplo `OperationOutcome` devuelto con mensajes de error podría ser parecido al siguiente:

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

En este ejemplo anterior, el recurso no se ajustaba al perfil `Patient` proporcionado que requería un valor de identificador de paciente y un sexo.

Si desea especificar un perfil como parámetro, puede especificar la dirección URL canónica con la que se va a validar el perfil, como el ejemplo siguiente para el perfil base HL7 para `heartrate`:

```rest
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>Validación de un recurso nuevo

Si desea validar un nuevo recurso que va a cargar en el servidor, puede realizar una solicitud `POST`:

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

Por ejemplo:

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

Esta solicitud creará el nuevo recurso que está especificando en la carga de la solicitud, ya sea en formato JSON o XML, y validará el recurso cargado. A continuación, devolverá `OperationOutcome` como resultado de la validación en el nuevo recurso.

### <a name="validate-on-resource-create-or-resource-update"></a>Validación en el recurso CREATE o el recurso UPDATE

Puede elegir cuándo desea validar el recurso, como en el recurso CREATE o UPDATE. Puede especificarlo en la configuración del servidor, en `CoreFeatures`:

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

Si el recurso se ajusta al `Resource.meta.profile` proporcionado y el perfil está presente en el sistema, el servidor actuará según la configuración anterior. Si el perfil proporcionado no está presente en el servidor, la solicitud de validación se omitirá y se deja en `Resource.meta.profile`.
La validación suele ser una operación costosa, por lo que normalmente solo se ejecuta en servidores de prueba o en un pequeño subconjunto de recursos; por ello, es importante tener estas maneras de activar o desactivar la validación en el lado servidor. Si la configuración del servidor especifica la exclusión de la validación en la creación/actualización de recursos, el usuario puede anular el comportamiento especificándolo en el objeto `header` de la solicitud de creación/actualización:

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre los perfiles de FHIR y cómo validar los recursos en los perfiles mediante $validate. Para obtener información sobre otras características admitidas de Azure API for FHIR, consulte:

>[!div class="nextstepaction"]
>[Características admitidas de FHIR](fhir-features-supported.md)
