---
title: 'Registro de una aplicación de recursos en Azure AD: Azure API for FHIR'
description: Registre una aplicación (o una API) de recursos en Azure Active Directory para que las aplicaciones cliente puedan solicitar acceso al recurso al autenticarse.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: e8453a2b82051dc7e63d1193947747d839478427
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780744"
---
# <a name="register-a-resource-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registro de una aplicación de recursos en Azure Active Directory para Azure API for FHIR

En este artículo aprenderá registrar una aplicación de recursos (o API) en Azure Active Directory. Una aplicación de recursos es una representación de Azure Active Directory de la misma API de FHIR Server y las aplicaciones cliente pueden solicitar acceso al recurso al autenticar. La aplicación de recursos también se conoce como la *audiencia* en términos de OAuth.

## <a name="azure-api-for-fhir"></a>API de Azure para FHIR

Si usa Azure API for FHIR, se crea automáticamente una aplicación de recursos al implementar el servicio. Si usa Azure API for FHIR en el mismo inquilino de Azure Active Directory mientras implementa la aplicación, puede omitir esta guía de procedimientos e implementar Azure API for FHIR para comenzar.

Si usa un inquilino de Azure Active Directory diferente (es decir, no asociado a la suscripción), puede importar la aplicación de recursos de Azure API for FHIR en el inquilino con PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

O bien, puede usar la CLI de Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Servidor de FHIR para Azure

Si usa el servidor de FHIR para Azure, de código abierto, siga los pasos que se indican en el [repositorio de GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) para registrar una aplicación de recursos. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido cómo registrar una aplicación de recursos en Azure Active Directory. A continuación, registre la aplicación cliente confidencial.
 
>[!div class="nextstepaction"]
>[Registro de aplicación cliente confidencial](register-confidential-azure-ad-client-app.md)