---
title: 'Registro de aplicaciones de Azure Active Directory para el servicio DICOM: Azure Healthcare APIs'
description: En este artículo se describe cómo registrar una aplicación de Azure Active Directory para el servicio DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780515"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>Registro de aplicaciones de Azure Active Directory para el servicio DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

Tiene varias opciones entre las que elegir al configurar el servicio DICOM o el servidor FHIR para Azure (OSS). Para código abierto, tendrá que crear su propio registro de aplicación de recursos. En el caso de Azure API for FHIR, esta aplicación de recursos se crea automáticamente.

## <a name="application-registrations"></a>Registros de aplicación

Para que una aplicación interactúe con Azure AD, debe estar registrada. En el contexto del servicio DICOM, hay tres tipos de registros de aplicaciones cliente para analizar.

## <a name="client-applications"></a>Aplicaciones cliente

Las aplicaciones cliente son registros de los clientes que van a solicitar tokens. En OAuth 2.0 solemos distinguir entre al menos tres tipos diferentes de aplicaciones:

### <a name="confidential-clients"></a>Clientes confidenciales

Los clientes confidenciales también se conocen como "aplicaciones web" en Azure AD. Los clientes confidenciales son aplicaciones que usan [flujo de código de autorización](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) para obtener un token en nombre de un usuario con la sesión iniciada mediante la presentación de credenciales válidas. Se denominan clientes confidenciales porque pueden contener un secreto, el cual presentarán a Azure AD al intercambiar el código de autenticación por un token. Dado que los clientes confidenciales pueden autenticarse a sí mismos mediante el secreto de cliente, son de mayor confianza que los clientes públicos, pueden tener tokens de mayor duración y se les puede conceder un token de actualización. Para obtener más información, consulte [Registro de una aplicación cliente confidencial en Azure Active Directory](dicom-register-confidential-client-application.md). Es importante registrar la dirección URL de respuesta en la que el cliente va a recibir el código de autorización.

### <a name="public-clients"></a>Clientes públicos

Se trata de clientes que no pueden contener un secreto. Normalmente se trata de una aplicación de dispositivo móvil o una aplicación de página única de JavaScript, donde un usuario podría detectar un secreto en el cliente. Los clientes públicos también usan el flujo de código de autorización, pero no se les permite presentar un secreto al obtener un token y pueden tener tokens de menor duración y ningún token de actualización. Para obtener más información, consulte [Registro de una aplicación cliente pública en Azure Active Directory](dicom-register-public-application.md).

### <a name="service-clients"></a>Clientes de servicio

Estos clientes obtienen tokens en nombre de ellos mismos (no en nombre de un usuario) mediante el [flujo de credenciales de cliente](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Suelen representar aplicaciones que acceden al servidor DICOM de forma no interactiva. Un ejemplo podría ser un proceso de ingesta. Cuando se usa un cliente de servicio, no es necesario iniciar el proceso de obtención de un token con una llamada al punto de conexión /authorize. Un cliente de servicio puede ir directamente al punto de conexión /token y presentar el Id. y el secreto de cliente para obtener un token. Para obtener más información, consulte [Registro de una aplicación cliente de servicio en Azure Active Directory](dicom-register-service-client-application.md).

## <a name="next-steps"></a>Pasos siguientes

Este artículo de información general le ha guiado por el proceso de registro de aplicaciones para que las aplicaciones cliente y de recursos funcionen con el servicio DICOM. Una vez registradas las aplicaciones, puede implementar el servicio DICOM.

>[!div class="nextstepaction"]
>[Registro de una aplicación cliente confidencial en Azure Active Directory](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[Registro de una aplicación cliente pública en Azure Active Directory](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[Registro de una aplicación cliente de servicio en Azure Active Directory](dicom-register-service-client-application.md)