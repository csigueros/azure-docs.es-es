---
title: Solución de problemas de los conectores Dynamics 365, Dataverse (Common Data Service) y Dynamics CRM
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con los conectores Dynamics 365, Dataverse (Common Data Service) y Dynamics CRM en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8552cbcb79522933e0b2cf9ffe369cefdf900b79
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390458"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas con los conectores Dynamics 365, Dataverse (Common Data Service) y Dynamics CRM en Azure Data Factory y Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con los conectores Dynamics 365, Dataverse (Common Data Service) y Dynamics CRM en Azure Data Factory y Azure Synapse.

## <a name="error-code-dynamicscreateserviceclienterror"></a>Código de error: DynamicsCreateServiceClientError

- **Mensaje**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Causa**: Se trata de un problema transitorio en el servidor de Dynamics.

- **Recomendación:**  vuelva a ejecutar la canalización. Si sigue sin funcionar, intente reducir el paralelismo. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Dynamics.


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Faltan columnas al importar un esquema u obtener una vista previa de los datos

- **Síntomas**: Faltan algunas columnas al importar un esquema u obtener una vista previa de los datos. Mensaje de error: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: este problema es así por naturaleza, ya que las canalizaciones de Data Factory y Synapse no puede mostrar las columnas que no contienen valores en los 10 primeros registros. Asegúrese de que las columnas que ha agregado tengan el formato correcto. 

- **Recomendación:** Agregue manualmente las columnas en la pestaña Asignación.


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Código de error: DynamicsMissingTargetForMultiTargetLookupField

- **Mensaje**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa**: La columna de destino no existe en el origen o en la asignación de columnas.

- **Recomendación:**  
  1. Asegúrese de que el origen contenga la columna de destino. 
  2. Agregue la columna de destino en la asignación de columnas. Asegúrese de que la columna de receptor tenga el formato *{fieldName}@EntityReference* .


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Código de error: DynamicsInvalidTargetForMultiTargetLookupField

- **Mensaje**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Causa**: Se ha proporcionado un nombre de entidad incorrecto como entidad de destino de un campo de búsqueda de varios destinos.

- **Recomendación:**  Proporcione un nombre de entidad válido para el campo de búsqueda de varios destinos.


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Código de error: DynamicsInvalidTypeForMultiTargetLookupField

- **Mensaje**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Causa**: El valor de la columna de destino no es una cadena.

- **Recomendación:**  Proporcione una cadena válida en la columna de destino de búsqueda de varios destinos.


## <a name="error-code-dynamicsfailedtorequetserver"></a>Código de error: DynamicsFailedToRequetServer

- **Mensaje**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Causa**: El servidor de Dynamics es inestable o no es accesible, o bien en la red se experimentan problemas.

- **Recomendación:**  Para conocer los detalles, compruebe la conectividad de red o el registro del servidor de Dynamics. Si necesita ayuda adicional, póngase en contacto con el soporte técnico de Dynamics.


