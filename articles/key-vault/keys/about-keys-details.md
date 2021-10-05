---
title: 'Tipos de clave, algoritmos y operaciones: Azure Key Vault'
description: Tipos de clave, algoritmos y operaciones admitidos (detalles).
services: key-vault
author: mbaldwin
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: mbaldwin
ms.openlocfilehash: 3f3db4b694dd825b15cf80be247bf43780e1d381
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563008"
---
# <a name="key-types-algorithms-and-operations"></a>Tipos de claves, algoritmos y operaciones

Key Vault admite dos tipos de recursos: almacenes y HSM administrados. Ambos son compatibles con varias claves de cifrado. Para ver un resumen de los tipos de clave y los tipos de protección compatibles con cada tipo de recurso, consulte [Acerca de las claves](about-keys.md).

En la siguiente tabla se muestra un resumen de los tipos de clave y los algoritmos compatibles.

|Tipos de clave/tamaños/curvas| Cifrar/Descifrar<br>(Encapsular/Desencapsular) | FIRMAR/VERIFICAR | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, EC-521|N/D|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128-bit, 256-bit <br/>(solo HSM administrado)| AES-KW<br>AES-GCM<br>AES-CBC| N/D| 
|||

##  <a name="ec-algorithms"></a>Algoritmos de EC
 Los identificadores de algoritmo siguientes son compatibles con las claves EC-HSM

### <a name="curve-types"></a>Tipos de curvas

