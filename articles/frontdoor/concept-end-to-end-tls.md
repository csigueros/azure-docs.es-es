---
title: TLS de un extremo a otro con Azure Front Door
description: Obtenga información sobre el cifrado TLS de un extremo a otro al usar Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 5e0cac6f9ba6a245ec201666adb2c5cfeed79c38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343104"
---
# <a name="end-to-end-tls-with-azure-front-door"></a>TLS de un extremo a otro con Azure Front Door

Seguridad de la capa de transporte (TLS), antes conocido como Capa de sockets seguros (SSL), es la tecnología de seguridad estándar para establecer un vínculo cifrado entre un servidor web y un explorador. Este vínculo garantiza que todos los datos que se pasan entre el servidor web y el explorador web siguen siendo privados y continúan estando cifrados.

Para cumplir los requisitos de seguridad o cumplimiento, Azure Front Door (AFD) admite el cifrado TLS de un extremo a otro. La descarga de TLS/SSL de Front Door finaliza la conexión TLS, descifra el tráfico en el Azure Front Door y vuelve a cifrar el tráfico antes de reenviarlo al back-end. Puesto que las conexiones al back-end se suceden a través de la dirección IP pública, se recomienda encarecidamente configurar HTTPS como protocolo de reenvío en su instancia de Azure Front Door para aplicar el cifrado TLS de un extremo a otro desde el cliente al back-end.

## <a name="end-to-end-tls-encryption"></a>Cifrado TLS de un extremo a otro

TLS de un extremo a otro permite proteger los datos confidenciales mientras están en tránsito al back-end, al tiempo que se beneficia de las características de Azure Front Door, como el equilibrio de carga global y el almacenamiento en caché. Algunas de las características también incluyen el enrutamiento basado en URL, la división TCP, el almacenamiento en caché en la ubicación perimetral más cercana a los clientes y la personalización de solicitudes HTTP en el perímetro.

Azure Front Door descarga las sesiones TLS en el perímetro y descifra las solicitudes de cliente. A continuación, aplica las reglas de enrutamiento configuradas para enrutar las solicitudes al back-end adecuado en el grupo de back-end. Entonces, Azure Front Door inicia una nueva conexión TLS al back-end y vuelve a cifrar todos los datos mediante el certificado del back-end antes de transmitir la solicitud al back-end. Cualquier respuesta del back-end se cifra a través del mismo proceso de vuelta al usuario final. Puede configurar su instancia de Azure Front Door para usar HTTPS como protocolo de reenvío para habilitar TLS de un extremo a otro.

## <a name="supported-tls-versions"></a>Versiones de TLS admitidas

Azure Front Door admite tres versiones del protocolo TLS: las versiones 1.0, 1.1 y 1.2 de TLS. Todos los perfiles de Azure Front Door creados después de septiembre de 2019 utilizan TLS 1.2 como mínimo de manera predeterminada, pero TLS 1.0 y TLS 1.1 siguen siendo compatibles con versiones anteriores.

Si bien Azure Front Door admite TLS 1.2, que presentó la autenticación mutua o de cliente en RFC 5246, actualmente, Azure Front Door no admite la autenticación mutua ni de cliente.

