---
title: Conversión de datos para las API de Azure Healthcare
description: Use el punto $convert de conexión de datos personalizados y las plantillas de convertidor de personalización para convertir datos en las API de atención sanitaria.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/11/2021
ms.author: ranku
ms.openlocfilehash: df201cf281fd24ebf288849d5192aa9355f3416d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787815"
---
# <a name="converting-your-data-to-fhir"></a>Conversión de los datos a FHIR

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar pública y se proporciona sin un contrato de nivel de servicio. No se recomienda para las cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

El $convert personalizado de datos de datos en el servicio FHIR está diseñado para la conversión de datos de diferentes tipos de datos a FHIR. Usa el motor de la plantilla Liquid y las plantillas del proyecto [FHIR Converter](https://github.com/microsoft/FHIR-Converter) como plantillas predeterminadas. Estas plantillas de conversión se pueden personalizar según sea necesario. Actualmente admite dos tipos de conversión, **C-CDA a FHIR** y **hl7v2 a conversión de FHIR.**

## <a name="use-the-convert-data-endpoint"></a>Uso del punto de conexión $convert-data

La `$convert-data` operación se integra en el servicio FHIR para ejecutarse como parte del servicio. Después de habilitar en el servidor, puede realizar llamadas API al servidor `$convert-data` para convertir los datos en FHIR:

`https://<<FHIR service base URL>>/$convert-data`

### <a name="parameter-resource"></a>Recurso de parámetro

$convert-data toma un [recurso Parameter](http://hl7.org/fhir/parameters.html) en el cuerpo de la solicitud, como se describe en la tabla siguiente. En el cuerpo de la solicitud de llamada API, incluiría los parámetros siguientes:

| Nombre de parámetro      | Descripción | Valores aceptados |
| ----------- | ----------- | ----------- |
| inputData      | Datos que se van a convertir. | Para `Hl7v2` : cadena <br> Para `Ccda` : XML|
| inputDataType   | Tipo de datos de entrada. | ```HL7v2```, ``Ccda`` |
| templateCollectionReference | Referencia a una colección de plantillas de imágenes de [OCI](https://github.com/opencontainers/image-spec) [Azure Container Registry (ACR).](https://azure.microsoft.com/services/container-registry/) Es la imagen que contiene las plantillas de Liquid que se usarán para la conversión. Puede ser una referencia a las plantillas predeterminadas o a una imagen de plantilla personalizada registrada en el servicio FHIR. Consulte a continuación para obtener información sobre cómo personalizar las plantillas, hospedar las de ACR y registrarse en el servicio FHIR. | Para plantillas predeterminadas: <br> Para **las plantillas predeterminadas de HL7v2:** <br>```microsofthealth/fhirconverter:default``` <br>``microsofthealth/hl7v2templates:default``<br>Para **las plantillas predeterminadas de C-CDA:**``microsofthealth/ccdatemplates:default`` <br><br>Para plantillas personalizadas: <br> \<RegistryServer\>/\<imageName\>@\<imageDigest\>, \<RegistryServer\>/\<imageName\>:\<imageTag\> |
| rootTemplate | La plantilla raíz que se usa al transformar los datos. | Para **HL7v2:**<br>```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04```<br><br> Para **C-CDA:**<br>```CCD```, `ConsultationNote`, `DischargeSummary`, `HistoryandPhysical`, `OperativeNote`, `ProcedureNote`, `ProgressNote`, `ReferralNote`, `TransferSummary` |

> [!WARNING]
> Las plantillas predeterminadas se lanzan bajo licencia MIT y no **son** compatibles con Soporte técnico de Microsoft.
>
> Las plantillas predeterminadas solo se proporcionan para ayudarle a empezar a trabajar rápidamente. Pueden actualizarse al actualizar las versiones del Azure API for FHIR. Por lo tanto, debe  comprobar el comportamiento de conversión y hospedar su propia copia de plantillas en un Azure Container Registry, registrarlos en el Azure API for FHIR y usar en las llamadas API para tener un comportamiento coherente de conversión de datos en las distintas versiones de Azure API for FHIR.

#### <a name="sample-request"></a>Solicitud de ejemplo

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

#### <a name="sample-response"></a>Respuesta de ejemplo

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Personalización de plantillas

Puede usar la extensión [FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) para Visual Studio Code para personalizar las plantillas según sus necesidades. La extensión proporciona una experiencia de edición interactiva y facilita la descarga de plantillas publicadas por Microsoft y datos de ejemplo. Consulte la documentación de la extensión para obtener más detalles.

## <a name="host-and-use-templates"></a>Hospedaje y uso de plantillas

Se recomienda encarecidamente hospedar su propia copia de plantillas en ACR. Hay cuatro pasos implicados en el hospedaje de su propia copia de plantillas y su uso en la operación $convert datos:

1. Inserte la imagen en un una instancia de Azure Container Registry.
1. Habilite la identidad administrada en la instancia del servicio FHIR.
1. Proporcione acceso de ACR a la identidad administrada del servicio FHIR.
1. Registre los servidores de ACR en el servicio FHIR.
1. Opcionalmente, configure el firewall de ACR para el acceso seguro.

### <a name="push-templates-to-azure-container-registry"></a>Inserción de plantillas en Azure Container Registry

Después de crear una instancia de Azure Container Registry, puede usar el comando _FHIR Converter: Push Templates_ en la [extensión FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) para insertar las plantillas personalizadas en ACR. Como alternativa, puede usar la [herramienta CLI de administración de plantillas](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) para tal fin.

### <a name="enable-managed-identity-on-fhir-service"></a>Habilitación de la identidad administrada en el servicio FHIR

Vaya a la instancia del servicio FHIR en la Azure Portal y, a continuación, seleccione la **hoja** Identidad.
Cambie el estado a **On (Activar)** para habilitar la identidad administrada en el servicio FHIR.

![Habilitación de una entidad administrada](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-fhir-service"></a>Proporcionar acceso del servicio ACR a FHIR

1. Vaya a la **hoja Control de acceso (IAM).**

1. Seleccione **Agregar y,** a continuación, **seleccione Agregar asignación de roles** para abrir la página Agregar asignación de roles.

1. Asigne el [rol AcrPull.](../../role-based-access-control/built-in-roles.md#acrpull) 

   ![Página Agregar asignación de roles](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

Para más información sobre la asignación de roles en Azure Portal, consulte [Roles integrados de Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="register-the-acr-servers-in-fhir-service"></a>Registro de los servidores de ACR en el servicio FHIR

Puede registrar el servidor de ACR mediante el Azure Portal o mediante la CLI.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Registro del servidor de ACR mediante Azure Portal
Vaya a la hoja **Artifacts** en **Transformación de datos** en la instancia del servicio FHIR. Verá la lista de servidores de ACR registrados actualmente. Seleccione **Agregar** y, a continuación, seleccione el servidor del Registro en el menú desplegable. Tendrá que seleccionar Guardar **para** que el registro suba efecto. La aplicación del cambio y el reinicio de la instancia pueden tardar unos minutos.

#### <a name="registering-the-acr-server-using-cli"></a>Registro del servidor de ACR mediante la CLI
Puede registrar hasta 20 servidores de ACR en el servicio FHIR.

Instale la CLI de Healthcare API desde Azure PowerShell si es necesario:

```powershell
az extension add -n healthcareapis
```

Registre los servidores acr en el servicio FHIR siguiendo los ejemplos siguientes:

##### <a name="register-a-single-acr-server"></a>Registro de un servidor de ACR individual

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>Registro de varios servidores de ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>Configuración del firewall de ACR

Seleccione **Redes** de la cuenta de almacenamiento de Azure en el portal.

   :::image type="content" source="media/convert-data/networking-container-registry.png" alt-text="Registro de contenedor.":::


Seleccione **Redes seleccionadas**. 

En la **sección Firewall,** especifique la dirección IP en el **cuadro Intervalo de** direcciones. Agregue intervalos IP para permitir el acceso desde Internet o redes locales. 

En la tabla siguiente, encontrará la dirección IP de la región de Azure donde se aprovisiona el servicio FHIR.

|**Región de Azure**         |**Dirección IP pública** |
|:----------------------|:-------------------|
| Este de Australia       | 20.53.44.80       |
| Centro de Canadá       | 20.48.192.84      |
| Centro de EE. UU.           | 52.182.208.31     |
| Este de EE. UU.              | 20.62.128.148     |
| Este de EE. UU. 2            | 20.49.102.228     |
| EUAP de Este de EE. UU. 2       | 20.39.26.254      |
| Norte de Alemania        | 51.116.51.33      |
| Centro-oeste de Alemania | 51.116.146.216    |
| Japón Oriental           | 20.191.160.26     |
| Centro de Corea del Sur        | 20.41.69.51       |
| Centro-Norte de EE. UU     | 20.49.114.188     |
| Norte de Europa         | 52.146.131.52     |
| Norte de Sudáfrica   | 102.133.220.197   |
| Centro-sur de EE. UU.     | 13.73.254.220     |
| Sudeste de Asia       | 23.98.108.42      |
| Norte de Suiza    | 51.107.60.95      |
| Sur de Reino Unido             | 51.104.30.170     |
| Oeste de Reino Unido              | 51.137.164.94     |
| Centro-Oeste de EE. UU.      | 52.150.156.44     |
| Oeste de Europa          | 20.61.98.66       |
| Oeste de EE. UU. 2            | 40.64.135.77      |


> [!NOTE]
> Los pasos anteriores son similares a los pasos de configuración descritos en el documento Cómo exportar datos de FHIR. Para obtener más información, [vea Exportación segura a Azure Storage](./export-data.md#secure-export-to-azure-storage)

### <a name="verify"></a>Comprobación

Realice una llamada a la API de $convert-data, en la que especifique la referencia de la plantilla en el parámetro templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`