-   **P-256**: curva NIST P-256, definida en [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K**: curva SEC SECP256K1, definida en [SEC 2: parámetros de dominio de curva elíptica recomendados](https://www.secg.org/sec2-v2.pdf).
-   **P-384**: curva NIST P-384, definida en [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521**: curva NIST P-521, definida en [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>SIGN/VERIFY

-   **ES256**: se crean claves y códigos hash de ECDSA para SHA-256 creados con la curva P-256. Este algoritmo se describe en [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K**: se crean claves y códigos hash de ECDSA para SHA-256 creados con la curva P-256K. Este algoritmo está pendiente de normalización.
-   **ES384**: se crean claves y códigos hash de ECDSA para SHA-384 creados con la curva P-384. Este algoritmo se describe en [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512**: se crean claves y códigos hash de ECDSA para SHA-512 creados con la curva P-521. Este algoritmo se describe en [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Los siguientes identificadores de algoritmo son compatibles con las claves RSA y RSA-HSM  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5**: cifrado de clave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP**: RSAES con relleno de cifrado asimétrico óptimo (OAEP) [RFC3447], con los parámetros predeterminados especificados en RFC 3447 en la sección A.2.1. Estos parámetros predeterminados utilizan una función hash de SHA-1 y una función de generación de máscara de MGF1 con SHA-1.  
-  **RSA-OAEP-256**: RSAES mediante un relleno óptimo de cifrado asimétrico con una función hash de SHA-256 y una función de generación de máscara de MGF1 con SHA-256

### <a name="signverify"></a>SIGN/VERIFY

-   **PS256** - RSASSA-PSS con SHA-256 y MGF1 con SHA-256, como se indica en [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS con SHA-384 y MGF1 con SHA-384, como se indica en [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS con SHA-512 y MGF1 con SHA-512, como se describe en [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256**: RSASSA-PKCS-v1_5 con SHA-256. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-256 y debe tener 32 bytes de longitud.  
-   **RS384**: RSASSA-PKCS-v1_5 con SHA-384. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-384 y debe tener 48 bytes de longitud.  
-   **RS512**: RSASSA-PKCS-v1_5 con SHA-512. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-512 y debe tener 64 bytes de longitud.  
-   **RSNULL**: consulte [RFC2437](https://tools.ietf.org/html/rfc2437), un caso de uso especializado para habilitar ciertos escenarios de TLS.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Algoritmos de clave simétrica (solo HSM administrado)
- **AES-KW**: encapsulado de claves AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- **AES-GCM**: cifrado AES en el modo de contador Galois ([NIST SP 800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC**: cifrado AES en el modo de encadenamiento de bloques de cifrado ([NIST SP 800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> La implementación de AES-GCM actual y las API correspondientes son experimentales. La implementación y las API pueden cambiar considerablemente en las iteraciones futuras. 

##  <a name="key-operations"></a>Operaciones con claves

Key Vault, incluido el HSM administrado, admite las siguientes operaciones en objetos de clave:  

-   **Crear**: permite a un cliente crear una clave en Key Vault. El valor de la clave lo genera y almacena Key Vault y no se entrega al cliente. Las claves asimétricas pueden crearse en Key Vault.  
-   **Import**: permite a un cliente importar una clave existente en Key Vault. Se pueden importar claves asimétricas en Key Vault mediante una serie de métodos de empaquetado diferentes dentro de una construcción JWK. 
-   **Actualizar**: permite a un cliente con los permisos suficientes modificar los metadatos (atributos de la clave) asociados con una clave almacenada previamente en Key Vault.  
-   **Eliminar**: permite a un cliente con los permisos suficientes eliminar una clave de Key Vault.  
-   **Enumerar**: permite a un cliente enumerar todas las claves de un determinado almacén de Key Vault.  
-   **Enumerar versiones**: permite a un cliente enumerar todas las versiones de una clave determinada en un determinado almacén de Key Vault.  
-   **Obtener**: permite a un cliente recuperar las partes públicas de una clave determinada en un almacén de Key Vault.  
-   **Backup (Copia de seguridad)** : exporta una clave en un formato protegido.  
-   **Restore (Restaurar)** : importa una clave desde una copia de seguridad previa.  

Para más información, consulte las [operaciones clave en la referencia de la API REST de Key Vault](/rest/api/keyvault).  

Una vez creada una clave en Key Vault, se pueden realizar las siguientes operaciones criptográficas con la clave:  

-   **Firmar y verificar**: estrictamente, esta operación es "firmar un hash" o "verificar un hash", ya que Key Vault no admite la creación de un hash del contenido como parte de la creación de la firma. Las aplicaciones deben crear el hash de los datos que se van a firmar de modo local y, a continuación, solicitar que Key Vault firme el hash. La verificación de valores hash firmados se admite como una operación conveniente para aplicaciones que no pueden acceder a material de clave [público]. Para obtener el mejor rendimiento de la aplicación, las operaciones VERIFY deben realizarse localmente.  
-   **Cifrado/Encapsulado de clave**: una clave almacenada en Key Vault se puede utilizar para proteger otra clave, normalmente una clave de cifrado de contenido (CEK) simétrica. Cuando la clave en Key Vault es asimétrica, se usa el cifrado de claves. Por ejemplo, RSA-OAEP y las operaciones WRAPKEY/UNWRAPKEY son equivalentes a ENCRYPT/DECRYPT. Cuando la clave en Key Vault es simétrica, se usa el encapsulado de clave. Por ejemplo, AES-KW. La operación WRAPKEY se admite como una operación conveniente para aplicaciones que no pueden acceder a material de clave [público]. Para obtener el mejor rendimiento de la aplicación, las operaciones WRAPKEY deben realizarse localmente.  
-   **Cifrar y descifrar**: una clave almacenada en Key Vault puede utilizarse para cifrar o descifrar un único bloque de datos. El tamaño del bloque se determina en función del tipo de clave y del algoritmo de cifrado seleccionado. La operación de cifrado se proporciona por comodidad, para las aplicaciones que no pueden acceder a material de clave [público]. Para obtener el mejor rendimiento de la aplicación, las operaciones ENCRYPT se deben realizar localmente.  

Mientras que WRAPKEY/UNWRAPKEY con claves asimétricas puede parecer superfluo (porque la operación es equivalente a ENCRYPT/DECRYPT), es importante el uso de operaciones distintas. La distinción proporciona separación semántica y de autorización de estas operaciones, así como coherencia cuando otros tipos de claves son compatibles con el servicio.  

Azure Key Vault no admite operaciones de EXPORTACIÓN. Cuando se aprovisiona una clave en el sistema, no se puede extraer ni modificar el material de la clave. Sin embargo, los usuarios de Key Vault pueden requerir su clave para otros casos de uso, por ejemplo, después de que se haya eliminado. En este caso, pueden usar las operaciones de COPIA DE SEGURIDAD y RESTAURACIÓN para importar o exportar la clave en un formato protegido. Las claves creadas por la operación de COPIA DE SEGURIDAD no se pueden utilizar fuera de Key Vault. Como alternativa, la operación de IMPORTACIÓN se puede utilizar en varias instancias de Key Vault.  

Los usuarios pueden restringir cualquiera de las operaciones criptográficas que admite Key Vault en una base por cada clave mediante la propiedad key_ops del objeto JWK.  

Para más información acerca de los objetos JWK, consulte [Clave web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Atributos de clave

Además del material de la clave, se pueden especificar los siguientes atributos. En una solicitud JSON, la palabra clave attributes y las llaves, "{' '}", son obligatorias aunque no haya ningún atributo especificado.  

- *enabled*: booleano, opcional, el valor predeterminado es **true**. Especifica si la clave está habilitada y se puede utilizar para operaciones criptográficas. El atributo *enabled* se utiliza junto con *nbf* y *exp*. Cuando se produce una operación entre *nbf* y *exp*, solo se permitirá si *enabled* está establecido en **true**. Las operaciones fuera de la franja *nbf* / *exp* se establecen automáticamente como no permitidas, salvo en determinados tipos de operación en [condiciones particulares](#date-time-controlled-operations).
- *nbf*: tipo IntDate, opcional, el valor predeterminado es now (ahora). El atributo *nbf* (no antes de) identifica la hora antes de la cual la clave NO DEBE utilizarse para operaciones criptográficas, excepto para ciertos tipos de operación en [condiciones particulares](#date-time-controlled-operations). El procesamiento del atributo *nbf* requiere que la fecha y hora actual sea posterior o igual que la fecha y hora enumerada en el atributo *nbf*. Key Vault PUEDE proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  
- *exp*: tipo IntDate, opcional, el valor predeterminado es "forever" (indefinidamente). El atributo *exp* (hora de expiración) identifica la hora después de la cual la clave no debe utilizarse para operaciones criptográficas, excepto para ciertos tipos de operación en las [condiciones particulares](#date-time-controlled-operations). El procesamiento del atributo *exp* requiere que la fecha y hora actual sea anterior a la fecha y hora enumerada en el atributo *exp*. Key Vault PUEDE proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  

Existen atributos de solo lectura adicionales que se incluyen en cualquier respuesta que incluya atributos de clave:  

- *created*: IntDate, opcional. El atributo *created* indica cuándo se creó esta versión de la clave. El valor es NULL para las claves creadas antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.  
- *updated*: IntDate, opcional. El atributo *updated* indica cuándo se modificó esta versión de la clave. El valor es NULL para las claves modificadas por última vez antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.  

Para más información acerca de IntDate y otros tipos de datos, consulte [Acerca de claves, secretos y certificados: [Tipos de datos](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operaciones controladas de fecha y hora

Las claves todavía no válidas y las expiradas, fuera de la franja *nbf* / *exp*, funcionarán para las operaciones **decrypt**, **unwrap** y **verify** (no devolverán 403, prohibido). La razón de usar el estado "todavía no válido" es permitir que una clave se pruebe antes de su uso en producción. La razón de usar el estado "expirado" es permitir las operaciones de recuperación de datos que se crearon cuando la clave era válida. Además, puede deshabilitar el acceso a una clave mediante directivas de Key Vault o actualizando el atributo de clave *enabled* como **false**.

Para más información sobre los tipos de datos, vea [Tipos de datos](../general/about-keys-secrets-certificates.md#data-types).

Para más información sobre otros posibles atributos, vea [Clave web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Etiquetas de la clave

Puede especificar metadatos específicos de la aplicación adicionales en forma de etiquetas. Key Vault admite hasta 15 etiquetas, cada una de las cuales puede tener un nombre de 256 caracteres y un valor de 256 caracteres.  

> [!NOTE] 
> El autor de llamada puede leer las etiquetas si tienen el permiso *list* o *get* para esa clave.

##  <a name="key-access-control"></a>Control de acceso a claves

El control de acceso para las claves administradas por Key Vault se proporciona en el nivel de un almacén de claves que actúa como contenedor de claves. La directiva de control de acceso para las claves es distinta de la directiva de control de acceso para los secretos en la misma instancia de Key Vault. Los usuarios pueden crear uno o varios almacenes para almacenar las claves y están obligados a mantener la segmentación adecuada del escenario y la administración de claves. El control de acceso para claves es independiente del control de acceso para secretos.  

Se pueden conceder los permisos siguientes, por usuario o por entidad de servicio, en la entrada de control de acceso a las claves de un almacén. Estos permisos reflejan fielmente las operaciones permitidas en un objeto de clave.  La operación para conceder acceso a una entidad de servicio del almacén de claves solo se realiza una vez y será igual en todas las suscripciones de Azure. Puede usarlo para implementar tantos certificados como desee. 

- Permisos para las operaciones de administración de claves
  - *get*: leer la parte pública de una clave, además de sus atributos
  - *list*: enumerar las claves o las versiones de una clave almacenada en un almacén de claves
  - *update*: actualizar los atributos de una clave
  - *create*: crear nuevas claves
  - *import*: importar una clave a un almacén de claves
  - *delete*: eliminar el objeto de clave
  - *recover*: recuperar una clave eliminada
  - *backup*: copia de seguridad de una clave de un almacén de claves
  - *restore*: restaurar una copia de seguridad de una clave a un almacén de claves

- Permisos para las operaciones criptográficas
  - *decrypt*: usar la clave para desproteger una secuencia de bytes
  - *encrypt*: usar la clave para proteger una secuencia arbitraria de bytes
  - *unwrapKey*: usar la clave para desproteger claves simétricas encapsuladas
  - *wrapKey*: usar la clave para proteger una clave simétrica
  - *verify*: usar la clave para verificar hashes  
  - *sign*: usar la clave para firmar hashes
    
- Permisos para las operaciones con privilegios
  - *purge*: purgar (eliminar permanentemente) una clave eliminada

Para más información sobre cómo trabajar con claves, consulte las [operaciones con claves en la referencia de la API de REST de Key Vault](/rest/api/keyvault). Para obtener información sobre cómo establecer permisos, vea [Almacenes: crear o actualizar](/rest/api/keyvault/vaults/createorupdate) y [Almacenes: actualizar directiva de acceso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Pasos siguientes
- [Acerca de Key Vault](../general/overview.md)
- [Acerca de HSM administrado](../managed-hsm/overview.md)
- [Información acerca de los secretos](../secrets/about-secrets.md)
- [Información acerca de los certificados](../certificates/about-certificates.md)
- [Introducción a la API REST de Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticación, solicitudes y respuestas](../general/authentication-requests-and-responses.md)
- [Guía del desarrollador de Key Vault](../general/developers-guide.md)
