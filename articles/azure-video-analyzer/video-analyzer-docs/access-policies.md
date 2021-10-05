---
title: Directivas de acceso de Azure Video Analyzer
description: En este artículo se explica cómo Azure Video Analyzer usa tokens JWT en directivas de acceso para proteger vídeos.
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: fe421e429357000bf6380cdf18f3a029fea4f7c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670488"
---
# <a name="access-policies"></a>Directivas de acceso

Las directivas de acceso definen los permisos y la duración del acceso a un recurso de vídeo determinado de Video Analyzer. Estas directivas de acceso permiten un mayor control y flexibilidad al permitir que los tokens JWT de terceros (clientes que no son de AAD) proporcionen autorización a las API de cliente que habilitan: 

- acceso a metadatos de vídeo. 
- acceso a streaming de vídeos. 

## <a name="access-policy-definition"></a>Definición de una directiva de acceso

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> Solo se requiere un tipo de clave. 

### <a name="roles"></a>Roles

Actualmente, solo se admite el rol de lector.

### <a name="issuer-matching-rules"></a>Reglas de coincidencia del emisor

Se pueden especificar varios problemas en la directiva, mientras que un solo emisor se puede especificar en el token.  El emisor coincide si el emisor del token se encuentra entre los emisores especificados en la directiva.

### <a name="audience-matching-rules"></a>Reglas de coincidencia de audiencia

Si el valor de audiencia es ${System.Runtime.BaseResourceUrlPattern} para el recurso de vídeo, la audiencia que se proporciona en el token JWT debe coincidir con la dirección URL del recurso base. Si no es así, la audiencia del token debe coincidir con la audiencia de la directiva de acceso.

### <a name="claims-matching-rules"></a>Reglas de coincidencia de notificaciones

Se pueden especificar varias notificaciones en la directiva de acceso y en el token JWT.  Todas las notificaciones de una directiva de acceso debe proporcionarse en el token para pasar la validación; sin embargo, el token JWT puede tener notificaciones adicionales que no aparecen en la directiva de acceso.

### <a name="keys"></a>Claves

Se admiten dos tipos de claves: RSA y ECC.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg: algoritmo.  Puede ser 256, 384 o 512 
* kid: id. clave
* n: módulo
* e: exponente público 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg: algoritmo.  Puede ser 256, 384 o 512
* kid: id. clave
* x: valor de coordenada.
* y: valor de coordenada.

### <a name="token-validation-process"></a>Proceso de validación de tokens

Los clientes deben crear sus propios tokens JWT y se validarán mediante el método siguiente:

- En la lista de directivas que coinciden con el id. clave, validamos:
  - Firma de token
  - Expiración del token
  - Emisor
  - Público
  - Notificaciones adicionales

### <a name="policy-audience-and-token-matching-examples"></a>Ejemplos de coincidencia de tokens y audiencia de la directiva:

| **Audiencia de la directiva**                      | URL solicitada                         | Dirección URL de token                            | Resultado |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (Cualquier literal)                            | (CUALQUIERA)                                 | (Coincide)                              | Conceder  |
| (Cualquier literal)                            | (CUALQUIERA)                                 | (No coincide)                          | Denegar   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | Conceder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | Denegar   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | Conceder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | Conceder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | Conceder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | Conceder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | Denegar   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | Denegar   |

> [!NOTE]  
> Video Analyzer admite un máximo de 20 directivas.  ${System.Runtime.BaseResourceUrlPattern} permite una mayor flexibilidad para acceder a recursos específicos mediante una directiva de acceso y varios tokens.  A continuación, estos tokens permiten el acceso a distintos recursos de Video Analyzer en función de la audiencia. 

## <a name="creating-a-token"></a>Creación de un token

En esta sección, crearemos un token JWT que usaremos más adelante en el artículo.  Usaremos una aplicación de ejemplo que generará el token JWT y le proporcionará todos los campos necesarios para crear la directiva de acceso.

