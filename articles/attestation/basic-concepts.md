---
title: Conceptos básicos de Azure Attestation
description: Conceptos básicos de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 5ac431f489f279ea09407e1a34c234911d71f12f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259730"
---
# <a name="basic-concepts"></a>Conceptos básicos

A continuación se muestran algunos conceptos básicos relacionados con Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) (JWT) es un método de [RFC7519](https://tools.ietf.org/html/rfc7519) estándar abierto para transmitir información de forma segura entre entidades como un objeto de notación de objetos JavaScript (JSON). Esta información se puede comprobar y es de confianza porque está firmada digitalmente. Los JWT se pueden firmar mediante un secreto o un par de claves pública y privada.

## <a name="json-web-key-jwk"></a>Clave de web JSON (JWK)

La [clave web de JSON](https://tools.ietf.org/html/rfc7517) (JWK) es una estructura de datos JSON que representa una clave criptográfica. Esta especificación también define una estructura de datos Set JSON de JWK que representa un conjunto de JWK.

## <a name="attestation-provider"></a>Proveedor de atestación

El proveedor de atestación pertenece al proveedor de recursos de Azure denominado Microsoft.Attestation. El proveedor de recursos es un punto de conexión de servicio que proporciona el contrato REST de Azure Attestation y se implementa mediante [Azure Resource Manager](../azure-resource-manager/management/overview.md). Cada proveedor de atestación respeta una directiva específica y reconocible. Los proveedores de atestación se crean con una directiva predeterminada para cada tipo de atestación (tenga en cuenta que el enclave de VBS no tiene ninguna directiva predeterminada). Consulte los [ejemplos de directivas de atestación](policy-examples.md) para más información sobre la directiva predeterminada de SGX.

### <a name="regional-shared-provider"></a>Proveedor compartido regional

Azure Attestation proporciona un proveedor compartido regional en cada región disponible. Los clientes pueden usar el proveedor predeterminado para la atestación, o bien crear proveedores propios mediante directivas personalizadas. Los proveedores compartidos están disponibles para cualquier usuario de Azure AD y la directiva asociada a él no se puede modificar.

| Region | URI de atestación | 
|--|--|
| Este de EE. UU. | `https://sharedeus.eus.attest.azure.net` | 
| Oeste de EE. UU. | `https://sharedwus.wus.attest.azure.net` | 
| Sur de Reino Unido | `https://shareduks.uks.attest.azure.net` | 
| Oeste de Reino Unido| `https://sharedukw.ukw.attest.azure.net  ` | 
| Este de Canadá | `https://sharedcae.cae.attest.azure.net` | 
| Centro de Canadá | `https://sharedcac.cac.attest.azure.net` | 
| Norte de Europa | `https://sharedneu.neu.attest.azure.net` | 
| Oeste de Europa| `https://sharedweu.weu.attest.azure.net` | 
| Este de EE. UU. 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| Centro de EE. UU. | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>Solicitud de atestación

La solicitud de atestación es un objeto JSON serializado que la aplicación cliente envía al proveedor de atestación. El objeto de solicitud para el enclave de SGX tiene dos propiedades: 
- "Quote": el valor de la propiedad "Quote" es una cadena que contiene una representación codificada en Base64URL de la oferta de atestación.
- "EnclaveHeldData": el valor de la propiedad "EnclaveHeldData" es una cadena que contiene una representación codificada en Base64URL de los datos contenidos en el enclave.

Azure Attestation validará el valor de "Quote" proporcionado y, luego, garantizará que el hash SHA256 de los datos contenidos en el enclave proporcionado se expresa en los primeros 32 bytes del campo reportData de la oferta. 

## <a name="attestation-policy"></a>Directiva de atestación

La directiva de atestación se usa para procesar la evidencia de atestación. Los clientes pueden configurarla. En el núcleo de Azure Attestation se encuentra un motor de directivas que procesa las notificaciones que constituyen la evidencia. Las directivas se usan para determinar si Azure Attestation emitirá o no un token de atestación en función de la evidencia y, por tanto, aprobará o no el atestador. En consecuencia, si no se superan todas las directivas, no se emitirá ningún token JWT.

Si la directiva predeterminada del proveedor de atestación no satisface las necesidades, los clientes podrán crear directivas personalizadas en cualquiera de las regiones que Azure Attestation admita. La administración de directivas es una característica clave que se proporciona a los clientes mediante Azure Attestation. Las directivas serán específicas del tipo de atestación y se pueden usar para identificar enclaves, agregar notificaciones al token de salida o modificar notificaciones en un token de salida. 

Para ver ejemplos de directivas, consulte [Ejemplos de una directiva de atestación](policy-examples.md).

## <a name="benefits-of-policy-signing"></a>Ventajas de la firma de directivas

En última instancia, una directiva de atestación es lo que determina si Azure Attestation emitirá el token de atestación. La directiva también determina las notificaciones que se van a generar en el token de atestación. Por lo tanto, es de la máxima importancia que la directiva que el servicio evalúe sea de hecho la que haya escrito el administrador y ninguna entidad externa la haya manipulado ni modificado. 

El modelo de confianza define el modelo de autorización del proveedor de atestación para definir y actualizar la directiva.  Se admiten dos modelos: uno basado en la autorización de Azure AD y otro basado en la posesión de claves criptográficas administradas por el cliente (conocidas como "modelo aislado").  El modelo aislado habilitará Azure Attestation para garantizar que la directiva que el cliente envíe no se manipule.

En el modelo aislado, el administrador crea un proveedor de atestación que especifica un conjunto de certificados X.509 de firma de confianza en un archivo. A continuación, el administrador puede agregar una directiva firmada al proveedor de atestación. Al procesar la solicitud de atestación, Azure Attestation validará la firma de la directiva mediante la clave pública representada por el parámetro "jwk" o "x5c" en el encabezado.  Azure Attestation también comprobará si la clave pública del encabezado de la solicitud está en la lista de certificados de firma de confianza asociados al proveedor de atestación. De esta manera, el usuario de confianza (Azure Attestation) puede confiar en una directiva firmada con los certificados X.509 que conozca. 

Consulte los [ejemplos de certificado de firma de directivas](policy-signer-examples.md).

## <a name="attestation-token"></a>Token de atestación

La respuesta de Azure Attestation será una cadena JSON cuyo valor contenga JWT. Azure Attestation empaquetará las notificaciones y generará un JWT firmado. La operación de firma se realiza mediante un certificado autofirmado con un nombre de sujeto que coincide con el elemento AttestUri del proveedor de atestación.

La API de obtención de metadatos de OpenID devuelve una respuesta de configuración de OpenID según lo especificado por el protocolo de detección de [OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). La API recupera los metadatos sobre los certificados de firma que Azure Attestation use.

Ejemplo de JWT generado para un enclave de SGX:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0",
  "x-ms-sgx-config-id": "000102030405060708090a0b0c0d8f99000102030405060708090a0b0c860e9a000102030405060708090a0b7d0d0e9b000102030405060708090a740c0d0e9c",
  "x-ms-sgx-config-svn": 3451,
  "x-ms-sgx-isv-extended-product-id": "8765432143211234abcdabcdef123456",
  "x-ms-sgx-isv-family-id": "1234567812344321abcd1234567890ab"
}.[Signature]
```

Algunas de las notificaciones usadas anteriormente se consideran en desuso, pero se admiten por completo.  Se recomienda que todas las herramientas y el código futuros usen los nombres de notificaciones que no estén en desuso. Para más información, consulte las [notificaciones emitidas por Azure Attestation](claim-sets.md).

Las notificaciones siguientes solo aparecerán en el token de atestación generado para las plataformas de servidor basadas en procesadores escalables de Intel® Xeon®. Las notificaciones no aparecerán si el enclave de SGX no está configurado con [Key Separation and Sharing Support](https://github.com/openenclave/openenclave/issues/3054) (Separación de claves y compatibilidad con uso compartido)

**x-ms-sgx-config-id**

**x-ms-sgx-config-svn**

**x-ms-sgx-isv-extended-product-id**

**x-ms-sgx-isv-family-id**

## <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo

Para proteger los datos de clientes, Azure Attestation conserva sus datos en Azure Storage. Azure Storage proporciona cifrado de los datos en reposo a medida que se escriben en los centros de datos, y los descifra para que los clientes puedan acceder a ellos. Este cifrado se produce mediante una clave de cifrado administrada de Microsoft. 

Además de proteger los datos en Azure Storage, Azure Attestation también aprovecha Azure Disk Encryption (ADE) para cifrar las máquinas virtuales del servicio. Si Azure Attestation se ejecuta en un enclave en entornos informáticos confidenciales de Azure, la extensión ADE no se admite actualmente. En estos escenarios, para evitar que los datos se almacenen en memoria, el archivo de paginación está deshabilitado. 

No se conservan datos de clientes en las unidades de disco duro locales de Azure Attestation.


## <a name="next-steps"></a>Pasos siguientes

- [Creación y firma de una directiva de atestación](author-sign-policy.md)
- [Configuración de Azure Attestation con PowerShell](quickstart-powershell.md)
