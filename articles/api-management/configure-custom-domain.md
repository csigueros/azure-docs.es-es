---
title: Configuración de un nombre de dominio personalizado para la instancia de Azure API Management
titleSuffix: Azure API Management
description: En este tema, se explica cómo configurar un nombre de dominio personalizado para la instancia de Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: apimpm
ms.openlocfilehash: ae0e5fde64cb3a00f16882698b931c9eadcf623d
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779813"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Configuración de un nombre de dominio personalizado para la instancia de Azure API Management

Cuando se crea una instancia del servicio Azure API Management, Azure le asigna un subdominio `azure-api.net` (por ejemplo, `apim-service-name.azure-api.net`). También puede exponer los puntos de conexión de API Management con su propio nombre de dominio personalizado, como **`contoso.com`** . En este tutorial se muestra cómo asignar un nombre DNS personalizado existente a los puntos de conexión expuestos por una instancia de API Management.

> [!IMPORTANT]
> API Management solo acepta solicitudes con valores de [encabezado de host](https://tools.ietf.org/html/rfc2616#section-14.23) que coincidan con:
>
>* El nombre de dominio predeterminado
>* Cualquiera de los nombres de dominio personalizados configurados

> [!WARNING]
> Si desea mejorar la seguridad de sus aplicaciones con la asignación de certificados, debe usar un nombre de dominio personalizado y el certificado que administra, no el predeterminado. La asignación del certificado predeterminado depende en gran medida de las propiedades del certificado que no administra, lo que no se recomienda.

## <a name="prerequisites"></a>Requisitos previos

-   Una suscripción de Azure activa. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   Una instancia de API Management Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
-   Nombre de dominio personalizado que propiedad de su organización o suyo. En este tema no se dan instrucciones para adquirir un nombre de dominio personalizado.
-   Un [registro CNAME hospedado en un servidor DNS](#dns-configuration) que asigna el nombre de dominio personalizado al nombre de dominio predeterminado de una instancia de API Management. En este tema no se dan instrucciones para hospedar un registro CNAME.
-   Un certificado válido con una clave pública y privada (.PFX). El firmante o el nombre alternativo del firmante (SAN) debe coincidir con el nombre de dominio. De este modo, API Management puede exponer de forma segura direcciones URL mediante TLS.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Uso de Azure Portal para configurar un nombre de dominio personalizado

1. Vaya a la instancia de API Management en [Azure Portal](https://portal.azure.com/).
1. Seleccione **Dominios personalizados**.

    Hay varios puntos de conexión a los que puede asignar un nombre de dominio personalizado. En la actualidad, están disponibles los siguientes:

    | Punto de conexión | Valor predeterminado |
    | -------- | ----------- |
    | **Puerta de enlace** | El valor predeterminado es `<apim-service-name>.azure-api.net`. La puerta de enlace es el único punto de conexión disponible para la configuración en el nivel Consumo. |
    | **Portal para desarrolladores (heredado)** | El valor predeterminado es `<apim-service-name>.portal.azure-api.net`. |
    | **Portal para desarrolladores** | El valor predeterminado es `<apim-service-name>.developer.azure-api.net`. |
    | **Administración** | El valor predeterminado es `<apim-service-name>.management.azure-api.net`. |
    | **SCM** | El valor predeterminado es `<apim-service-name>.scm.azure-api.net`. |

    > [!NOTE]
    > Puede actualizar cualquiera de los puntos de conexión. Por lo general, los clientes actualizan **Puerta de enlace** (esta dirección URL se utiliza para llamar a la API expuesta a través de API Management) y **Portal** (dirección URL del portal para desarrolladores).
    > 
    > Solo los propietarios de instancias de API Management pueden usar los puntos de conexión de **administración** y **SCM** internamente. A estos puntos de conexión se les asigna con menos frecuencia un nombre de dominio personalizado.
    >
    > Los niveles **Prémium** y **Desarrollador** admiten la configuración de varios nombres de host para el punto de conexión **Puerta de enlace**.

1. Seleccione **+Agregar** o seleccione un punto de conexión existente que quiera actualizar.
1. En la ventana de la derecha, seleccione el **Tipo** de punto de conexión para el dominio personalizado.
1. En el campo **Nombre de host**, especifique el nombre que desee usar. Por ejemplo, `api.contoso.com`.
1. En **Certificado**, seleccione **Key Vault** o **Personalizado**.
    - **Key Vault**
        - Haga clic en **Seleccionar**.
        - En la lista desplegable, seleccione **Suscripción**.
        - En la lista desplegable, seleccione **Key Vault**.
        - Una vez cargados los certificados, seleccione el **certificado** en la lista desplegable.
        - Haga clic en **Seleccionar**.
    - **Personalizada**
        - Seleccione el campo **Archivo de certificado** para seleccionar y cargar un certificado.
        - Cargue un archivo .PFX válido y proporcione su **contraseña** si el certificado está protegido con una contraseña.
1. Al configurar un punto de conexión de puerta de enlace, seleccione o anule la selección de [otras opciones según sea necesario](#clients-calling-with-server-name-indication-sni-header), como **Negociar certificado de cliente** o **Enlace SSL predeterminado**.
1. Seleccione **Actualizar**.

    > [!NOTE]
    > Los nombres de dominio con caracteres comodín, como `*.contoso.com`, se admiten en todos los niveles, salvo en el nivel de consumo.

    > [!TIP]
    > Se recomienda usar [Azure Key Vault para administrar certificados](../key-vault/certificates/about-certificates.md) y configurarlos en `autorenew`.
    >
    > Si usa Azure Key Vault para administrar el certificado TLS/SSL de dominio personalizado, asegúrese de que el certificado se inserta en Key Vault [como un _certificado_](/rest/api/keyvault/createcertificate/createcertificate), no como un _secreto_.
    >
    > Para capturar un certificado TLS/SSL, API Management debe tener la lista y obtener los permisos de secretos en la instancia de Azure Key Vault que contiene el certificado. 
    >
    >* Cuando se usa Azure Portal, todos los pasos de configuración necesarios se realizarán automáticamente. 
    >* Cuando se usan las herramientas de línea de comandos o API Management, estos permisos se deben conceder manualmente en dos pasos:
    >    * Use la página de **identidades administradas** de la instancia de API Management, asegúrese de que está habilitada la identidad administrada y anote el identificador de la entidad que se muestra en esa página. 
    >    * Proporcione la lista de permisos y obtenga los permisos de secretos de este identificador de entidad de seguridad en la instancia de Azure Key Vault que contiene el certificado.
    >
    > Si el certificado está establecido en `autorenew` y el nivel de API Management tiene SLA (en todos los niveles, excepto el nivel de desarrollador), API Management seleccionará automáticamente la versión más reciente sin tiempo de inactividad para el servicio.

1. Haga clic en Aplicar.

    > [!NOTE]
    > El proceso de asignación del certificado puede tardar unos 15 minutos o más según el tamaño de la implementación. La SKU de desarrollador tiene un tiempo de inactividad, mientras que las SKU básica y superiores no lo tienen.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuración de DNS

Al configurar DNS para el nombre de dominio personalizado, tiene dos opciones:

-   Configurar un registro CNAME que apunte al punto de conexión del nombre de dominio personalizado configurado.
-   Configurar un registro A que apunte a la dirección IP de la puerta de enlace de API Management.

Mientras que los registros CNAME (o registros de alias) y los registros D le permiten asociar un nombre de dominio a un servidor específico o servicio, cada uno funciona de manera distinta. 

### <a name="cname-or-alias-record"></a>Registro de CNAME o Alias
Un registro CNAME asigna un dominio *específico*, como `contoso.com` o www\.contoso.com, a un nombre de dominio canónico. Una vez creado, el CNAME crea un alias para el dominio. La entrada de CNAME se resolverá en la dirección IP del servicio de dominio personalizado de manera automática, por lo que si la dirección IP cambia, no es preciso realizar ninguna acción.

> [!NOTE]
> Algunos registradores de dominio solo permiten asignar subdominios cuando se usa un registro CNAME, como www\.contoso.com, y no nombres raíz, como contoso.com. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, [la entrada de Wikipedia sobre el registro CNAME](https://en.wikipedia.org/wiki/CNAME_record) o el documento [Nombres de dominio IETF: implementación y especificación (en inglés)](https://tools.ietf.org/html/rfc1035).

### <a name="a-record"></a>Registro D
El registro D asigna un dominio, como `contoso.com` o **www\.contoso.com**, *o un nombre de dominio con carácter comodín*, como **\*.contoso.com**, a una dirección IP. Dado que cada registro D se asigna a una dirección IP estática, no puede resolver automáticamente los cambios que se produzcan en la dirección IP. Se recomienda usar el registro CNAME más estable en lugar de un registro D.

> [!NOTE]
> Si bien la dirección IP de la instancia de API Management es estática, es posible que cambien en algunos escenarios. Al elegir el método de configuración DNS, se recomienda usar un registro CNAME al configurar un dominio personalizado, ya que es más estable que un registro D en caso de que cambie la dirección IP. Más información en el [artículo de la documentación de IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) y las [Preguntas más frecuentes sobre API Management](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-).

## <a name="next-steps"></a>Pasos siguientes

[Actualización y escalado del servicio](upgrade-and-scale.md)