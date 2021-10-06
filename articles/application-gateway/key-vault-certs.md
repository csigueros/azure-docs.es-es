---
title: Terminación TLS con certificados de Azure Key Vault
description: Obtenga información sobre cómo puede integrar Azure Application Gateway con Key Vault para certificados de servidor que se adjuntan a los clientes de escucha con HTTPS habilitado.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 55ad3c97fd70db854faf3d2181a55630d04f5e41
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215099"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminación TLS con certificados de Key Vault

[Azure Key Vault](../key-vault/general/overview.md) es un almacén de secretos administrado por la plataforma que puede usar para proteger los secretos, las claves y los certificados TLS/SSL. Azure Application Gateway admite la integración con Key Vault para certificados de servidor adjuntos a clientes de escucha con HTTPS habilitado. Esta compatibilidad está limitada a la versión 2 de la SKU de Application Gateway.

La integración de Key Vault ofrece dos modelos para la terminación TLS:

- Proporcione explícitamente los certificados TLS/SSL asociados al cliente de escucha. Este modelo es la forma tradicional para pasar los certificados TLS/SSL a Application Gateway para la terminación TLS.
- Opcionalmente, proporcione una referencia a un certificado de Key Vault existente o un secreto al crear un cliente de escucha habilitado para HTTPS.

La integración de Application Gateway con Key Vault ofrece muchas ventajas, incluidas las siguientes:

- Mayor seguridad, dado que el equipo de desarrollo de aplicaciones no controla directamente los certificados TLS/SSL. La integración permite que un equipo de seguridad independiente haga lo siguiente:
  * Configurar instancias de Application Gateway.
  * Controlar los ciclos de vida de las instancias de Application Gateway.
  * Conceder permisos a las instancias de Application Gateway seleccionadas para tener acceso a los certificados almacenados en el almacén de claves.
- Compatibilidad con la importación de certificados existentes en el almacén de claves. También puede usar las API de Key Vault para crear y administrar certificados nuevos con cualquiera de los asociados de Key Vault de confianza.
- Compatibilidad con la renovación automática de certificados almacenados en el almacén de claves.

## <a name="supported-certificates"></a>Certificados admitidos

Actualmente, Application Gateway solo admite certificados validados por software. Los certificados validados por el módulo de seguridad de hardware (HSM) no se admiten. 

Después de configurar Application Gateway para usar certificados de Key Vault, sus instancias recuperan el certificado de Key Vault y lo instalan localmente para la terminación TLS. Además, las instancias sondean Key Vault en intervalos de cuatro horas para recuperar una versión renovada del certificado, si existe. Si se encuentra un certificado actualizado, el certificado TLS/SSL asociado actualmente al cliente de escucha HTTPS rota automáticamente. 

Application Gateway usa un identificador secreto en Key Vault para hacer referencia a los certificados. Para Azure PowerShell, la CLI de Azure o Azure Resource Manager, se recomienda encarecidamente usar un identificador secreto que no especifique una versión. De este modo, Application Gateway rotará automáticamente el certificado si hay una versión más reciente disponible en el almacén de claves. Un ejemplo de un URI de secreto sin una versión es `https://myvault.vault.azure.net/secrets/mysecret/`.

Azure Portal solo admite certificados de Key Vault, no secretos. Application Gateway todavía admite la referencia a secretos de Key Vault, pero solo a través de recursos que no son del portal, como PowerShell, la CLI de Azure, las API y las plantillas de Azure Resource Manager (plantillas de ARM). 

> [!WARNING]
> Azure Application Gateway actualmente solo admite cuentas de Key Vault de la misma suscripción que el recurso de Application Gateway. Si elige un almacén de claves de una suscripción que no es la suya de Application Gateway, se producirá un error.

## <a name="certificate-settings-in-key-vault"></a>Configuración del certificado en Key Vault

Para la terminación TLS, Application Gateway admite certificados en formato Personal Information Exchange (PFX). Puede importar un certificado existente o crear uno nuevo en el almacén de claves. Para evitar errores, asegúrese de que el estado del certificado está establecido en **Habilitado** en Key Vault.

## <a name="how-integration-works"></a>Funcionamiento de la integración

La integración de Application Gateway con Key Vault es un proceso de configuración de tres pasos:

