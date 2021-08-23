---
title: Ejemplos de API para modificar los flujos de usuario de Azure AD B2C | Microsoft Docs
description: Ejemplos de código para modificar los flujos de usuario con conectores de API
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d27bac61a7f39b50d692e579edb29da945234a27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114479678"
---
# <a name="api-connector-rest-api-samples"></a>Ejemplos de API REST de conectores de API

En las tablas siguientes se proporcionan vínculos a ejemplos de código para utilizar las API web en los flujos de usuario con [conectores de API](api-connectors-overview.md). Estos ejemplos están diseñados principalmente para utilizarlos con flujos de usuario integrados.

## <a name="azure-function-quickstarts"></a>Inicios rápidos de Azure Functions
| Muestra                                                                                                                          | Descripción                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Este ejemplo de Azure Functions con .NET Core muestra cómo limitar los registros a dominios de correo electrónico específicos y validar la información proporcionada por el usuario. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Este ejemplo de Azure Functions con Node.js muestra cómo limitar los registros a dominios de correo electrónico específicos y validar la información proporcionada por el usuario.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Este ejemplo de Azure Functions con Python muestra cómo limitar los registros a dominios de correo electrónico específicos y validar la información proporcionada por el usuario.    |


## <a name="automated-fraud-protection-services--captcha"></a>Servicios automatizados de protección contra el fraude y CAPTCHA
| Muestra                                                                                                            | Descripción                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Protección de Arkose Labs contra el fraude y el uso indebido](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | En este ejemplo se muestra cómo proteger los registros de usuario mediante el servicio de protección de Arkose Labs contra el fraude y el abuso. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | En este ejemplo se muestra cómo proteger los registros de usuario mediante un desafío reCAPTCHA para evitar el abuso automatizado. |


## <a name="identity-verification"></a>Verificación de identidad

| Muestra                                                                                                            | Descripción                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | En este ejemplo se muestra cómo comprobar una identidad de usuario como parte de los flujos de registro con el servicio de IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | En este ejemplo se muestra cómo comprobar una identidad de usuario como parte de los flujos de registro con el servicio de Experian. |


## <a name="other"></a>Otros

| Muestra                                                                                                            | Descripción                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Código de invitación](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Este ejemplo muestra cómo limitar los registros a públicos específicos mediante códigos de invitación.|
| [Ejemplos de la comunidad del conector de API](https://github.com/azure-ad-b2c/api-connector-samples) | Este repositorio incluye ejemplos de escenarios mantenidos por la comunidad habilitados por los conectores de API.|