Puede configurar la versión de TLS mínima en Azure Front Door en la configuración HTTPS del dominio personalizado desde Azure Portal o la  [API REST de Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Actualmente, puede elegir entre 1.0 y 1.2. Por lo tanto, si se especifica TLS 1.2 como versión mínima, se controla la versión mínima aceptable de TLS que Azure Front Door aceptará de un cliente. Cuando Azure Front Door inicia el tráfico TLS hacia el back-end, intentará negociar la mejor versión de TLS que el back-end pueda aceptar de forma fiable y coherente.

## <a name="supported-certificates"></a>Certificados admitidos

Al crear el certificado TLS/SSL, tiene que crear una cadena de certificados completa con una entidad de certificación (CA) permitida que forme parte de la  [lista de CA de confianza de Microsoft](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT). Si usa una CA no permitida, la solicitud se rechazará.

No se permiten certificados de entidades de certificación internas ni certificados autofirmados.

## <a name="online-certificate-status-protocol-ocsp-stapling"></a>Asociación del Protocolo de estado de certificados en línea (OCSP)

De forma predeterminada, la asociación OCSP es compatible en Azure Front Door y no se requiere ninguna configuración.

## <a name="backend-tls-connection-azure-front-door-to-backend"></a>Conexión TLS de back-end (Azure Front Door a back-end)

En el caso de las conexiones HTTPS, Azure Front Door espera que el back-end presente el certificado de una entidad de certificación válida con nombres de firmante que coincidan con el *nombre de host* del back-end. Por ejemplo, si el nombre de host del back-end se establece en  `myapp-centralus.contosonews.net`  y el certificado que el back-end presenta durante el protocolo de enlace TLS no tiene  `myapp-centralus.contosonews.net`  ni `*.contosonews.net`  en el nombre del firmante, Azure Front Door rechazará la conexión y devolverá un error.

> [!NOTE]
> El certificado debe tener una cadena de certificados completa con certificados hoja e intermedios. La entidad de certificación raíz debe formar parte de la  [lista de CA de confianza de Microsoft](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT). Si se presenta un certificado sin una cadena completa, no se garantiza que las solicitudes que involucran ese certificado funcionen según lo previsto.

Desde el punto de vista de la seguridad, Microsoft no recomienda deshabilitar la comprobación del nombre del firmante del certificado. En determinados casos de uso, como para las pruebas, por ejemplo, su origen debe utilizar un certificado autofirmado. Como solución para resolver errores de conexión HTTPS, puede deshabilitar la comprobación del nombre del firmante del certificado para Azure Front Door. La opción para deshabilitar está presente en la configuración de Azure Front Door en Azure Portal y en BackendPoolsSettings en la API de Azure Front Door. 

## <a name="frontend-tls-connection-client-to-front-door"></a>Conexión de TLS de front-end (cliente a Front Door)

Para habilitar el protocolo HTTPS para distribuir de forma segura el contenido en un dominio personalizado de Azure Front Door, puede optar por usar un certificado administrado por Azure Front Door o usar su propio certificado.  

* El certificado administrado por Azure Front Door proporciona un certificado TLS/SSL estándar a través de DigiCert y se almacena en la instancia de Key Vault de Azure Front Door.   

* Si decide usar su propio certificado, puede incorporar un certificado de una entidad de certificación compatible que puede ser un TLS estándar, un certificado de validación extendido o incluso un certificado comodín.  

* No se admiten certificados autofirmados. Aprenda a  [habilitar HTTPS para un dominio personalizado](front-door-custom-domain-https.md).

### <a name="certificate-autorotation"></a>Rotación automática de certificados

En el caso de la opción de certificado administrado de Azure Front Door, este administra y rota automáticamente los certificados en los 90 días posteriores al tiempo de expiración. En el caso de la opción de certificado administrado estándar o premium de Azure Front Door, este administra y rota automáticamente los certificados en los 45 días posteriores al tiempo de expiración. Si usa un certificado administrado de Azure Front Door y observa que la fecha de expiración del certificado es inferior a 60 días, o 30 en el caso de la SKU estándar o premium, cree una incidencia de soporte técnico. 

Para su propio certificado TLS/SSL personalizado:

1. Establezca la versión del secreto en "Más reciente" para que el certificado se rote automáticamente a la versión más reciente cuando haya disponible una versión más reciente del certificado en el almacén de claves. En el caso de los certificados personalizados, el certificado rota automáticamente en un plazo de 1 a 2 días con una versión más reciente, independientemente del tiempo de expiración del certificado.

1. Si se selecciona una versión específica, no se admite la rotación automática. Tendrá que volver a seleccionar la nueva versión manualmente para rotar el certificado. La nueva versión del certificado o el secreto tarda hasta 24 horas en implementarse.

    Tendrá que asegurarse de que la entidad de servicio de Front Door siga teniendo acceso al almacén de claves. Consulte cómo conceder acceso al almacén de claves. El lanzamiento de certificados actualizados por parte de Azure Front Door no provocará tiempos de inactividad en producción, siempre que no se haya modificado el nombre o el nombre alternativo del firmante (SAN) del certificado.

## <a name="supported-cipher-suites"></a>Conjuntos de cifrado admitidos

Para TLS 1.2, se admiten los siguientes conjuntos de cifrado:

* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

> [!NOTE]
> Para Windows 10 y versiones posteriores, se recomienda habilitar uno o ambos conjuntos de cifrado ECDHE para mejorar la seguridad. Windows 8.1, 8 y 7 no son compatibles con estos conjuntos de cifrado ECDHE. Los conjuntos de cifrado DHE proporcionan compatibilidad con esos sistemas operativos.

Al usar dominios personalizados con TLS 1.0/1.1 habilitado, se admiten los siguientes conjuntos de cifrado:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
* TLS_RSA_WITH_AES_256_GCM_SHA384
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA
* TLS_RSA_WITH_AES_128_CBC_SHA
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

Azure Front Door no permite la configuración de conjuntos de cifrado específicos. Puede obtener un certificado TLS/SSL propio personalizado de la entidad de certificación (por ejemplo, Verisign, Entrust, o Digicert). Después, tenga los conjuntos de cifrado específicos marcados en el certificado al generarlo. 

## <a name="next-steps"></a>Pasos siguientes

* [Configure un dominio personalizado](front-door-custom-domain.md) para Azure Front Door.
* [Habilite HTTPS para un dominio personalizado](front-door-custom-domain-https.md).
