---
title: 'Migración de la versión v1 a la v2: Azure Application Gateway'
description: En este artículo se explica cómo realizar la migración de Azure Application Gateway y Firewall de aplicaciones web de v1 a v2.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 000daf7c60d0bc823aacdab85de42af3b6cbbf55
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419057"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migración de Azure Application Gateway y Firewall de aplicaciones web de v1 a v2

Ya están disponibles [Azure Application Gateway y Firewall de aplicaciones web (WAF) v2](application-gateway-autoscaling-zone-redundant.md), con características adicionales como el escalado automático y la redundancia de la zona de disponibilidad. Aun así, las puertas de enlace v1 existentes no se actualizan automáticamente a v2. Si quiere migrar de v1 a v2, siga los pasos que se describen en este artículo.

Hay dos fases en una migración:

1. Migración de la configuración
2. Migración del tráfico de cliente

Este artículo trata sobre la migración de la configuración. La migración del tráfico de cliente varía en función del entorno, pero [se proporcionan](#migrate-client-traffic) algunas recomendaciones generales de alto nivel.

## <a name="migration-overview"></a>Información general sobre la migración

Existe un script de Azure PowerShell que hace lo siguiente:

* Crea una puerta de enlace Standard_v2 o WAF_v2 en la subred de red virtual que especifique.
* Copia sin problemas la configuración asociada con la puerta de enlace v1 Standard o WAF en la puerta de enlace Standard_V2 o WAF_V2 recién creada.

### <a name="caveatslimitations"></a>Advertencias y limitaciones

* La nueva puerta de enlace v2 tiene nuevas direcciones IP públicas y privadas. Es complicado mover las direcciones IP asociadas con la puerta de enlace v1 existente a la v2, pero puede asignar una dirección IP pública o privada existente (sin asignar) a la nueva puerta de enlace v2.
* Debe proporcionar un espacio de direcciones IP para otra subred dentro de la red virtual en la que está ubicada la puerta de enlace v1. El script no puede crear la puerta de enlace v2 en las subredes existentes que ya tengan una puerta de enlace v1. Aun así, si la subred existente ya tiene una puerta de enlace v2, esto podría funcionar siempre y cuando haya suficiente espacio de direcciones IP.
* Si tiene un grupo de seguridad de red o rutas definidas por el usuario asociadas a la subred de puerta de enlace v2, asegúrese de que se ajusta a los [requisitos de grupo de seguridad de red](../application-gateway/configuration-infrastructure.md#network-security-groups) y a los [requisitos de UDR](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) para que la migración se realice correctamente.
* Actualmente, no se admiten [directivas de punto de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) en una subred de Application Gateway.
* Para migrar una configuración de TLS/SSL, debe especificar todos los certificados TLS/SSL que se usan en la puerta de enlace v1.
* Si tiene el modo FIPS habilitado para la puerta de enlace v1, no se migrará a la nueva puerta de enlace v2. El modo FIPS no se admite en v2.
* v2 no es compatible con IPv6, por lo que las puertas de enlace v1 con IPv6 habilitado no se pueden migrar. Si ejecuta el script, podría no completarse.
* Si la puerta de enlace v1 solo tiene una dirección IP privada, el script crea una dirección IP pública y una dirección IP privada para la nueva puerta de enlace v2. Las puertas de enlace v2 actualmente no admiten solo direcciones IP privadas.
* Los encabezados con nombres que contienen caracteres que no son letras, dígitos, guiones y caracteres de subrayado no se pasan a la aplicación. Esto solo se aplica a los nombres de encabezado, no a los valores de encabezado. Este es un cambio importante de la versión v1.

## <a name="download-the-script"></a>Descarga del script

Descargue el script de migración de la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Uso del script

Dispone de dos opciones en función de sus preferencias y de la configuración del entorno de PowerShell local:

* Si no tiene instalados los módulos de Azure Az, o si no le importa desinstalarlos, la mejor alternativa es usar la opción `Install-Script` para ejecutar el script.
* Si necesita conservar los módulos de Azure Az, lo mejor es que descargue el script y lo ejecute directamente.

Para determinar si tiene instalados los módulos de Azure Az, ejecute `Get-InstalledModule -Name az`. Si no ve ningún módulo de Az instalado, puede usar el método `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Instalación con el método Install-Script

Para usar esta opción, los módulos de Azure Az no deben estar instalados en el equipo. En caso de que lo estén, el comando siguiente mostrará un error. Puede desinstalar los módulos de Azure Az o usar la otra opción para descargar manualmente el script y ejecutarlo.
  
Ejecute el script con el siguiente comando para obtener la última versión:

`Install-Script -Name AzureAppGWMigration -Force`

Este comando también instala los módulos de Az necesarios.  

### <a name="install-using-the-script-directly"></a>Instalación directamente con el script

Si tiene instalados algunos módulos de Azure Az y no puede desinstalarlos (o no le interesa hacerlo), puede descargar manualmente el script mediante la pestaña **Descarga manual** en el vínculo de descarga del script. El script se descarga como un archivo nupkg sin procesar. Para instalar el script desde este archivo nupkg, consulte [Descarga manual del paquete](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para ejecutar el script:

1. Use `Connect-AzAccount` para conectarse a Azure.

1. Use `Import-Module Az` para importar los módulos de Az.

1. Ejecute `Get-Help AzureAppGWMigration.ps1` para examinar los parámetros obligatorios:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -AppGwResourceGroupName <resource group name you want to use>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parámetros del script:
   * **resourceId: [cadena]: obligatorio**. Este es el identificador de recurso de Azure de la puerta de enlace Standard v1 WAF v1 existente. Para buscar este valor de cadena, vaya a Azure Portal, seleccione el recurso de puerta de enlace o WAF de la aplicación y haga clic en el vínculo **Propiedades** de la puerta de enlace. El identificador de recurso se encuentra en esa página.

     También puede ejecutar los siguientes comandos de Azure PowerShell para obtener el identificador de recurso:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [cadena]:  obligatorio**. Se trata el espacio de direcciones IP que ha asignado (o quiere asignar) a la nueva subred que contiene la nueva puerta de enlace v2. Debe especificarse en la notación CIDR. Por ejemplo: 10.0.0.0/24. No es necesario crear esta subred de antemano, pero el CIDR debe formar parte del espacio de direcciones de la red virtual. El script lo crea automáticamente si no existe y, si existe, usará el existente (asegúrese de que la subred esté vacía, solo contenga la puerta de enlace v2 si existe y tenga suficientes direcciones IP disponibles).
   * **appgwName: [cadena]: opcional**. Se trata de una cadena que se especifica para su uso como nombre de la nueva puerta de enlace Standard_v2 o WAF_v2. Si no se proporciona este parámetro, se usará el nombre de la puerta de enlace v1 existente con el sufijo *_v2* anexado.
   * **AppGwResourceGroupName: [String]: opcional**. Nombre del grupo de recursos en el que desea que se creen los recursos de Application Gateway v2 (el valor predeterminado será `<v1-app-gw-rgname>`)
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: opcional**.  La lista separada por comas de objetos PSApplicationGatewaySslCertificate que cree para representar los certificados TLS/SSL de la puerta de enlace v1 debe cargarse en la nueva puerta de enlace v2. Para cada uno de los certificados TLS/SSL configurados para la puerta de enlace Standard v1 o WAF v1, puede crear un objeto PSApplicationGatewaySslCertificate con el comando `New-AzApplicationGatewaySslCertificate` que se muestra aquí. Necesita la ruta de acceso del archivo del certificado TLS/SSL y la contraseña.

     Este parámetro solo es opcional si no tiene agentes de escucha HTTPS configurados para la puerta de enlace v1 o WAF. Si tiene al menos un programa de instalación del agente de escucha HTTPS, debe especificar este parámetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Puede pasar `$mySslCert1, $mySslCert2` (separados por comas) en el ejemplo anterior como valores para este parámetro en el script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: opcional**. Se trata de una lista de objetos PSApplicationGatewayTrustedRootCertificate separados por comas que se crea para representar los [certificados raíz de confianza](ssl-overview.md) para la autenticación de las instancias de back-end de la puerta de enlace v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Para crear una lista de objetos PSApplicationGatewayTrustedRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **privateIpAddress: [cadena]: opcional**. Dirección IP privada específica que quiere asociar a la nueva puerta de enlace v2.  Debe ser de la misma red virtual que asigne a la nueva puerta de enlace v2. Si esto no se especifica, el script asigna una dirección IP privada para la puerta de enlace v2.
   * **publicIpResourceId: [cadena]: opcional**. Identificador de recurso de una dirección IP pública existente (SKU estándar) en la suscripción que quiere asignar a la nueva puerta de enlace v2. Si esto no se especifica, el script asigna una nueva dirección IP pública en el mismo grupo de recursos. El nombre es el mismo de la puerta de enlace v2 con *-IP* anexado.
   * **validateMigration: [modificador]: opcional**. Use este parámetro si quiere que el script realice algunas validaciones de comparación de la configuración básica tras la creación de la puerta de enlace v2 y la copia de la configuración. De forma predeterminada, no se realiza ninguna validación.
   * **enableAutoScale: [modificador]: opcional**. Use este parámetro si quiere que el script habilite el escalado automático en la nueva puerta de enlace v2 después de crearla. De forma predeterminada, el escalado automático está deshabilitado. Puede habilitarlo manualmente más adelante en la puerta de enlace v2 recién creada.

1. Ejecute el script con los parámetros adecuados. Podría tardar entre cinco y siete minutos en finalizar.

    **Ejemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -AppGwResourceGroupName "MyResourceGroup" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migración del tráfico de cliente

En primer lugar, asegúrese de que el script ha creado correctamente una puerta de enlace v2 con la configuración exacta migrada a través de la puerta de enlace v1. Puede comprobarlo desde Azure Portal.

Como prueba manual, envíe una pequeña cantidad de tráfico a través de la puerta de enlace v2.
  
A continuación se indican algunos escenarios en los que la puerta de enlace de aplicaciones actual (Standard) puede recibir tráfico de cliente y nuestras recomendaciones en cada caso:

* **Zona DNS personalizada (por ejemplo, contoso.com) que apunta a la dirección IP de front-end (mediante un registro A) asociada a la puerta de enlace Standard v1 o WAF v1**.

    Puede actualizar el registro DNS para que apunte a la etiqueta DNS o IP de front-end asociada a la puerta de enlace de aplicaciones Standard_v2. Según el TTL configurado en el registro DNS, el proceso de migrar todo el tráfico de cliente a la nueva puerta de enlace v2 podría tardar unos minutos.
* **Zona DNS personalizada (por ejemplo, contoso.com) que apunta a la etiqueta DNS (por ejemplo: *myappgw.eastus.cloudapp.azure.com* con un registro CNAME) asociada a la puerta de enlace v1**.

   Tiene dos opciones:

  * Si usa direcciones IP públicas en la puerta de enlace de aplicaciones, puede llevar a cabo una migración pormenorizada controlada mediante un perfil de Traffic Manager para enrutar el tráfico de forma incremental (método de enrutamiento de tráfico ponderado) a la nueva puerta de enlace v2.

    Para hacerlo, agregue las etiquetas DNS de las puertas de enlace de aplicaciones v1 y v2 al [perfil de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method) y asigne el registro CNAME del DNS personalizado (por ejemplo, `www.contoso.com`) en el dominio Traffic Manager (por ejemplo, contoso.trafficmanager.net).
  * También puede actualizar el registro DNS del dominio personalizado para que apunte a la etiqueta DNS de la nueva puerta de enlace de aplicaciones v2. Según el TTL configurado en el registro DNS, el proceso de migrar todo el tráfico de cliente a la nueva puerta de enlace v2 podría tardar unos minutos.
* **Los clientes se conectan a la dirección IP de front-end de la puerta de enlace de aplicaciones**.

   Actualice los clientes para que usen las direcciones IP asociadas a la puerta de enlace de aplicaciones v2 recién creada. Se recomienda que no use direcciones IP directamente. Considere la posibilidad de usar la etiqueta de nombre DNS (por ejemplo, yourgateway.eastus.cloudapp.azure.com) asociada a la puerta de enlace de aplicaciones cuyo registro CNAME pueda asignar a su propia zona DNS personalizada (por ejemplo, contoso.com).

## <a name="common-questions"></a>Preguntas frecuentes

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>¿Hay alguna limitación en el script de Azure PowerShell para migrar la configuración de v1 a v2?

Sí. Consulte [Advertencias y limitaciones](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>¿Son también aplicables este artículo y el script de Azure PowerShell al producto WAF de Application Gateway? 

Sí.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>¿Puede el script de Azure PowerShell cambiar el tráfico de mi puerta de enlace v1 a la puerta de enlace v2 recién creada?

No. El script de Azure PowerShell solo migra la configuración. Usted es el responsable de realizar y controlar la migración real del tráfico.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>¿Es el tamaño de la nueva puerta de enlace v2 creada por el script de Azure PowerShell adecuado para controlar todo el tráfico que actualmente atiende mi puerta de enlace v1?

El script de Azure PowerShell crea una puerta de enlace v2 con un tamaño adecuado para controlar el tráfico de la puerta de enlace v1 existente. El escalado automático está deshabilitado de forma predeterminada, pero se puede habilitar al ejecutar el script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>He configurado mi puerta de enlace v1 para que envíe registros a Azure Storage. ¿Replica también el script esta configuración para v2?

No. El script no replica esta configuración para v2. Debe agregar la configuración de registros por separado a la puerta de enlace v2 migrada.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>¿Admite este script los certificados cargados en Azure Key Vault?

No. Actualmente, el script no admite los certificados de Key Vault, pero se está valorando la posibilidad de que lo haga en una versión futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Se han producido algunos problemas al usar este script. ¿Cómo puedo obtener ayuda?
  
Puede ponerse en contacto con el Soporte técnico de Azure en el tema "Configuración y migración a la SKU V2". Obtenga más información sobre el [Soporte técnico de Azure aquí](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Pasos siguientes

[Información sobre Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