## <a name="error-code-dynamicsfailedtoconnect"></a>Código de error: DynamicsFailedToConnect 
 
 - **Mensaje**: `Failed to connect to Dynamics: %message;` 
 
 - **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

    | Análisis de las causas                                               | Recomendación                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Aparece `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` o, en caso contrario, `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'`, si el caso de uso cumple **todas** las condiciones siguientes: <li>Se va a conectar a Dynamics 365, Common Data Service o Dynamics CRM.</li><li>Va a usar la autenticación de Office365.</li><li>El inquilino y el usuario están configurados en Azure Active Directory para el [acceso condicional](../active-directory/conditional-access/overview.md), o bien si la autenticación multifactor es necesaria (consulte este [vínculo](/powerapps/developer/data-platform/authenticate-office365-deprecation) para ver la documentación de Dataverse).</li>  En estas circunstancias, la conexión que se use funcionará correctamente hasta el 8/6/2021. A partir del 9/6/2021, la conexión comenzará a producir un error debido al desuso del servicio de detección regional (consulte este [vínculo](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)).| Si el inquilino y el usuario están configurados en Azure Active Directory para el [acceso condicional](../active-directory/conditional-access/overview.md), o bien, si la autenticación multifactor es necesaria, deberá utilizar una "entidad de servicio de Azure AD" para autenticarse después del 8/6/2021. Consulte este [vínculo](./connector-dynamics-crm-office-365.md#prerequisites) para obtener pasos detallados.|
    |Si aparece `Office 365 auth with OAuth failed` en el mensaje de error, es posible que el servidor tenga algunas configuraciones no compatibles con OAuth.| <li>Para obtener ayuda, póngase en contacto con el equipo de soporte técnico de Dynamics con el mensaje de error detallado.</li><li>Use la autenticación de la entidad de servicio, puede consultar este artículo: [Ejemplo: Dynamics en línea con la entidad de servicio de Azure AD y la autenticación de certificados](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication).</li>
    |Si aparece `Unable to retrieve authentication parameters from the serviceUri` en el mensaje de error, puede que haya escrito una dirección URL de servicio de Dynamics incorrecta o aplicado un proxy o firewall para interceptar el tráfico. |<li>Asegúrese de haber indicado el URI de servicio correcto en el servicio vinculado.</li><li>Si usa el IR autohospedado, asegúrese de que el firewall o proxy no intercepten las solicitudes al servidor de Dynamics.</li> |
    |Si aparece `An unsecured or incorrectly secured fault was received from the other party` en el mensaje de error, se han obtenido respuestas inesperadas del lado servidor.  | <li>Asegúrese de que el nombre de usuario y la contraseña sean correctos si usa la autenticación de Office 365. </li><li> Asegúrese de haber escrito el URI de servicio correcto.</li><li>Si usa la dirección URL de CRM regional (la dirección URL tiene un número después de "crm"), asegúrese de usar el identificador regional correcto.</li><li>Para obtener ayuda, póngase en contacto con el equipo de soporte técnico de Dynamics.</li>|
    |Si aparece `No Organizations Found` en el mensaje de error, el nombre de la organización es incorrecto o ha usado un identificador de región de CRM incorrecto en la dirección URL del servicio.|<li>Asegúrese de haber escrito el URI de servicio correcto.</li><li>Si usa la dirección URL de CRM regional (la dirección URL tiene un número después de "crm"), asegúrese de usar el identificador regional correcto.</li><li>Para obtener ayuda, póngase en contacto con el equipo de soporte técnico de Dynamics.</li>|
    | Si aparece `401 Unauthorized` y un mensaje de error relacionado con AAD, hay un problema con la entidad de servicio. |Siga las instrucciones del mensaje de error para corregir el problema de la entidad de servicio. |
   |Para otros errores, el problema suele encontrarse en el lado servidor. |Use [XrmToolBox](https://www.xrmtoolbox.com/) para establecer la conexión. Si el error persiste, póngase en contacto con el equipo de soporte técnico de Dynamics para obtener ayuda. |

## <a name="error-code-dynamicsoperationfailed"></a>Código de error: DynamicsOperationFailed 
 
- **Mensaje**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Causa**: Error en la operación en el lado servidor. 

- **Recomendación**: Extraiga el código de error de la operación de Dynamics que aparece en el mensaje de error: `Dynamics operation failed with error code: {code}`, y consulte el artículo [Códigos de error de servicio web](/powerapps/developer/data-platform/org-service/web-service-error-codes) para obtener información más detallada. Si es necesario, puede ponerse en contacto con el equipo de soporte técnico de Dynamics. 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>Código de error: DynamicsInvalidFetchXml 
  
- **Mensaje**: `The Fetch Xml query specified is invalid.` 

- **Causa**: Existe un error en el XML de captura.  

- **Recomendación**: Corrija el error en el XML de captura. 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>Código de error: DynamicsMissingKeyColumns 
 
- **Mensaje**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Causa**: Los datos de origen no contienen la columna de clave para la entidad receptora. 

- **Recomendación**: Confirme que las columnas de clave estén en los datos de origen o asigne una columna de origen a la columna de clave de la entidad receptora. 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>Código de error: DynamicsPrimaryKeyMustBeGuid 
 
- **Mensaje**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **Causa**: El tipo de la columna de clave principal no es "Guid". 
 
- **Recomendación**: Asegúrese de que la columna de clave principal de los datos de origen sea de tipo "Guid". 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>Código de error: DynamicsAlternateKeyNotFound 
 
- **Mensaje**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Causa**: La clave alternativa proporcionada no existe, lo que puede deberse a nombres de clave incorrectos o permisos insuficientes. 
 
- **Recomendación:** <br/> 
    - Corrija los errores tipográficos en el nombre de clave.<br/> 
    - Asegúrese de tener permisos suficientes en la entidad. 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>Código de error: DynamicsInvalidSchemaDefinition 
 
- **Mensaje**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Causa**: Falta la propiedad "type" en las columnas receptoras de la asignación de columnas. 
 
- **Recomendación**: Puede agregar la propiedad "type" a esas columnas de la asignación de columnas mediante el editor JSON en el portal. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
