---
title: Certificados de App Service Environment
description: Se explican los temas relacionados con los certificados de App Service Environment. Obtenga información sobre cómo funcionan los enlaces de certificados en las aplicaciones de un solo inquilino de App Service Environment.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: a86a5e67faafc88c837f7401db473b0164125a82
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525787"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados y App Service aislado 
> [!NOTE]
> Este artículo trata sobre App Service Environment v3, que se usa con planes de App Service v2 aislados.
>

App Service Environment es una implementación de Azure App Service que se ejecuta en una red virtual de Azure. Se puede implementar con un punto de conexión de la aplicación al que se puede acceder desde Internet o un punto de conexión de la aplicación que se encuentra en la red virtual. Si App Service aislado se implementa con un punto de conexión accesible a Internet, dicha implementación se denomina App Service aislado externo. Si App Service Environment se implementa con un punto de conexión en la red virtual, dicha implementación se denomina ASE con ILB. Puede obtener más información sobre App Service aislado con ILB en el documento [Creación y uso de App Service aislado con ILB](./creation.md).

## <a name="application-certificates"></a>Certificados de aplicación

Las aplicaciones hospedadas en una instancia de App Service aislado pueden usar las características de los certificados centradas en la aplicación que están disponibles en la instancia de App Service multiinquilino. Estas características son:

- Certificados SNI
- Certificados hospedados en Key Vault

Los requisitos e instrucciones para cargar y administrar dichos certificados están disponibles en [Adición de un certificado TLS/SSL en Azure App Service](../configure-ssl-certificate.md).

Una vez que el certificado se agrega a la aplicación de App Service o a la aplicación de funciones, se puede [proteger un nombre de dominio personalizado con él](../configure-ssl-bindings.md), o bien se puede [usar en el código de la aplicación](../configure-ssl-certificate-in-code.md).

## <a name="tls-settings"></a>Configuración de TLS

[TLS se puede configurar](../configure-ssl-bindings.md#enforce-tls-versions) en un nivel de aplicación.

## <a name="private-client-certificate"></a>Certificado de cliente privado

Es un caso de uso común consiste en configurar la aplicación como un cliente en un modelo cliente/servidor. Si protege el servidor con un certificado de entidad de certificación privado, deberá cargar el certificado de cliente en la aplicación. Las instrucciones siguientes servirán para la carga de certificados en el almacén de confianza de los trabajadores en el que se ejecuta la aplicación. Si carga el certificado en una aplicación, se puede utilizar con el resto de aplicaciones en el mismo plan de App Service sin volver a cargar el certificado.

>[!NOTE]
> Los certificados de cliente privados no se admiten fuera de la aplicación, lo que limita su uso en escenarios como la extracción de la imagen del contenedor de aplicaciones de un registro mediante un certificado privado y la validación de TLS a través de los servidores front-end mediante un certificado privado.

Para cargar el certificado en una aplicación en App Service Environment (archivo *.cer*), siga estos pasos. El archivo *.cer* se puede exportar desde el certificado. Al final hay un ejemplo de PowerShell para generar un certificado autofirmado temporal, por si se desea realizar pruebas:

1. Vaya a la aplicación que necesita el certificado en Azure Portal.
1. Vaya a la **configuración de TLS/SSL** en la aplicación. Haga clic en **Certificado de clave pública (.cer)** . Seleccione **Cargar certificado de clave pública**. Proporcione un nombre. Busque y seleccione el archivo *.cer*. Seleccione Cargar. 
1. Copie la huella digital.
1. Vaya a **Configuración de la aplicación**. Cree una configuración de aplicación WEBSITE_LOAD_ROOT_CERTIFICATES con la huella digital como valor. Si tiene varios certificados, puede colocarlos en la misma configuración separados por comas y sin espacios en blanco, como se muestra a continuación: 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

El certificado estará disponible para todas las aplicaciones del mismo plan de App Service que la aplicación que configuró dicho valor. Si necesita que esté disponible para las aplicaciones en otro plan de App Service, deberá repetir la operación de configuración de la aplicación en una aplicación en dicho plan de App Service. Para comprobar si el certificado está establecido, vaya a la consola de Kudu y emita este comando en la consola de depuración de PowerShell.

```azurepowershell-interactive
dir Cert:\LocalMachine\Root
```

Para realizar pruebas, puede crear un certificado autofirmado y generar un archivo *.cer* con el siguiente comando de PowerShell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -CertStoreLocation "Cert:\LocalMachine\My" -DnsName "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "Cert:\LocalMachine\My\" + $certificate.Thumbprint
$fileName = "exportedcert.cer"
Export-Certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

## <a name="next-steps"></a>Pasos siguientes

* Información sobre cómo [usar certificados en el código de aplicación](../configure-ssl-certificate-in-code.md)