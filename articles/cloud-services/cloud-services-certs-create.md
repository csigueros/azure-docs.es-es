---
title: Cloud Services (clásico) y certificados de administración | Microsoft Docs
description: Obtenga información sobre cómo crear e implementar certificados para Cloud Services y para la autenticación con la API de administración en Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 926d711f86a07ee22a4370e838efae9f4e0be957
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126219"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Introducción a los certificados de Azure Cloud Services (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

Los certificados se usan en Azure para los servicios en la nube ([certificados de servicio](#what-are-service-certificates)) y para realizar la autenticación con la API de administración ([certificados de administración](#what-are-management-certificates)). En este tema se proporciona información general de ambos tipos de certificado y se explica cómo [crearlos](#create) y cómo implementarlos en Azure.

Los certificados usados en Azure son certificados x.509 v3 y pueden estar firmados por otro certificado de confianza o estar autofirmados. Un certificado autofirmado está firmado por su propio creador; por lo tanto, no es de confianza de forma predeterminada. La mayoría de los exploradores pueden pasar por alto este problema. Solo debe usar certificados autofirmados cuando desarrolle y pruebe sus servicios en la nube. 

Los certificados utilizados por Azure pueden contener una clave pública. Los certificados tienen una huella digital que proporciona un medio para identificarlos de forma inequívoca. Esta huella digital se utiliza en el [archivo de configuración](cloud-services-configure-ssl-certificate-portal.md) de Azure para identificar qué certificado debe usar un servicio en la nube. 

>[!Note]
>Azure Cloud Services no acepta el certificado cifrado AES256-SHA256.

## <a name="what-are-service-certificates"></a>¿Qué son los certificados de servicio?
Los certificados de servicio están vinculados a los servicios en la nube y posibilitan la comunicación segura hacia y desde el servicio. Por ejemplo, si implementó un rol web, desearía proporcionar un certificado que pueda autenticar un punto de conexión HTTPS expuesto. Los certificados de servicio, definidos en la definición de servicio, se implementan automáticamente en la máquina virtual que está ejecutando una instancia del rol. 

Puede cargar certificados de servicio en Azure mediante Azure Portal o mediante el modelo de implementación clásica. Los certificados de servicio están asociados a un servicio en la nube específico. Se asignan a una implementación en el archivo de definición de servicio.

Los certificados de servicio se pueden administrar independientemente de los servicios y pueden ser administrados por distintas personas. Por ejemplo, un desarrollador puede cargar un paquete de servicio que hace referencia a un certificado que un administrador de TI ha cargado previamente en Azure. Un administrador de TI puede administrar y renovar el certificado (cambiando la configuración del servicio) sin necesidad de cargar un nuevo paquete de servicio. La actualización sin un nuevo paquete de servicio es posible porque el nombre lógico, el nombre y la ubicación del certificado se encuentran en el archivo de definición de servicio y la huella digital del certificado se especifica en el archivo de configuración de servicio. Para actualizar el certificado, solo es necesario cargar un nuevo certificado y cambiar el valor de huella digital en el archivo de configuración de servicio.

>[!Note]
>El artículo [P+F de Cloud Services - Configuración y administración](cloud-services-configuration-and-management-faq.yml) dispone de información útil sobre los certificados.

## <a name="what-are-management-certificates"></a>¿Qué son los certificados de administración?
Los certificados de administración le permiten autenticar con el modelo de implementación clásica. Muchos programas y herramientas (como Visual Studio o Azure SDK) utilizan estos certificados para automatizar la configuración y la implementación de diferentes servicios de Azure. Estos no están relacionados realmente con servicios en la nube. 

> [!WARNING]
> Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados. 
> 
> 

### <a name="limitations"></a>Limitaciones
Hay un límite de 100 certificados de administración por suscripción. También hay un límite de 100 certificados de administración para todas las suscripciones bajo un identificador de usuario de un administrador de servicio específico. Si ya ha se ha usado el identificador de usuario para el administrador de la cuenta para agregar 100 certificados de administración y se necesitan más certificados, puede agregar un coadministrador para añadir certificados adicionales. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Creación de un nuevo certificado autofirmado
Puede utilizar cualquier herramienta disponible para crear un certificado autofirmado, siempre que cumpla esta configuración:

* Un certificado X.509.
* Contiene una clave pública.
* Creado para intercambio de claves (archivo .pfx).
* El nombre de sujeto debe coincidir con el dominio usado para tener acceso al servicio en la nube.

    > No se puede adquirir un certificado TLS/SSL para el dominio cloudapp.net (ni para ningún dominio relacionado con Azure); el nombre del firmante del certificado debe coincidir con el nombre de dominio personalizado que se usa para obtener acceso a la aplicación. Por ejemplo, **contoso.net**, no **contoso.cloudapp.net**.

* Mínimo de cifrado de 2.048 bits.
* **Solo certificado de servicio**: el certificado de cliente debe encontrarse en el almacén de certificados *Personal*.

Hay dos métodos sencillos para crear un certificado en Windows, con la utilidad `makecert.exe` o con IIS.

### <a name="makecertexe"></a>Makecert.exe
Esta utilidad ha quedado en desuso y ya no se documenta aquí. Para obtener más información, consulte [este artículo de MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Si desea utilizar el certificado con una dirección IP en lugar de un dominio, utilice la dirección IP en el parámetro - DnsName.


Si quiere utilizar este [certificado con el portal de administración](/previous-versions/azure/azure-api-management-certs), expórtelo a un archivo **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Servicios de Internet Information Server (IIS)
Hay muchas páginas en Internet que explican cómo hacer esto con IIS. [Aquí](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) hay una excelente que encontré en la que se explica bien. 

### <a name="linux"></a>Linux
[Este](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artículo se describe cómo crear certificados con SSH.

## <a name="next-steps"></a>Pasos siguientes
[Cargue el certificado de servicio en Azure Portal](cloud-services-configure-ssl-certificate-portal.md).

Cargue un [certificado de API de administración](/previous-versions/azure/azure-api-management-certs) en Azure Portal.