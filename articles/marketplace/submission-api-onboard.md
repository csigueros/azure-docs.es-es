---
title: Introducción a la incorporación de la API de envío del Centro de partners
description: Una introducción a la incorporación de la API de envío del Centro de partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 5c6431fef8c91b41f19d27d19b59efca14986b7f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061948"
---
# <a name="partner-center-submission-api-onboarding"></a>Incorporación de la API de envío del Centro de partners

Use la API de envío del Centro de partners para consultar, crear envíos y publicar ofertas de Marketplace comercial mediante programación. Esta API es útil si su cuenta administra muchas ofertas y desea automatizar y optimizar el proceso de envío de estas ofertas.

## <a name="api-prerequisites"></a>Requisitos previos de la API

La API de envío del Centro de partners requiere algunos recursos de programación:

- Una aplicación de Azure Active Directory.
- Un token de acceso de Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Paso 1: Cumplimentación los requisitos previos para el uso de la API de envío del Centro de partners

Antes de empezar a escribir código para llamar a la API de envío del Centro de partners, asegúrese de que ha completado los requisitos previos que se indican a continuación.

- Usted (o su organización) tiene que tener un directorio de Azure AD y el permiso de [Administrador global](../active-directory/roles/permissions-reference.md) para el directorio. Si usa Microsoft 365 u otros servicios empresariales de Microsoft, ya tiene el directorio de Azure AD. De lo contrario, puede [crear uno nuevo](manage-tenants.md#create-a-new-tenant) en el Centro de partners sin cargo adicional.
- Tiene que [asociar una aplicación de Azure AD](manage-aad-apps.md) a su cuenta del Centro de partners y obtener el id. de inquilino, el id. de cliente y la clave. Necesitará estos valores para obtener el token de acceso de Azure AD, que usará en las llamadas a la API de envío de Microsoft Store.

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>Asociación de una aplicación de Azure AD con la cuenta del Centro de partners

Para usar la API de envío del Centro de partners, tiene que asociar una aplicación de Azure AD a su cuenta del Centro de partners, recuperar el id. de inquilino y el de cliente para la aplicación y generar una clave. La aplicación de Azure AD representa la aplicación o el servicio desde donde desea llamar a la API de envío del Centro de partners. Necesita el id. de inquilino, el de cliente y la clave para obtener un token de acceso de Azure AD para pasar a la API.

> [!NOTE]
> Solo tiene que realizar esta operación una vez. Una vez que tenga el identificador de inquilino, el identificador de cliente y la clave, puede volver a usarlos cada vez que tenga que crear un nuevo token de acceso de Azure AD.

1. En el Centro de partners, [asocie la cuenta del Centro de partners de la organización](manage-tenants.md) con el directorio de Azure AD de la organización.

1. En la página **Usuarios** en la sección **Configuración de cuenta** del Centro de partners, [agregue la aplicación de Azure AD](manage-aad-apps.md) que representa la aplicación o el servicio que usará para obtener acceso a los envíos de la cuenta del Centro de partners. Asegúrese de asignar a esta aplicación el rol **Administrador**. Si la aplicación aún no existe en el directorio de Azure AD, [cree una nueva aplicación de Azure AD](manage-aad-apps.md#add-new-azure-ad-applications) en el Centro de partners.

1. Vuelva a la página **Usuarios**, seleccione el nombre de la aplicación de Azure AD para ir a la configuración de la aplicación y, a continuación, copie los valores de **Id. de inquilino** e **Id. de cliente**.

1. Seleccione **Agregar nueva clave**. En la pantalla siguiente, copie el valor de **Clave**. Después de salir de esta página no podrá tener acceso de nuevo a esta información. Para más información, consulte [Administrar claves para una aplicación de Azure AD](manage-aad-apps.md#manage-keys-for-an-azure-ad-application).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Paso 2: Obtención de un token de acceso de Azure AD

Antes de llamar a cualquiera de los métodos de la API de envío del Centro de partners, primero tiene que obtener un token de acceso de Azure AD para pasar al encabezado de **autorización** de cada método de la API. Un token de acceso tarda 60 minutos en expirar desde su emisión. Después, puede actualizarlo para poder usarlo en futuras llamadas a la API.

Para obtener el token de acceso, siga las instrucciones de [Llamadas entre servicios mediante las credenciales del cliente](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) para enviar una `HTTP POST` al punto de conexión `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Esta es una solicitud de ejemplo:

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Para el valor tenant_id en el URI de POST y los parámetros client_id y client_secret, especifique el id. de inquilino, el de cliente y la clave de la aplicación que recuperó del Centro de partners en la sección anterior. Para el parámetro resource, tiene que especificar https://api.partner.microsoft.com.

### <a name="step-3-use-the-partner-center-submission-api"></a>Paso 3: Uso de la API de envío del Centro de partners

Una vez que tenga un token de acceso de Azure AD, llame a los métodos en la API de envío del Centro de partners. Para crear o actualizar envíos, normalmente se llama a varios métodos en la API de envío del Centro de partners en un orden específico. Para obtener información acerca de cada escenario y la sintaxis de cada método, consulte el swagger de la [API de ingesta](https://apidocs.microsoft.com/services/partneringestion/).

## <a name="next-steps"></a>Pasos siguientes

- Empezar a usar la API de envío del Centro de partners como se ha descrito anteriormente.
