---
title: Protección de activos de Azure CDN con autenticación por tokens | Microsoft Docs
description: Obtenga información sobre cómo usar la autenticación por tokens para proteger el acceso a los recursos de Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: b28041d0bfcc60b3973e17d1883ef63be521a515
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613106"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Protección de los activos de Azure CDN con autenticación por tokens

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general

La autenticación por tokens es un mecanismo que permite impedir que Azure Content Delivery Network (CDN) proporcione recursos a clientes no autorizados. Normalmente esto se hace para evitar la *vinculación activa* de contenido, donde otro sitio web, como un panel de mensajes, usa los recursos sin permiso. La vinculación activa puede repercutir en los costos de la entrega de contenido. Al habilitar la autenticación por tokens en la red de entrega de contenido, el servidor perimetral de dicha red autentica las solicitudes antes de que la propia red entregue el contenido. 

## <a name="how-it-works"></a>Funcionamiento

La autenticación por tokens verifica que un sitio de confianza genere las solicitudes, para lo que se requiere que contengan un valor de token con información codificada sobre el solicitante. El contenido solo se proporciona al solicitante cuando la información codificada cumple los requisitos; de lo contrario, las solicitudes se deniegan. Puede configurar los requisitos mediante el uso de uno o varios de los siguientes parámetros:

- País o región: permitir o denegar las solicitudes que se originan en los países o regiones especificados con su [código de país o región](/previous-versions/azure/mt761717(v=azure.100)).
- Dirección URL: permitir solo las solicitudes que coinciden con el recurso o la ruta de acceso especificados.
- Host: permitir o denegar las solicitudes que usan los hosts especificados en el encabezado de la solicitud.
- Origen de referencia: permitir o denegar las solicitudes procedentes del origen de referencia especificado.
- Dirección IP: permitir solo las solicitudes cuyo origen es una dirección IP específica o subred IP concretas.
- Protocolo: permitir o denegar las solicitudes en función del protocolo usado para solicitar el contenido.
- Fecha de expiración: asignar un período de fecha y hora para asegurarse de que un vínculo solo es válido durante un tiempo limitado.

