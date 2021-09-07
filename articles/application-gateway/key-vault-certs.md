---
title: Terminación TLS con certificados de Azure Key Vault
description: Obtenga información sobre cómo puede integrar Azure Application Gateway con Key Vault para certificados de servidor que se adjuntan a los clientes de escucha con HTTPS habilitado.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: cf0afce3c5495199b23059df226115154dade7f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736101"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminación TLS con certificados de Key Vault

[Azure Key Vault](../key-vault/general/overview.md) es un almacén de secretos administrado por la plataforma que puede usar para proteger los secretos, las claves y los certificados TLS/SSL. Azure Application Gateway admite la integración con Key Vault para certificados de servidor adjuntos a clientes de escucha con HTTPS habilitado. Esta compatibilidad está limitada a la versión 2 de la SKU de Application Gateway.

La integración de Key Vault ofrece dos modelos para la terminación TLS:

- Puede proporcionar explícitamente los certificados TLS/SSL asociados al cliente de escucha. Este modelo es la forma tradicional para pasar los certificados TLS/SSL a Application Gateway para la terminación TLS.
- También puede proporcionar una referencia a un certificado de Key Vault existente o un secreto al crear un cliente de escucha habilitado para HTTPS.

La integración de Application Gateway con Key Vault ofrece muchas ventajas, incluidas las siguientes:

- Mayor seguridad, dado que el equipo de desarrollo de aplicaciones no controla directamente los certificados TLS/SSL. La integración permite que un equipo de seguridad independiente haga lo siguiente:
  * Configurar instancias de Application Gateway.
  * Controlar los ciclos de vida de las instancias de Application Gateway.
  * Conceder permisos a las instancias de Application Gateway seleccionadas para tener acceso a los certificados almacenados en el almacén de claves.
- Compatibilidad con la importación de certificados existentes en el almacén de claves. También puede usar las API de Key Vault para crear y administrar certificados nuevos con cualquiera de los asociados de Key Vault de confianza.
- Compatibilidad con la renovación automática de certificados almacenados en el almacén de claves.

Actualmente, Application Gateway solo admite certificados validados por software. Los certificados validados por el módulo de seguridad de hardware (HSM) no se admiten. Después de configurar Application Gateway para usar certificados de Key Vault, sus instancias recuperan el certificado de Key Vault y lo instalan localmente para la terminación TLS. Además, las instancias sondean Key Vault en intervalos de 4 horas para recuperar una versión renovada del certificado, si existe. Si se encuentra un certificado actualizado, el certificado TLS/SSL asociado actualmente al cliente de escucha HTTPS rota automáticamente.

> [!NOTE]
> Azure Portal solo admite certificados de KeyVault, no secretos. Application Gateway sigue admitiendo hacer referencia a secretos desde KeyVault, pero solo a través de recursos que no son de portal, como PowerShell, la CLI, API, plantillas de ARM, etc. 

> [!WARNING]
> Azure Application Gateway actualmente solo admite cuentas de Key Vault de la misma suscripción que el recurso de Application Gateway. Si elige una instancia de Key Vault de una suscripción que no es la suya de Application Gateway, se producirá un error.

## <a name="how-integration-works"></a>Funcionamiento de la integración

La integración de Application Gateway con Key Vault requiere un proceso de configuración de tres pasos:

1. **Creación de una identidad administrada asignada por el usuario**

   Debe crear o volver a usar una identidad administrada asignada por el usuario existente, que Application Gateway usará para recuperar los certificados de Key Vault en su nombre. Para obtener más información, consulte [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Con este paso se crea una nueva identidad en el inquilino de Azure Active Directory. La identidad es de confianza para la suscripción que se usa para crear la identidad.

1. **Configuración del almacén de claves**

   A continuación, importe un certificado existente o cree uno nuevo en el almacén de claves. Las aplicaciones que se ejecutan a través de Application Gateway usarán el certificado. En este paso, también puede usar un secreto de almacén de claves que también permite el almacenamiento como archivo PFX codificado en Base 64 sin contraseña. Se recomienda usar un tipo de certificado debido a la capacidad de renovación automática que está disponible con estos objetos de tipo en el almacén de claves. Después de crear un Certificado o un Secreto, debe definir directivas de acceso en Key Vault para permitir que la identidad que se debe conceder obtenga acceso al secreto.
   
   > [!IMPORTANT]
   > A partir del 15 de marzo de 2021, Key Vault reconoce a Azure Application Gateway como uno de los servicios de confianza, lo que le permite crear un límite de red seguro en Azure. Esto le ofrece la posibilidad de denegar el acceso al tráfico desde todas las redes (incluido el tráfico de Internet) a Key Vault pero seguir haciendo que sea accesible para el recurso de Application Gateway en la suscripción. 

   > Puede configurar Application Gateway en una red restringida de Key Vault de la siguiente manera. <br />
   > a) En la hoja redes de Key Vault <br />
   > b) Elegir el punto de conexión privado y las redes seleccionadas en la pestaña "Firewall y redes virtuales" <br/>
   > c) Mediante el uso de redes virtuales, agregue la red virtual y la subred de la Application Gateway. Durante el proceso, configure también el punto de conexión de servicio "Microsoft. KeyVault" activando la casilla correspondiente. <br/>
   > d) Finalmente, seleccione "sí" para permitir que los servicios de confianza omitan el firewall de Key Vault. <br/>
   > 
   > ![Firewall de Key Vault](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Si implementa la puerta de enlace de aplicaciones mediante una plantilla de ARM, ya sea mediante la CLI de Azure o PowerShell o mediante una aplicación de Azure implementada desde Azure Portal, el certificado SSL se almacena en el almacén de claves como un archivo PFX con codificación base 64. Debe completar los pasos descritos en [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > Es especialmente importante establecer `enabledForTemplateDeployment` en `true`. El certificado puede ser sin contraseña o puede tener una. En el caso de un certificado con contraseña, se muestra el siguiente ejemplo como posible configuración para la entrada `sslCertificates` en `properties` para la configuración de la plantilla de ARM para una puerta de enlace de aplicaciones. Los valores de `appGatewaySSLCertificateData` y `appGatewaySSLCertificatePassword` se buscan desde el almacén de claves como se describe en la sección [Referencia a secretos con identificador dinámico](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Siga las referencias anteriores desde `parameters('secretName')` para ver cómo se produce la búsqueda. Si el certificado no tiene contraseña, omita la entrada `password`.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Configuración de la instancia de Application Gateway**

   Después de completar los dos pasos anteriores, puede configurar o modificar una puerta de enlace de aplicaciones existente para usar la identidad administrada asignada por el usuario. Para más información, consulte [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   También puede configurar el certificado TLS/SSL del cliente de escucha HTTP para que apunte al URI completo del certificado de Key Vault o al identificador de secreto.

   ![Certificados de Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Pasos siguientes

[Configuración de la terminación TLS con certificados de Key Vault mediante Azure PowerShell](configure-keyvault-ps.md)