> [!NOTE] 
> Si está familiarizado con cómo generar un token JWT basado en un certificado RSA o ECC, puede omitir esta sección.

1. Clone el [repositorio de ejemplos de C# de AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). Luego, vaya a la carpeta *src/jwt-token-issuer* de la aplicación JWTTokenIssuer y busque la aplicación.
2. Abra Visual Studio Code y vaya a la carpeta donde haya descargado la aplicación JWTTokenIssuer. Esta carpeta debe contener el archivo *\*.csproj*.
3. En el panel del explorador, vaya al archivo *program.cs*.
4. En la línea 77, cambie el público por el punto de conexión de Video Analyzer, seguido de /videos/\*, para que tenga un aspecto similar al siguiente:

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > El punto de conexión de Video Analyzer se puede encontrar en la sección de información general del recurso de Video Analyzer en Azure Portal.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Captura de pantalla en la que se muestra el punto de conexión del widget de reproductor":::
    
5. En la línea 78, cambie el emisor al valor del emisor del certificado. Ejemplo: `https://contoso.com`
6. Guarde el archivo.    

   > [!NOTE]
   > Es posible que vea el mensaje `Required assets to build and debug are missing from 'jwt token issuer'. Add them?` Seleccione `Yes`.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Captura de pantalla en la que se muestra la solicitud de los activos necesarios en Visual Studio Code":::
   
7. Abra una ventana del símbolo del sistema y vaya a la carpeta con los archivos JWTTokenIssuer. Ejecute los dos comandos siguientes en orden: `dotnet build` y `dotnet run`. Si tiene la extensión de C# en Visual Studio Code, también puede seleccionar F5 para ejecutar la aplicación JWTTokenIssuer.

La aplicación se compila y, a continuación, se ejecuta. Una vez compilada, crea un certificado autofirmado y genera a partir de este la información del token JWT. También puede ejecutar el archivo JWTTokenIssuer.exe que se encuentra en la carpeta de depuración del directorio desde el que se compiló JWTTokenIssuer. La ventaja de ejecutar la aplicación es que puede especificar las opciones de entrada de la siguiente manera:

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer crea el token JWT y los componentes necesarios siguientes:

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

Asegúrese de copiar estos valores para usarlos posteriormente.


## <a name="creating-an-access-policy"></a>Creación de una directiva de acceso

Hay dos maneras de crear una directiva de acceso.

### <a name="in-the-azure-portal"></a>En Azure Portal

1. Inicie sesión en Azure Portal y vaya al grupo de recursos en el que se encuentra su cuenta de Video Analyzer.
1. Seleccione el recurso de Video Analyzer.
1. En **Video Analyzer**, seleccione **Directivas de acceso**.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Widget del reproductor: directivas de acceso en el portal.":::
   
1. Seleccione **Nueva** e indique la información siguiente:

   > [!NOTE] 
   > Estos valores proceden de la aplicación JWTTokenIssuer creada en el paso anterior.

   - Nombre de la directiva de acceso: cualquier nombre

   - Emisor: debe coincidir con el emisor del token JWT. 

   - Público: el público para el token JWT. El valor predeterminado es `${System.Runtime.BaseResourceUrlPattern}`.

   - Tipo de clave: RSA 

   - Algoritmo: los valores admitidos son RS256, RS384 y RS512

   - Identificador de clave: generado a partir del certificado. Para obtener más información, consulte [Creación de un token](#creating-a-token).

   - Módulo de clave RSA: este valor se genera a partir del certificado. Para obtener más información, consulte [Creación de un token](#creating-a-token).

   - Exponente de clave RSA: este valor se genera a partir del certificado. Para obtener más información, consulte [Creación de un token](#creating-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Widget del reproductor: directivas de acceso en el portal"::: 
   
1. Seleccione **Guardar**.
### <a name="create-access-policy-via-api"></a>Creación de una directiva de acceso a través de la API

Consulte API de Azure Resource Manager (ARM) 

## <a name="next-steps"></a>Pasos siguientes

[Información general](overview.md)
