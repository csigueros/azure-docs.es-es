---
title: Acceso al servicio FHIR de Azure Healthcare APIs mediante Postman
description: En este artículo se describe cómo acceder al servicio FHIR de Azure Healthcare APIs con Postman.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: 775c8a4366a381d90f8e24772097249b8cba844a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187802"
---
# <a name="access-using-postman"></a>Acceso mediante Postman

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, se le recorrerán los pasos necesarios para acceder al servicio FHIR de healthcare APIs (conocido como servicio FHIR) con [Postman.](https://www.getpostman.com/)

## <a name="prerequisites"></a>Requisitos previos

* El servicio FHIR implementado en Azure. Para obtener información sobre cómo implementar el servicio FHIR, consulte [Implementación de un servicio FHIR.](fhir/fhir-portal-quickstart.md)
* Una aplicación cliente registrada para acceder al servicio FHIR. Para obtener información sobre cómo registrar una aplicación cliente, vea Registrar una aplicación [cliente de servicio en Azure Active Directory](register-application.md). 
* Permisos concedidos a la aplicación cliente y a su cuenta de usuario, por ejemplo, "Colaborador de datos de FHIR", para acceder al servicio FHIR. 
* Postman instalado localmente. Para obtener más información sobre Postman, [vea Introducción con Postman](https://www.getpostman.com/).

## <a name="using-postman-create-workspace-collection-and-environment"></a>Uso de Postman: creación de un área de trabajo, una colección y un entorno

Si no está nuevo en Postman, siga estos pasos. Omita este paso si prefiere no hacerlo
 
Postman presenta el concepto de área de trabajo para que usted y su equipo puedan compartir API, colecciones, entornos y otros componentes. Puede usar el valor predeterminado "Mi área de trabajo" o "Área de trabajo de equipo" o crear una nueva área de trabajo para usted o su equipo.
 
[![Captura de pantalla de la creación de un área de trabajo en Postman. ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

A continuación, cree una nueva colección donde pueda agrupar todas las solicitudes de API REST relacionadas. En el área de trabajo, **seleccione Crear colecciones.** Puede mantener el nombre predeterminado Nueva **colección o** cambiarle el nombre. El cambio se guarda automáticamente.

[![Captura de pantalla de la creación de una nueva colección. ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

También puede importar y exportar colecciones de Postman. Para obtener más información, [vea la documentación de Postman](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/).

[![Captura de pantalla de la importación de datos. ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>Creación o actualización de variables de entorno

Aunque puede usar la dirección URL completa en la solicitud, se recomienda almacenar la dirección URL y otros datos en variables y usarlas.

Para acceder al servicio FHIR, es necesario crear o actualizar las siguientes variables.

* **tenantid:** inquilino de Azure en el que se implementa el servicio FHIR. Se encuentra en la opción de **menú Información general del registro** de aplicaciones.
* **subid:** suscripción de Azure en la que se implementa el servicio FHIR. Se encuentra en la opción de menú de información general del servicio **FHIR.**
* **clientid:** identificador de registro de cliente de aplicación.
* **clientsecret:** secreto de registro de cliente de aplicación.
* **fhirurl:** dirección URL completa del servicio FHIR. Por ejemplo, `https://xxx.azurehealthcareapis.com`. Se encuentra en la opción de menú de información general del servicio **FHIR.**
* **bearerToken:** la variable para almacenar el token de Azure Active Directory (Azure AD) en el script. Deje este parámetro en blanco.

> [!NOTE]
> Asegúrese de que ha configurado la dirección URL de redireccionamiento, `https://www.getpostman.com/oauth2/callback` , en el registro de la aplicación cliente.

[![Captura de pantalla de la variable de entornos. ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>Conectar al servidor de FHIR

Abra Postman, seleccione el área **de trabajo,** **la** colección y **el** entorno que desea usar. Seleccione el `+` icono para crear una nueva solicitud. 

[![Captura de pantalla de la creación de una nueva solicitud. ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Instrucción get capability

Escriba `{{fhirurl}}/metadata` en la solicitud y presione `GET` `Send` . Debería ver la instrucción de funcionalidad del servicio FHIR.

[![Captura de pantalla de los parámetros de la instrucción de funcionalidad. ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![Captura de pantalla de la solicitud de guardado. ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>Obtener Azure AD token de acceso

El servicio FHIR está protegido por Azure AD. No se puede deshabilitar la autenticación predeterminada. Para acceder al servicio FHIR, primero debe obtener un token Azure AD de acceso. Para obtener más información, [vea Plataforma de identidad de Microsoft tokens de acceso](./../active-directory/develop/access-tokens.md).

Cree una nueva `POST` solicitud:

1. Escriba en el encabezado de solicitud: `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. Seleccione la **pestaña Cuerpo** y seleccione **x-www-form-urlencoded**. Escriba los siguientes valores en la sección clave y valor:
    - **grant_type**: `Client_Credentials`
    - **client_id**: `{{clientid}}`
    - **client_secret**: `{{clientsecret}}`
    - **resource**: `{{fhirurl}}`
    
3. Seleccione la **pestaña Prueba** y escriba en la sección de texto: `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. Haga clic en **Save** (Guardar) para guardar la configuración.
5. Presione **Enviar**. Debería ver una respuesta con el token Azure AD de acceso, que se guarda en la variable `bearerToken` automáticamente. A continuación, puede usarlo en todas las solicitudes de LA API del servicio FHIR.

  [![Captura de pantalla del botón Enviar. ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

Puede examinar el token de acceso mediante herramientas en línea como [https://jwt.ms](https://jwt.ms) . Seleccione la **pestaña** Notificaciones para ver descripciones detalladas de cada notificación en el token.

[![Captura de pantalla de notificaciones de token de acceso. ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>Obtener el recurso de FHIR

Después de obtener un token de Azure AD, puede acceder a los datos de FHIR. En una nueva `GET` solicitud, escriba `{{fhirurl}}/Patient` .

Seleccione **Token de portador como** tipo de autorización.  Escriba `{{bearerToken}}` en la sección Token **(Token).** Seleccione **Enviar**. Como respuesta, debería ver una lista de pacientes en el recurso de FHIR.

[![Captura de pantalla de la selección del token de portador. ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>Creación o actualización del recurso de FHIR

Después de obtener un token de Azure AD, puede crear o actualizar los datos de FHIR. Por ejemplo, puede crear un nuevo paciente o actualizar uno existente.
 
Cree una nueva solicitud, cambie el método a "Post" y escriba el valor en la sección de solicitud.

`{{fhirurl}}/Patient`

Seleccione **Token de portador como** tipo de autorización.  Escriba `{{bearerToken}}` en la sección Token **(Token).** Seleccione la **pestaña** Cuerpo. Seleccione la **opción sin** formato y **JSON como** formato de texto del cuerpo. Copie y pegue el texto en la sección del cuerpo. 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
Seleccione **Enviar**. Debería ver un nuevo paciente en la respuesta JSON.

[![Captura de pantalla del botón Enviar para crear un nuevo paciente. ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>Exportación de datos de FHIR

Después de obtener un token de Azure AD, puede exportar los datos de FHIR a una cuenta de almacenamiento de Azure.

Cree una nueva `GET` solicitud: `{{fhirurl}}/$export?_container=export`

Seleccione **Token de portador como** tipo de autorización.  Escriba `{{bearerToken}}` en la sección Token **(Token).** Seleccione **Encabezados** para agregar dos nuevos encabezados:

- **Acepte**: `application/fhir+json`
- **Prefiere**:  `respond-async`

Presione **Enviar**. Debería observar una `202 Accepted` respuesta. Seleccione la **pestaña Encabezados** de la respuesta y anote el valor en **Content-Location**. Puede usar el valor para consultar el estado del trabajo de exportación.

[![Captura de pantalla de la publicación para crear una nueva respuesta aceptada del paciente 202. ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a acceder al servicio FHIR en las API de Azure Healthcare con Postman. Para obtener información sobre el servicio FHIR en las API de Azure Healthcare, consulte

>[!div class="nextstepaction"]
>[¿Qué es el servicio FHIR?](overview.md)
