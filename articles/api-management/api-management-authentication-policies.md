---
title: Directivas de autenticación de Azure API Management | Microsoft Docs
description: Aprenda sobre las directivas de autenticación disponibles para su uso en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: danlep
ms.openlocfilehash: d43310918f1aaeb7905d22f22726709f8d0ed1f4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629775"
---
# <a name="api-management-authentication-policies"></a>Directivas de autenticación de Azure API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Directivas de autenticación

-   [Autenticar con opción básica](api-management-authentication-policies.md#Basic) : autenticar con un servicio de back-end mediante la autenticación básica.

-   [Autenticar con certificado de cliente](api-management-authentication-policies.md#ClientCertificate) : autenticar con un servicio de back-end mediante certificados de cliente.

-   [Autenticar con identidad administrada](api-management-authentication-policies.md#ManagedIdentity): autenticar con una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) para el servicio API Management.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Autenticación con Basic
 Use la directiva `authentication-basic` para realizar la autenticación con un servicio de back-end mediante autenticación Básica. Esta directiva establece eficazmente el encabezado de autorización HTTP en el valor correspondiente a las credenciales proporcionadas en la directiva.

### <a name="policy-statement"></a>Instrucción de la directiva

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Ejemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|Nombre|Descripción|Obligatorio|
|----------|-----------------|--------------|
|authentication-basic|Elemento raíz.|Sí|

### <a name="attributes"></a>Atributos

|Nombre|Descripción|Obligatorio|Valor predeterminado|
|----------|-----------------|--------------|-------------|
|username|Especifica el nombre de usuario de la credencial básica.|Sí|N/D|
|password|Especifica la contraseña de usuario de la credencial básica.|Sí|N/D|

### <a name="usage"></a>Uso
 Esta directiva puede usarse en las siguientes [secciones](./api-management-howto-policies.md#sections) y [ámbitos](./api-management-howto-policies.md#scopes) de directiva.

-   **Secciones de la directiva:** inbound (entrada)

-   **Ámbitos de la directiva:** todos los ámbitos

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Autenticación Básica
 Use la directiva `authentication-certificate` para realizar la autenticación con un servicio de back-end mediante un certificado de cliente. El certificado se debe [instalar en API Management](./api-management-howto-mutual-certificates.md) primero y se identifica mediante su huella digital o id. de certificado (nombre del recurso). 

> [!CAUTION]
> Si el certificado hace referencia a un certificado almacenado en Azure Key Vault, se identifica mediante el id. de certificado. Cuando se gira un certificado de Key Vault, su huella digital en API Management cambiará, por lo que la directiva no resolverá el nuevo certificado si se identifica mediante la huella digital.

### <a name="policy-statement"></a>Instrucción de la directiva

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Ejemplos

En este ejemplo, el certificado de cliente se identifica mediante el id. de certificado:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

En este ejemplo, el certificado de cliente se identifica mediante su huella digital:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
En este ejemplo, el certificado de cliente se establece en la directiva en lugar de recuperarlo del almacén de certificados integrado:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|thumbprint|La huella digital del certificado de cliente.|`thumbprint` o `certificate-id` debe estar presente.|N/D|
|certificate-id|Nombre del recurso de certificado.|`thumbprint` o `certificate-id` debe estar presente.|N/D|
|body|Certificado de cliente como matriz de bytes.|No|N/D|
|password|Contraseña del certificado de cliente.|Se usa si el certificado especificado en `body` está protegido por contraseña.|N/D|
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](./api-management-howto-policies.md#sections) y [ámbitos](./api-management-howto-policies.md#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound (entrada)  
  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Autenticación con una identidad administrada  
 Use la directiva `authentication-managed-identity` para realizar la autenticación con un servicio de back-end mediante la identidad administrada. En esencia, esta directiva usa la identidad administrada para obtener un token de acceso de Azure Active Directory para acceder al recurso especificado. Después de obtener el token correctamente, la Directiva establecerá el valor del token en el `Authorization` encabezado mediante el esquema `Bearer`.

Para solicitar el token, se puede usar tanto la identidad asignada por el sistema como cualquiera de las distintas identidades asignadas por el usuario. Si no se proporciona `client-id`, se presupone la identidad asignada por el sistema. Si se proporciona la variable `client-id`, se solicita el token para esa identidad asignada por el usuario de Azure Active Directory
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Ejemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Uso de una identidad administrada para autenticación con un servicio back-end
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Uso de una identidad administrada y establecimiento manual de un encabezado

```xml
<authentication-managed-identity resource="Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Uso de una identidad administrada en una directiva send-request
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|resource|String. Identificador de aplicación de la API web de destino (recurso seguro) en Azure Active Directory.|Sí|N/D|
|client-id|String. El id. de aplicación de la identidad asignada por el usuario en Azure Active Directory.|No|identidad asignada por el sistema|
|output-token-variable-name|String. Nombre de la variable de contexto que recibirá el valor del token como un tipo de objeto `string`. |No|N/D|  
|ignore-error|booleano. Si se establece en `true`, la canalización de directivas seguirá ejecutándose incluso si no se obtiene un token de acceso.|No|false|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](./api-management-howto-policies.md#sections) y [ámbitos](./api-management-howto-policies.md#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound (entrada)  
  
-   **Ámbitos de la directiva:** todos los ámbitos

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [Directivas de Azure API Management](api-management-howto-policies.md)
+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](./api-management-policies.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](./policy-reference.md)