Para obtener más información, vea los ejemplos de configuración detallados para cada parámetro en [Configuración de la autenticación por tokens](#setting-up-token-authentication).

>[!IMPORTANT] 
> Si está habilitada la autorización de token en cualquier ruta de acceso de esta cuenta, el modo de caché estándar es el único modo que se puede usar para el almacenamiento en caché de la cadena de consulta. Para más información, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Arquitectura de referencia

En el siguiente diagrama de flujo de trabajo se describe cómo la red de entrega de contenido usa la autenticación por tokens para trabajar con la aplicación web.

![Flujo de trabajo de la autenticación por tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validación de tokens en el punto de conexión de red CDN
    
En el diagrama de flujo siguiente se describe cómo Azure CDN valida una solicitud del cliente cuando se configura la autenticación por tokens en el punto de conexión de la red de entrega de contenido.

![Lógica de la validación de tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuración de la autenticación por tokens

1. En [Azure Portal](https://portal.azure.com), busque el perfil de la red CDN y seleccione **Administrar** para iniciar el portal complementario.

    ![Botón de administración del perfil de la red CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Mantenga el mouse sobre **HTTP grande** y después seleccione **Token Auth** (Autenticación por tokens) en el control flotante. Después puede configurar la clave y los parámetros de cifrado como sigue:

   1. Cree una o varias claves de cifrado. Una clave de cifrado distingue mayúsculas de minúsculas y puede contener cualquier combinación de caracteres alfanuméricos. No se permite ningún otro tipo de caracteres, incluidos espacios. La longitud máxima es de 250 caracteres. Para asegurarse de que las claves de cifrado sean aleatorias, se recomienda crearlas con la [herramienta OpenSSL](https://www.openssl.org/). 

      La herramienta OpenSSL tiene la sintaxis siguiente:

      `rand -hex <key length>`

      Por ejemplo:

      `OpenSSL> rand -hex 32` 

      Para evitar el tiempo de inactividad, cree un elemento principal y una clave de copia de seguridad. Una clave de copia de seguridad proporciona un acceso ininterrumpido a su contenido cuando se actualiza la clave principal.
    
   2. Escriba una clave de cifrado única en el cuadro **Clave principal** y, opcionalmente, escriba una clave de copia de seguridad en el cuadro **Clave de copia de seguridad**.

   3. Seleccione la versión de cifrado mínima para cada clave de la lista **Minimum Encryption Version** (Versión mínima de cifrado) y después seleccione **Actualizar**:
      - **V2**: indica que la clave se puede utilizar para generar tokens de versión 2.0 y 3.0. Use esta opción solo si está realizando la transición de una clave de cifrado de la versión 2.0 heredada a una clave de la versión 3.0.
      - **V3**: (recomendada) indica que la clave solo se puede utilizar para generar tokens de la versión 3.0.

      ![Clave de configuración de autenticación por tokens de la red CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Use la herramienta de cifrado para configurar los parámetros de cifrado y generar un token. Con la herramienta de cifrado, puede permitir o denegar las solicitudes según la fecha de expiración, el país o región, el origen de referencia, el protocolo y la dirección IP del cliente (con cualquier combinación). Aunque no hay ningún límite en el número y la combinación de parámetros que se pueden combinar para formar un token, la longitud total de un token está limitada a 512 caracteres. 

      ![Herramienta de cifrado de la red CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Escriba los valores para uno o varios de los siguientes parámetros de cifrado en la sección **Encrypt Tool** (Herramienta de cifrado): 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nombre de parámetro</th> 
      >   <th>Descripción</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Asigna una fecha de expiración a un token, tras la cual el token expira. Las solicitudes que se presenten después de la fecha de expiración se deniegan. Este parámetro utiliza una marca de tiempo UNIX, que se basa en el número de segundos transcurridos desde la época UNIX estándar de `1/1/1970 00:00:00 GMT`. (Puede usar herramientas en línea para la conversión entre la hora estándar y la hora UNIX).> 
      >    Por ejemplo, si desea que el token expire en la fecha `12/31/2016 12:00:00 GMT`, introduzca el valor de marca de tiempo UNIX, `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Permite adaptar tokens para un recurso o ruta de acceso concretos. Restringe el acceso a las solicitudes cuya dirección URL comience con una ruta de acceso relativa específica. Las direcciones URL distinguen mayúsculas de minúsculas. Incluya varias rutas de acceso separadas por comas y no agregue espacios. En función de los requisitos, puede configurar otros valores para proporcionar distintos niveles de acceso.> 
      >    Por ejemplo, para la dirección URL `http://www.mydomain.com/pictures/city/strasbourg.png`, estas solicitudes se permiten para los siguientes valores de entrada: 
      >    <ul>
      >       <li>Valor de entrada `/`: se permiten todas las solicitudes.</li>
      >       <li>Valor de entrada `/pictures`: se permiten las siguientes solicitudes: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Valor de entrada `/pictures/`: solo se permiten las solicitudes que contienen la ruta de acceso `/pictures/`. Por ejemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Valor de entrada `/pictures/city/strasbourg.png`: solo se permiten las solicitudes de esta ruta de acceso y recurso específicos.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Solo permite solicitudes que se originen en uno o más países o regiones especificados. Se deniegan las solicitudes originadas en los demás países o regiones. Utilice un [código ISO 3166 de país o región](/previous-versions/azure/mt761717(v=azure.100)) de dos letras para cada país o región, sepárelos con una coma y no agregue espacios. Por ejemplo, si solo desea permitir el acceso desde Estados Unidos y Francia, escriba `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Deniega solicitudes que se originan en uno o más países o regiones especificados. Se permiten las solicitudes originadas en todos los demás países o regiones. La implementación es la misma que con el parámetro <b>ec_country_allow</b>. Si hay un código de país o región en los parámetros <b>ec_country_allow</b> y <b>ec_country_deny</b>, el parámetro <b>ec_country_allow</b> tiene prioridad.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Solo se permiten solicitudes del origen de referencia especificado. Un origen de referencia identifica la dirección URL de la página web vinculada al recurso que se solicita. No incluya el protocolo en el valor del parámetro.> 
      >    Se permiten los siguientes tipos de entrada:
      >    <ul>
      >       <li>Un nombre de host o un nombre de host y una ruta de acceso.</li>
      >       <li>Varios orígenes de referencia. Para agregar varios orígenes de referencia, sepárelos por comas y no agregue ningún espacio. Si especifica un valor para el origen de referencia, pero no se envía la información de este en la solicitud debido a alguna configuración del explorador, la solicitud se deniega de forma predeterminada.</li> 
      >       <li>Solicitudes en blanco o en las que falta información sobre el origen de referencia. De forma predeterminada, el parámetro <b>ec_ref_allow</b> bloquea estos tipos de solicitudes. Para permitir estas solicitudes, escriba el texto "falta" o escriba un valor en blanco (mediante el uso de una coma final).</li> 
      >       <li>Subdominios. Para permitir subdominios, escriba un asterisco (\*). Por ejemplo, para permitir todos los subdominios de `contoso.com`, escriba `*.contoso.com`.</li>
      >    </ul> 
      >    Por ejemplo, para permitir el acceso a las solicitudes de `www.contoso.com`, todos los subdominios de `contoso2.com` y las solicitudes sin origen de referencia o con un origen de referencia en blanco, introduzca `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Deniega solicitudes del origen de referencia especificado. La implementación es la misma que con el parámetro <b>ec_ref_allow</b>. Si un origen de referencia está presente en los parámetros <b>ec_ref_allow</b> y <b>ec_ref_deny</b>, el parámetro <b>ec_ref_allow</b> tiene prioridad.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Solo se permiten solicitudes del protocolo especificado. Los valores válidos son `http`, `https` o `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Deniega solicitudes del protocolo especificado. La implementación es la misma que con el parámetro <b>ec_proto_allow</b>. Si un protocolo está presente en los parámetros <b>ec_proto_allow</b> y <b>ec_proto_deny</b>, el parámetro <b>ec_proto_allow</b> tiene prioridad.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Restringe el acceso a la dirección IP del solicitante especificado. Se admiten tanto IPV4 como IPV6. Puede especificar una única dirección IP de solicitud o direcciones IP asociadas a una subred específica. Por ejemplo, `11.22.33.0/22` permite solicitudes desde las direcciones IP 11.22.32.1 a 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Cuando termine de especificar los valores de los parámetros de cifrado, seleccione una clave para cifrar (si ha creado una clave principal y una clave de copia de seguridad) en la lista **Key To Encrypt** (Clave para cifrar).
    
   6. Seleccione una versión de cifrado de la lista **Encryption Version** (Versión de cifrado): **V2** para la versión 2 o **V3** para la versión 3 (recomendado). 

   7. Seleccione **Cifrar** para generar el token.

      Una vez generado el token, se muestra en el cuadro **Generated Token** (Token generado). Para usar el token, anéxelo como una cadena de consulta al final del archivo en la ruta de acceso de la dirección URL. Por ejemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcionalmente, pruebe el token con la herramienta de descifrado para poder ver los parámetros del token. Pegue el valor del token en el cuadro **Token to Decrypt** (Token para descifrar). Seleccione la clave de cifrado que se usará en la lista **Key To Decrypt** (Clave para descifrar) y después seleccione **Descifrar**.

      Una vez descifrado el token, sus parámetros se muestran en el cuadro **Parámetros originales**.

   9. También puede personalizar el tipo de código de respuesta que se devuelve cuando se deniega una solicitud. Seleccione **Habilitado** y, después, seleccione el código de respuesta de la lista **Código de respuesta**. El **Nombre de encabezado** se establece automáticamente en **Ubicación**. Seleccione **Guardar** para implementar el nuevo código de respuesta. Para determinados códigos de respuesta, también debe especificar la dirección URL de la página de error en el cuadro **Valor de encabezado**. El código de respuesta **403** (Prohibido) está seleccionado de forma predeterminada. 

3. En **HTTP Large** (HTTP grandes), seleccione **Motor de reglas**. Utilice el motor de reglas para definir las rutas de acceso para aplicar la característica, habilitar la característica de autenticación por tokens y habilitar otras funcionalidades relacionadas con la autenticación por tokens. Para más información, vea [Referencia del motor de reglas](./cdn-verizon-premium-rules-engine-reference.md).

   1. Seleccione una regla existente o cree una para definir el recurso o la ruta de acceso para los que desea aplicar la autenticación por tokens. 
   2. Para habilitar la autenticación por tokens según una regla, seleccione **[Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** (Autenticación por tokens) en la lista **Características** y después seleccione **Habilitada**. Seleccione **Actualizar** si va a actualizar una regla o **Agregar** si va a crearla.
        
      ![Ejemplo de autenticación por tokens habilitada en el motor de reglas de la red CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. En el motor de reglas, también puede habilitar otras características relacionadas con la autenticación por tokens. Para habilitar cualquiera de las siguientes características, selecciónela en la lista **Características** y después seleccione **Habilitada**.
    
   - **[Token Auth Denial Code](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)** (Código de denegación de autenticación de token): determina el tipo de respuesta que se devuelve al usuario cuando se deniega una solicitud. Las reglas establecidas aquí invalidan el código de respuesta especificado en la sección **Custom Denial Handling** (Control de denegación personalizado) de la página de autenticación por tokens.

   - **[Token Auth Ignore URL Case](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)** (Ignorar mayúsculas y minúsculas en URL de autenticación de tokens): determina si la dirección URL usada para validar el token distingue mayúsculas de minúsculas.

   - **[Token Auth Parameter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)** (Parámetro de autenticación por tokens): cambia el nombre del parámetro de cadena de consulta de autenticación por tokens que aparece en la dirección URL solicitada. 
        
     ![Ejemplo de configuración de la autenticación por tokens en el motor de reglas de la red CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Puede personalizar el token mediante el acceso al código fuente en [GitHub](https://github.com/VerizonDigital/ectoken).
   Idiomas disponibles:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Precios de las características y los proveedores de Azure CDN

Para información sobre las características, consulte [Características de producto de Azure CDN](cdn-features.md). Para obtener información sobre los precios, consulte [Precios de Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
