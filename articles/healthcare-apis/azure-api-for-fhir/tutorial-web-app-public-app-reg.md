---
title: 'Tutorial de aplicación web: configuración de aplicaciones cliente - Azure API for FHIR'
description: Este tutorial le guía a través de los pasos para registrar una aplicación pública con el fin de preparar la implementación de una aplicación web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 438ed15df54aefe299cbda0c45fe168f5857c93c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787079"
---
# <a name="client-application-registration-for-azure-api-for-fhir"></a>Registro de aplicaciones cliente para Azure API for FHIR
En el tutorial anterior, ha implementado y configurado Azure API for FHIR. Ahora que la configuración de Azure API for FHIR ya está realizada, vamos a registrar una aplicación cliente pública. Para conocer más detalles o solucionar problemas puede leer la guía paso a paso para [registrar una aplicación cliente pública](register-public-azure-ad-client-app.md), pero en este tutorial hemos indicado los pasos principales a continuación.

1. Vaya a Azure Active Directory.
1. Seleccione **Registro de aplicación** --> **Nuevo registro**.
1. Asigne un nombre a la aplicación.
1. Seleccione **Cliente público o nativo (móvil y escritorio)** y establezca la URI de redirección en `https://www.getpostman.com/oauth2/callback`.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Captura de pantalla del panel Registrar una aplicación y un nombre de aplicación y una dirección URL de redireccionamiento de ejemplo.":::

## <a name="client-application-settings"></a>Configuración de la aplicación cliente

Una vez registrada la aplicación cliente, copie el identificador de aplicación (cliente) y el identificador de inquilino de la página Información general. Necesitará estos dos valores más adelante al acceder al cliente.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Captura de pantalla del panel Configuración de la aplicación cliente, con los identificadores de la aplicación y el directorio resaltados.":::

### <a name="connect-with-web-app"></a>Conexión con la aplicación web

Si ha [escrito la aplicación web](tutorial-web-app-write-web-app.md) para conectarse a Azure API for FHIR, también debe establecer las opciones de autenticación correctas. 

1. En el menú izquierdo, en **Administrar**, seleccione **Autenticación**. 

1. Para agregar una nueva configuración de plataforma, seleccione **Web**.

1. Configure el identificador URI de redireccionamiento como preparativo para cuando cree la aplicación web en la cuarta parte de este tutorial. Para ello, agregue `https://\<WEB-APP-NAME>.azurewebsites.net` a la lista de identificadores URI de redireccionamiento. Si elige un nombre diferente durante el paso en el que [escribe la aplicación web](tutorial-web-app-write-web-app.md), tendrá que volver y actualizar esto.

1. Seleccione las casillas **Token de acceso** y **Token de identificador**.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Captura de pantalla de la hoja Configuración de la autenticación de la aplicación, con los pasos para agregar una plataforma resaltados.":::

## <a name="add-api-permissions"></a>Adición de permisos de API

Ahora que ha configurado la autenticación correcta, establezca los permisos de la API:

1. Seleccione **Permisos de API** y haga clic en **Agregar un permiso**.
1. En **API usadas en mi organización**, busque las API de salud de Azure.
1. Seleccione **user_impersonation** y haga clic en **Agregar permisos**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Captura de pantalla de la hoja Agregar permisos de API, con los pasos para agregar permisos de API resaltados.":::

## <a name="next-steps"></a>Pasos siguientes
Ahora tiene una aplicación cliente pública. El próximo tutorial le guiará a través de las pruebas y la obtención de acceso a esta aplicación mediante Postman.

>[!div class="nextstepaction"]
>[Prueba de la aplicación cliente en Postman](tutorial-web-app-test-postman.md)
