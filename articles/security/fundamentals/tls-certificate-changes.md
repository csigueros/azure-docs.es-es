---
title: Cambios en los certificados TLS de Azure
description: Cambios en los certificados TLS de Azure
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 09/13/2021
ms.author: mbaldwin
ms.openlocfilehash: 01acde63371fea6a8c3f752caf31fa883e5e617e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641225"
---
# <a name="azure-tls-certificate-changes"></a>Cambios en los certificados TLS de Azure  

Microsoft está actualizando los servicios de Azure para que usen los certificados TLS de un conjunto diferente de entidades de certificación (CA) raíz. Este cambio se realiza porque los certificados de entidad de certificación actuales no cumplen uno de los requisitos de la base de referencia del foro CA/Browser y se revocarán el 15 de febrero de 2021.

## <a name="when-will-this-change-happen"></a>¿Cuándo se producirá este cambio?

Los puntos de conexión de Azure existentes han haciendo el cambio de manera escalonada desde el 13 de agosto de 2020. Todos los puntos de conexión de TLS/SSL de Azure recién creados contienen certificados actualizados que se encadenan a las nuevas entidades de certificación raíz.

Todos los servicios de Azure se ven afectados por este cambio. Estos son algunos detalles adicionales de servicios específicos:

- Los servicios de [Azure Active Directory](../../active-directory/index.yml) (Azure AD) comenzaron esta transición el 7 de julio de 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) y [DPS](../../iot-dps/index.yml) permanecerán en la entidad de certificación raíz Baltimore CyberTrust, pero las entidades de certificación intermedias cambiarán. [Haga clic aquí para más información](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- Para [Azure Storage](../../storage/index.yml), [haga clic aquí para más información](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581).
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) permanecerá en la entidad de certificación raíz Baltimore CyberTrust, pero las entidades de certificación intermedias cambiarán. [Haga clic aquí para más información](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service permanecerá en la entidad de certificación raíz Baltimore CyberTrust, pero las entidades de certificación intermedias cambiarán. [Haga clic aquí para más información](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Es posible que los clientes necesiten actualizar sus aplicaciones después de este cambio, con el fin de evitar errores de conectividad al intentar conectarse a Azure Storage.
https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581
## <a name="what-is-changing"></a>¿Qué está cambiando?

En la actualidad, la mayoría de los certificados TLS usados por los servicios de Azure se encadenan a la siguiente CA raíz:

| Nombre común de la CA | Huella digital (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Los certificados TLS usados por los servicios de Azure se encadenarán a una de las siguientes CA raíz:

| Nombre común de la CA | Huella digital (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>¿Cuándo puedo retirar la huella digital intermedia anterior?

Los certificados de CA actuales *no* se revocarán hasta el 15 de febrero de 2021. Después de esa fecha, puede quitar las huellas digitales anteriores del código.

Si esta fecha cambia, se le notificará la nueva fecha de revocación.

## <a name="will-this-change-affect-me"></a>¿Me afectará este cambio? 

Esperamos que la **mayoría de los clientes de Azure no** se vean afectados.  Sin embargo, la aplicación puede resultar afectada si especifica explícitamente una lista de entidades de certificación aceptables. Esta práctica se conoce como anclaje de certificados.

Estas son algunas maneras de detectar si una aplicación se ve afectada:

- Busque en el código fuente la huella digital, el nombre común y otras propiedades de certificado de cualquiera de las entidades de certificación de Microsoft IT TLS que se encuentran [aquí](https://www.microsoft.com/pki/mscorp/cps/default.htm). Si hay alguna coincidencia, la aplicación se verá afectada. Para resolver este problema, actualice el código fuente para incluir las nuevas entidades de certificación. Como procedimiento recomendado, asegúrese de que las entidades de certificación se pueden agregar o editar rápidamente. Las regulaciones del sector requieren que los certificados de las entidades de certificación se reemplacen en un plazo máximo de siete días, por lo que es preciso que los clientes que usan anclaje reaccionen con rapidez.

- Si tiene alguna aplicación que se integra con las API de Azure u otros servicios de Azure y no está seguro de si usa el anclaje de certificados, póngase en contacto con el proveedor de la aplicación.

- Los distintos sistemas operativos y entornos de ejecución de lenguaje que se comunican con los servicios de Azure pueden requerir pasos adicionales para compilar correctamente la cadena de certificados con estas nuevas raíces:
    - **Linux**: muchas distribuciones requieren que se agreguen entidades de certificación a /etc/SSL/certs. Para obtener instrucciones específicas, vea la documentación de la distribución.
    - **Java**: asegúrese de que el almacén de claves de Java contenga las entidades de certificación indicadas anteriormente.
    - **Windows se ejecuta en entornos desconectados**: los sistemas que se ejecuten en entornos desconectados deberán agregar las nuevas raíces al almacén de entidades de certificación raíz de confianza y las intermedias al almacén de entidades de certificación intermedias.
    - **Android**: consulte la documentación del dispositivo y la versión de Android.
    - **Otros dispositivos de hardware, especialmente IoT**: póngase en contacto con el fabricante del dispositivo.

- Si en su entorno se ha establecido que las reglas de firewall permitan llamadas salientes solo a determinadas ubicaciones de descarga de la lista de revocación de certificados (CRL) o de comprobación del Protocolo de estado de certificados en línea (OCSP), tendrá que permitir las siguientes direcciones URL de CRL y OCSP:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## <a name="next-steps"></a>Pasos siguientes

Si tiene más preguntas, póngase en contacto con nosotros a través del departamento de [soporte técnico](https://azure.microsoft.com/support/options/).
