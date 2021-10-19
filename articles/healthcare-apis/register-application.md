---
title: Registro de una aplicación cliente en Azure Active Directory para las API de Azure Healthcare
description: Cómo registrar una aplicación cliente en el Azure AD y cómo agregar un secreto y permisos de API a las API de Azure Healthcare
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: b1ac54f71c9c49af5bb8656bdbe05d639b8bca25
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278055"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Registro de una aplicación cliente en Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a registrar una aplicación cliente en Azure Active Directory (Azure AD) para acceder a las API de atención sanitaria. Puede encontrar más información en [Registro de una aplicación con el Plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="register-a-new-application"></a>Registro de una nueva aplicación

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.
2. Seleccione **App registrations** (Registros de aplicaciones).
[![Captura de pantalla de la nueva ventana de registro de aplicaciones. ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. Seleccione **Nuevo registro**.
4. En Tipos de cuenta admitidos, seleccione **Solo cuentas en este directorio de la organización.** Deje las demás opciones tal y como están.
[![Captura de pantalla de las nuevas opciones de la cuenta de registro. ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. Seleccione **Registrar**.

## <a name="application-id-client-id"></a>Id. de aplicación (id. de cliente)

Después de registrar una nueva aplicación, puede encontrar el identificador de la aplicación (cliente) y el identificador de directorio (inquilino) en la opción de menú información general. Anote los valores para usarlos más adelante.

[![Captura de pantalla del panel de información general del identificador de cliente. ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![Captura de pantalla del identificador de cliente ](media/register-application-four.png) ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>Configuración de autenticación: confidencial frente a pública

Haga clic en **Autenticación** para revisar la configuración. El valor predeterminado de **Permitir flujos de cliente públicos** es "No".

Si mantiene este valor predeterminado, el registro de la aplicación es una aplicación **cliente confidencial** y se requiere un certificado o secreto.

[![Captura de pantalla de la aplicación cliente confidencial. ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

Si cambia el valor predeterminado a "Sí",  el registro de la aplicación es una aplicación cliente pública y no se requiere un certificado o secreto. El valor "Sí" es útil cuando desea usar la aplicación cliente en la aplicación móvil o en una aplicación de JavaScript en la que no desea almacenar secretos.

Para las herramientas que requieren una dirección URL de redireccionamiento, **seleccione Agregar una plataforma** para configurar la plataforma.

[![Captura de pantalla de la adición de una plataforma. ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

En Postman, seleccione **Aplicaciones móviles y de escritorio.** Escriba " https://www.getpostman.com/oauth2/callback " en la sección URI de **redireccionamiento** personalizados. Seleccione el **botón** Configurar para guardar la configuración.

[![Captura de pantalla de la configuración de otros servicios. ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>Certificados & secretos

Seleccione **Certificates & Secrets (Certificados & secretos de** cliente) y **seleccione New Client Secret (Nuevo secreto de cliente).** Seleccione **Recomendado 6 meses en** el campo **Expira.** Este nuevo secreto será válido durante seis meses. También puede elegir valores diferentes, como:
 
* 03 meses
* 12 meses
* 24 meses
* Fecha de inicio y fecha de finalización personalizadas.

>[!NOTE]
>Es importante que guarde el valor del secreto, no el identificador del secreto.

[![Captura de pantalla de certificados y secretos. ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

Opcionalmente, puede cargar un certificado (clave pública) y usar el identificador de certificado, un valor GUID asociado al certificado. Con fines de prueba, puede crear un certificado autofirmado mediante herramientas como la línea de comandos de PowerShell, y, a continuación, exportar el certificado desde el `New-SelfSignedCertificate` almacén de certificados.

## <a name="api-permissions"></a>Permisos de API

Los permisos para las API de atención sanitaria se administran a través de RBAC. Para más información, visite [Configuración de RBAC de Azure para el servicio FHIR.](./fhir/configure-azure-rbac-for-fhir.md)

>[!NOTE]
>Use grant_type de client_credentials al intentar retener un token de acceso para el servicio FHIR mediante herramientas como Postman o Rest Client. Para más información, visite [Access using Postman (Acceso mediante Postman)](use-postman.md) y Accessing the Healthcare [APIs using the REST Client Extension in Visual Studio Code](using-rest-client.md)(Acceso a las API de atención sanitaria mediante la extensión de cliente REST en Visual Studio Code ).

El registro de la aplicación ya está completo.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a registrar una aplicación cliente en el Azure AD. Además, ha aprendido a agregar un secreto y permisos de API a las API de Azure Healthcare. Para más información sobre las API de Azure Healthcare, consulte

>[!div class="nextstepaction"]
>[Introducción a las API de Azure Healthcare](healthcare-apis-overview.md)
