---
title: Acceso a las API de Azure Healthcare mediante el cliente REST
description: En este artículo se explica cómo acceder a las API de atención sanitaria mediante la extensión de cliente REST en VSCode.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 615aaed8be507ed19314e7f7329d7ca2f15a5f0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042359"
---
# <a name="accessing-the-healthcare-apis-preview-using-the-rest-client-extension-in-visual-studio-code"></a>Acceso a las API de atención sanitaria (versión preliminar) mediante la extensión de cliente REST en Visual Studio Code

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a acceder a las API de atención sanitaria mediante la extensión de cliente [REST en Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

## <a name="install-rest-client-extension"></a>Instalación de la extensión de cliente REST

Seleccione el icono Extensiones en el panel izquierdo de la Visual Studio Code y busque "Cliente REST". Busque la [extensión de cliente REST e](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) instálla.

[![Extensión DE VSCode del cliente REST ](media/rest-install.png) ](media/rest-install.png#lightbox)

## <a name="create-a-http-file-and-define-variables"></a>Creación de un `.http` archivo y definición de variables

Cree un archivo en Visual Studio Code. Escriba una `GET` línea de comandos de solicitud en el archivo y guárdelo como `test.http` . El sufijo de `.http` archivo activa automáticamente el entorno de cliente REST. Haga clic en `Send Request` para obtener los metadatos. 

[![Enviar solicitud ](media/rest-send-request.png) ](media/rest-send-request.png#lightbox)

## <a name="get-client-application-values"></a>Obtener valores de aplicación cliente

> [!Important]
> Antes de llamar a la API REST del servidor FHIR (que no sea obtener los metadatos), debe completar el **[registro de la aplicación.](register-application.md)** Anote el identificador de inquilino de **Azure,** el **identificador de** cliente, el secreto **de cliente** y la dirección URL **del servicio.**

Aunque puede usar valores como el identificador de cliente directamente en las llamadas a la API REST, es una buena práctica que defina algunas variables para estos valores y use las variables en su lugar.

En el `test.http` archivo, incluya la siguiente información obtenida al registrar la aplicación: 

```
### REST Client
@fhirurl =https://xxx.azurehealthcareapis.com
@clientid =xxx....
@clientsecret =xxx....
@tenantid =xxx....
```

## <a name="get-azure-ad-access-token"></a>Obtener Azure AD token de acceso

Después de incluir la información siguiente en el `test.http` archivo, presione `Send Request` . Verá una respuesta HTTP que contiene el token de acceso.

La línea que empieza `@name` por contiene una variable que captura la respuesta HTTP que contiene el token de acceso. La variable , `@token` , se usa para almacenar el token de acceso.

>[!Note] 
>el `grant_type` de se usa para obtener un token de `client_credentials` acceso.

```
### Get access token 
@name getAADToken 
POST https://login.microsoftonline.com/{{tenantid}}/oauth2/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&resource={{fhirurl}}
&client_id={{clientid}}
&client_secret={{clientsecret}}

### Extract access token from getAADToken request
@token = {{getAADToken.response.body.access_token}}
```

[![Obtener token de acceso ](media/rest-config.png) ](media/rest-config.png#lightbox)

## <a name="get-fhir-patient-data"></a>`GET` Datos de pacientes de FHIR

Ahora puede obtener una lista de pacientes o un paciente específico con la `GET` solicitud. La línea con `Authorization` es la información de encabezado de la `GET` solicitud. También puede enviar o `PUT` solicitar la creación o actualización de recursos de `POST` FHIR.

```
### GET Patient 
GET {{fhirurl}}/Patient/<patientid>
Authorization: Bearer {{token}}
```

[![GET Patient ](media/rest-patient.png) ](media/rest-patient.png#lightbox)

## <a name="run-powershell-or-cli"></a>Ejecución de PowerShell o cli

Puede ejecutar scripts de PowerShell o cli en Visual Studio Code. Presione `CTRL` y la tecla y seleccione `~` PowerShell o Bash. Puede encontrar más detalles en [Terminal integrado.](https://code.visualstudio.com/docs/editor/integrated-terminal)

### <a name="powershell-in-visual-studio-code"></a>PowerShell en Visual Studio Code
[![ejecución de PowerShell ](media/rest-powershell.png) ](media/rest-powershell.png#lightbox)

### <a name="cli-in-visual-studio-code"></a>CLI en Visual Studio Code
[![ejecución de la CLI ](media/rest-cli.png) ](media/rest-cli.png#lightbox)

## <a name="troubleshooting"></a>Solución de problemas

Si no puede obtener los metadatos, que no requieren un token de acceso basado en la especificación HL7, compruebe que el servidor FHIR se está ejecutando correctamente.

Si no puede obtener un token de acceso, asegúrese de que la aplicación cliente está registrada correctamente y de que usa los valores correctos del paso de registro de la aplicación.

Si no puede obtener datos del servidor de FHIR, asegúrese de que a la aplicación cliente (o a la entidad de servicio) se le hayan concedido permisos de acceso, como "Colaborador de datos de FHIR", al servidor de FHIR.