![Diagrama que muestra tres pasos para integrar Application Gateway con Key Vault.](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Puede crear una identidad administrada asignada por el usuario, o bien puede reutilizar una existente. Application Gateway usa la identidad administrada para recuperar certificados de Key Vault en su nombre. Para obtener más información, consulte [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Con este paso se crea una nueva identidad en el inquilino de Azure Active Directory. La identidad es de confianza para la suscripción que se usa para crear la identidad.

### <a name="configure-your-key-vault"></a>Configuración del almacén de claves

Defina directivas de acceso para usar la identidad administrada asignada por el usuario con el almacén de claves:
    
1. En Azure Portal, vaya a **Key Vault**.
1. Abra el panel **Directivas de acceso**.
1. Si usa el modelo de permisos **Directiva de acceso del almacén**: seleccione **+ Agregar directiva de acceso**, seleccione **Obtener** para **Permisos de secretos** y elija la identidad administrada asignada por el usuario para **Seleccionar la entidad de seguridad**. Después, seleccione **Guardar**.
   
   Si usa el modelo de permisos **Control de acceso basado en roles de Azure**: agregue una asignación de roles para la identidad administrada asignada por el usuario a Azure Key Vault para el rol **Key Vault Secrets User** (Usuario de secretos de Key Vault).

A partir del 15 de marzo de 2021, Key Vault reconoce Application Gateway como un servicio de confianza y aprovecha las identidades administradas por el usuario para la autenticación en Azure Key Vault.  Con el uso de puntos de conexión de servicio y la habilitación de la opción de servicios de confianza para el firewall del almacén de claves, puede crear un límite de red seguro en Azure. Puede denegar el acceso al tráfico desde todas las redes (incluido el tráfico de Internet) a Key Vault, pero seguir haciendo que sea accesible para el recurso de Application Gateway en la suscripción.

Cuando use un almacén de claves restringido, siga estos pasos para configurar Application Gateway para usar firewalls y redes virtuales: 

1. En Azure Portal, en el almacén de claves, seleccione **Redes**.
1. En la pestaña **Firewalls y redes virtuales**, seleccione **Punto de conexión privado y redes seleccionadas**.
1. En **Redes virtuales**, seleccione **Add existing virtual networks** (Agregar redes virtuales existentes) y luego agregue la red virtual y la subred para la instancia de Application Gateway. Durante el proceso, configure también el punto de conexión de servicio `Microsoft.KeyVault` seleccionando su casilla.
1. Seleccione **Sí** para permitir que los servicios de confianza omitan el firewall del almacén de claves.
  
![Captura de pantalla que muestra las selecciones para configurar Application Gateway para usar firewalls y redes virtuales.](media/key-vault-certs/key-vault-firewall.png)

Si implementa la instancia de Application Gateway mediante una plantilla de ARM, ya sea mediante la CLI de Azure o PowerShell o mediante una aplicación de Azure implementada desde Azure Portal, el certificado SSL se almacena en el almacén de claves como un archivo PFX con codificación base 64. Debe completar los pasos descritos en [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../azure-resource-manager/templates/key-vault-parameter.md). 

Es especialmente importante establecer `enabledForTemplateDeployment` en `true`. Es posible que el certificado tenga una contraseña o no. En el caso de un certificado con contraseña, se muestra el siguiente ejemplo como posible configuración para la entrada `sslCertificates` en `properties` para la configuración de la plantilla de ARM para Application Gateway. 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

Los valores de `appGatewaySSLCertificateData` y `appGatewaySSLCertificatePassword` se buscan desde el almacén de claves, como se describe en [Referencia a secretos con identificador dinámico](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Siga las referencias anteriores desde `parameters('secretName')` para ver cómo se produce la búsqueda. Si el certificado no tiene contraseña, omita la entrada `password`.

### <a name="configure-application-gateway"></a>Configuración de Application Gateway

Después de crear una identidad administrada asignada por el usuario y configurar el almacén de claves, puede asignar la identidad administrada para la instancia de Application Gateway a través de la Administración de identidad y acceso (IAM). Para PowerShell, consulte [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

## <a name="investigating-and-resolving-key-vault-errors"></a>Investigación y resolución de errores de Key Vault

Azure Application Gateway solo sondea la versión del certificado renovado en Key Vault cada intervalo de cuatro horas. También registra cualquier error y se integra con Azure Advisor para que se presenten errores de configuración como recomendación. La recomendación contiene detalles sobre el problema y el recurso Key Vault asociado. Puede usar esta información junto con la [guía de solución de problemas](../application-gateway/application-gateway-key-vault-common-errors.md) para resolver rápidamente este tipo de error de configuración. 

Se recomienda encarecidamente [configurar las alertas de Advisor](../advisor/advisor-alerts-portal.md) para que permanezcan actualizadas cuando se detecte un problema. Para establecer una alerta para este caso específico, use **Resolución del problema de Azure Key Vault para su Application Gateway** como tipo de recomendación. 

## <a name="next-steps"></a>Pasos siguientes

[Configuración de la terminación TLS con certificados de Key Vault mediante Azure PowerShell](configure-keyvault-ps.md)
