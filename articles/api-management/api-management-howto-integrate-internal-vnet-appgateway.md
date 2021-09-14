---
title: Uso de API Management en una red virtual con Azure Application Gateway
titleSuffix: Azure API Management
description: Configuración de Azure API Management en una red virtual interna con Application Gateway (Web Application Firewall) como front-end
services: api-management
documentationcenter: ''
author: solankisamir
ms.service: api-management
ms.topic: how-to
ms.author: sasolank
ms.date: 06/10/2021
ms.custom: devx-track-azurepowershell,contperf-fy21q4
ms.openlocfilehash: e7c4583797719ca3b9cbb47d5dead48138d22fe1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543131"
---
# <a name="integrate-api-management-in-an-internal-virtual-network-with-application-gateway"></a>Integración de API Management en una red virtual interna con Application Gateway

Puede configurar el servicio de API Management se en una [red virtual en modo interno](api-management-using-with-internal-vnet.md), para que sea accesible únicamente desde dentro de la red virtual. [Azure Application Gateway](../application-gateway/overview.md) es un servicio PAAS que actúa como un equilibrador de carga de nivel 7. Actúa como un servicio de proxy inverso y proporciona entre su oferta un firewall de aplicaciones web (WAF).

Al combinar API Management aprovisionado en una red virtual interna con el front-end de Application Gateway, puede:

* Utilizar el mismo recurso de API Management para su uso por los consumidores internos y los consumidores externos.
* Utilizar un único recurso de API Management y tener definido un subconjunto de API en API Management disponible para los consumidores externos.
* Proporcionar una solución de llave en mano para activar y desactivar el acceso a API Management desde la red pública de Internet.

> [!NOTE]
> Este artículo se ha actualizado para usar la [SKU Application Gateway WAF_v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md).

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para seguir los pasos que se describen en este artículo, debe tener:

* Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificados
     - Archivos PFX para los nombres de host personalizados del servicio de API Management: puerta de enlace, portal para desarrolladores y punto de conexión de administración. 
     - Un archivo CER para el certificado raíz de los certificados PFX. 
     
    Para obtener más información, vea [Certificados para el back-end](../application-gateway/certificates-for-backend-authentication.md). Con fines de prueba, puede generar [certificados autofirmados](../application-gateway/self-signed-certificates.md).
* La versión más reciente de Azure PowerShell. Si todavía no lo ha hecho, [instale Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="scenario"></a>Escenario

En este artículo, obtendrá información sobre cómo usar un único servicio de API Management para los consumidores tanto internos como externos y hacer que actúe como un único servidor de front-end para las API locales y en la nube. También comprenderá cómo debe exponer solamente un subconjunto de las API (resaltado en verde en el ejemplo) para permitir su consumo externo, utilizando para ello la funcionalidad de enrutamiento disponible en Application Gateway.

En el primer ejemplo de la configuración, todas las API se administran únicamente desde dentro de la red virtual. Los consumidores internos (resaltados en color naranja) pueden tener acceso a todas las API internas y externas. El tráfico nunca sale a Internet. La conectividad de alto rendimiento se proporciona mediante circuitos ExpressRoute.

![ruta de dirección URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

### <a name="what-is-required-to-integrate-api-management-and-application-gateway"></a>¿Qué se requiere para integrar API Management y Application Gateway?

* **Grupo de servidores de back-end:** se trata de la dirección IP virtual interna del servicio de API Management.
* **Configuración del grupo de servidores back-end** : cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores se aplican a todos los servidores del grupo.
* **Puerto de front-end:** es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico que llega se redirige a uno de los servidores back-end.
* **Agente de escucha:** tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado TLS/SSL (si se configura una descarga de TLS).
* **Regla:** la regla enlaza un cliente de escucha con un grupo de servidores back-end.
* **Sondeo de estado personalizado**: Application Gateway, de forma predeterminada, usa sondeos basados en direcciones IP para determinar cuáles son los servidores de BackendAddressPool que están activos. El servicio API Management responde solo a las solicitudes con el encabezado de host correcto, por lo tanto, los sondeos predeterminados no podrán completarse. Debe definir el sondeo de estado personalizado para ayudar a la puerta de enlace de aplicación a determinar que el servicio está activo y debe reenviar las solicitudes.
* **Certificados de dominio personalizado:** para tener acceso a API Management desde Internet, debe crear una asignación de CNAME entre el nombre de host y el nombre DNS del front-end de Application Gateway. Esto garantiza que API Management reconoce como válidos el encabezado de nombre de host y el certificado enviados a Application Gateway que se reenvían a API Management. En este ejemplo, usaremos tres certificados: para la puerta de enlace del servicio de API Management (el back-end), el portal para desarrolladores y el punto de conexión de administración.  

## <a name="steps-required-to-integrate-api-management-and-application-gateway"></a>Pasos necesarios para integrar API Management y Application Gateway

1. Cree un grupo de recursos para Resource Manager.
1. Cree una red virtual, una subred y una IP pública para la instancia de Application Gateway. Cree otra subred para API Management.
1. Cree un servicio de API Management en la subred de la red virtual creada en el paso anterior. Asegúrese de usar el modo interno.
1. Configure un nombre de dominio personalizado en el servicio de API Management.
1. Configuración de una zona DNS privada para la resolución DNS en la red virtual
1. Cree un objeto de configuración de Application Gateway.
1. Cree un recurso de Application Gateway.
1. Cree un CNAME del nombre DNS público de Application Gateway en el nombre de host de proxy de API Management.

### <a name="expose-the-developer-portal-and-management-endpoint-externally-through-application-gateway"></a>Exposición del portal para desarrolladores y el punto de conexión de administración externamente mediante Application Gateway

En esta guía, también se expone el **portal para desarrolladores** y el **punto de conexión de administración** a audiencias externas a través de Application Gateway. Se necesitan pasos adicionales para crear un agente de escucha, un sondeo, una configuración y reglas para cada punto de conexión. Todos los detalles se proporcionan en los pasos correspondientes.

> [!WARNING]
> Si usa Azure AD o un método de autenticación de terceros, habilite la característica de [afinidad de sesión basada en cookies](../application-gateway/features.md#session-affinity) de Application Gateway.

> [!WARNING]
> Para evitar que WAF de Application Gateway interrumpa la descarga de las especificaciones de OpenAPI en el portal para desarrolladores, debe deshabilitar la regla de firewall `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`.
> 
> Las reglas de WAF de Application Gateway, que pueden interrumpir la funcionalidad del portal, incluyen:
> 
> - `920300`, `920330`, `931130`, `942100`, `942110`, `942180`, `942200`, `942260`, `942340`, `942370` para el modo administrativo
> - `942200`, `942260`, `942370`, `942430`, `942440` para el portal publicado

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para Resource Manager

### <a name="step-1"></a>Paso 1

Inicio de sesión en Azure

```powershell
Connect-AzAccount
```

Autentíquese con sus credenciales.

### <a name="step-2"></a>Paso 2

Seleccione la suscripción deseada.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Paso 3

Cree un grupo de recursos (omita este paso si usa uno existente).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager. La red virtual de este ejemplo consta de subredes independientes para Application Gateway y API Management.

### <a name="step-1"></a>Paso 1

Cree grupos de seguridad de red y reglas de NSG para las subredes de Application Gateway y API Management.

```powershell
$appGwRule1 = New-AzNetworkSecurityRuleConfig -Name appgw-in -Description "AppGw inbound" `
    -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix `
    GatewayManager -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 65200-65535
$appGwNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APPGW" -SecurityRules $appGwRule1

$apimRule1 = New-AzNetworkSecurityRuleConfig -Name apim-in -Description "APIM inbound" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix `
    ApiManagement -SourcePortRange * -DestinationAddressPrefix VirtualNetwork -DestinationPortRange 3443
$apimNsg = New-AzNetworkSecurityGroup -ResourceGroupName $resGroupName -Location $location -Name `
    "NSG-APIM" -SecurityRules $apimRule1
```

### <a name="step-2"></a>Paso 2

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se va a usar para Application Gateway al crear la red virtual.

```powershell
$appGatewaySubnet = New-AzVirtualNetworkSubnetConfig -Name "appGatewaySubnet" -NetworkSecurityGroup $appGwNsg -AddressPrefix "10.0.0.0/24"
```

### <a name="step-3"></a>Paso 3

Asigne el intervalo de direcciones 10.0.1.0/24 a la variable de subret que se va a usar para API Management al crear la red virtual.

```powershell
$apimSubnet = New-AzVirtualNetworkSubnetConfig -Name "apimSubnet" -NetworkSecurityGroup $apimNsg -AddressPrefix "10.0.1.0/24"
```

### <a name="step-4"></a>Paso 4

Cree una red virtual llamada **appgwvnet** en el grupo de recursos **apim-appGw-RG** para la región Oeste de EE. UU. Use el prefijo 10.0.0.0/16 con las subredes 10.0.0.0/24 y 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName `
  -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appGatewaySubnet,$apimSubnet
```

### <a name="step-5"></a>Paso 5

Asigne variables de subred para los pasos siguientes.

```powershell
$appGatewaySubnetData = $vnet.Subnets[0]
$apimSubnetData = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-virtual-network-configured-in-internal-mode"></a>Cree un servicio de API Management dentro de una red virtual configurada en modo interno.

En el ejemplo siguiente se muestra cómo crear un servicio de API Management en una red virtual configurada únicamente para el acceso interno.

### <a name="step-1"></a>Paso 1

Cree un objeto de red virtual de API Management con la subred `$apimSubnetData` creada anteriormente.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimSubnetData.Id
```

### <a name="step-2"></a>Paso 2

Cree un servicio de API Management dentro de la red virtual. En este ejemplo, se crea el servicio en el nivel de servicio Desarrollador. Sustituya un nombre único por el servicio de API Management.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name, must be globally unique
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Normalmente, se tarda entre 30 y 40 minutos en crear y activar una instancia del servicio API Management en este nivel. Después de que el comando anterior se ejecute correctamente, consulte [Configuración de DNS requerida para acceder al servicio de API Management de red virtual interna](api-management-using-with-internal-vnet.md#dns-configuration) para confirmar el acceso. 

## <a name="set-up-custom-domain-names-in-api-management"></a>Configuración de nombres de dominio personalizados en API Management

### <a name="step-1"></a>Paso 1

Inicialice las variables siguientes con los detalles de los certificados con claves privadas para los dominios y el certificado raíz de confianza. En este ejemplo, usamos `api.contoso.net`, `portal.contoso.net` y `management.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$managementHostname = "management.contoso.net"               # API management endpoint host
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$managementCertPfxPath = "C:\Users\Contoso\management.pfx"   # full path to management.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate
$managementCertPfxPassword = "certificatePassword123"    # password for management.contoso.net pfx certificate
# Path to trusted root CER file used in Application Gateway HTTP settings
$trustedRootCertCerPath = "C:\Users\Contoso\trustedroot.cer" # full path to contoso.net trusted root .cer file

$certGatewayPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
$certManagementPwd = ConvertTo-SecureString -String $managementCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Paso 2

Cree y establezca los objetos de configuración del nombre de host para los puntos de conexión de API Management.  

```powershell
$gatewayHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname `
  -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certGatewayPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname `
  -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd
$managementHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $managementHostname `
  -HostnameType Management -PfxPath $managementCertPfxPath -PfxPassword $certManagementPwd

$apimService.ProxyCustomHostnameConfiguration = $gatewayHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
$apimService.ManagementCustomHostnameConfiguration = $managementHostnameConfig

Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Para configurar la conectividad del portal para desarrolladores heredado, debe reemplazar `-HostnameType DeveloperPortal` por `-HostnameType Portal`.

## <a name="configure-a-private-zone-for-dns-resolution-in-the-virtual-network"></a>Configuración de una zona privada para la resolución DNS en la red virtual

### <a name="step-1"></a>Paso 1

Cree una zona DNS privada y vincule la red virtual.

```powershell
$myZone = New-AzPrivateDnsZone -Name "contoso.net" -ResourceGroupName $resGroupName 
$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Name "mylink" `
  -VirtualNetworkId $vnet.id
```

### <a name="step-2"></a>Paso 2

Cree registros A para los nombres de host de dominio personalizado al asignarlos a la dirección IP privada del servicio API Management:

```powershell
$apimIP = $apimService.PrivateIPAddresses[0]

New-AzPrivateDnsRecordSet -Name api -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name portal -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
New-AzPrivateDnsRecordSet -Name management -RecordType A -ZoneName contoso.net `
  -ResourceGroupName $resGroupName -Ttl 3600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $apimIP)
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso de IP pública estándar **publicIP01** en el grupo de recursos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName `
  -name "publicIP01" -location $location -AllocationMethod Static -Sku Standard
```

Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Creación de una configuración de puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia Application Gateway, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appGatewaySubnetData
```

### <a name="step-2"></a>Paso 2

Configure el puerto IP de front-end para el punto de conexión de IP pública. Este puerto es el puerto al que se conectan los usuarios finales.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Paso 3

Configuración de la dirección IP de front-end con el punto de conexión de IP pública

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Paso 4

Configure el certificado para Application Gateway, que se usará para descifrar y volver a cifrar el tráfico que pasa por ella.

> [!NOTE]
> Application Gateway permite definir opciones de TLS personalizadas, deshabilitar determinadas versiones del protocolo TLS y especificar conjuntos de cifrado y el orden de preferencia. Para más información sobre las opciones configurables de TLS, consulte [Introducción a la directiva TLS](../application-gateway/application-gateway-ssl-policy-overview.md).

```powershell
$certGateway = New-AzApplicationGatewaySslCertificate -Name "gatewaycert" `
  -CertificateFile $gatewayCertPfxPath -Password $certGatewayPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "portalcert" `
  -CertificateFile $portalCertPfxPath -Password $certPortalPwd
$certManagement = New-AzApplicationGatewaySslCertificate -Name "managementcert" `
  -CertificateFile $managementCertPfxPath -Password $certManagementPwd
```

### <a name="step-5"></a>Paso 5

Cree los agentes de escucha HTTP para Application Gateway. Asígneles la configuración IP de front-end, el puerto y los certificados TSL/SSL que se usarán.

```powershell
$gatewayListener = New-AzApplicationGatewayHttpListener -Name "gatewaylistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certGateway -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "portallistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
$managementListener = New-AzApplicationGatewayHttpListener -Name "managementlistener" `
  -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 `
  -SslCertificate $certManagement -HostName $managementHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Paso 6

Cree sondeos personalizados para el punto de conexión de dominio de la puerta de enlace `ContosoApi` del servicio de API Management. La ruta de acceso `/status-0123456789abcdef` es un punto de conexión de mantenimiento predeterminado hospedado en todos los servicios de API Management. Establezca `api.contoso.net` como un nombre de host de sondeo personalizado para protegerlo con el certificado TLS/SSL.

> [!NOTE]
> El nombre de host `contosoapi.azure-api.net` es el nombre de host de proxy predeterminado configurado cuando se crea un servicio denominado `contosoapi` en el ámbito público de Azure.
>

```powershell
$apimGatewayProbe = New-AzApplicationGatewayProbeConfig -Name "apimgatewayprobe" `
  -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" `
  -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" `
  -Protocol "Https" -HostName $portalHostname -Path "/signin" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
$apimManagementProbe = New-AzApplicationGatewayProbeConfig -Name "apimmanagementprobe" `
  -Protocol "Https" -HostName $managementHostname -Path "/ServiceStatus" `
  -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Paso 7

Cargue el certificado raíz de confianza que se va a configurar en la configuración HTTP.

```powershell
$trustedRootCert = New-AzApplicationGatewayTrustedRootCertificate -Name "whitelistcert1" -CertificateFile $trustedRootCertCerPath
```

### <a name="step-8"></a>Paso 8

Establezca la configuración de back-end HTTP para Application Gateway, incluido un límite de tiempo de espera para las solicitudes de back-end, después del cual se cancelarán. Este valor es distinto del tiempo de espera del sondeo.

```powershell
$apimPoolGatewaySetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolGatewaySetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimGatewayProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
$apimPoolManagementSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolManagementSetting" `
  -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimManagementProbe `
  -TrustedRootCertificate $trustedRootCert -PickHostNameFromBackendAddress -RequestTimeout 180
```

### <a name="step-9"></a>Paso 9:

Configure un grupo de direcciones IP de back-end para cada punto de conexión de API Management, con su nombre de dominio respectivo.

```powershell
$apimGatewayBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "gatewaybackend" `
  -BackendFqdns $gatewayHostname
$apimPortalBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "portalbackend" `
  -BackendFqdns $portalHostname
$apimManagementBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "managementbackend" `
  -BackendFqdns $managementHostname
```

### <a name="step-10"></a>Paso 10

Cree reglas para Application Gateway para usar el enrutamiento básico.

```powershell
$gatewayRule = New-AzApplicationGatewayRequestRoutingRule -Name "gatewayrule" `
  -RuleType Basic -HttpListener $gatewayListener -BackendAddressPool $apimGatewayBackendPool `
  -BackendHttpSettings $apimPoolGatewaySetting
$portalRule = New-AzApplicationGatewayRequestRoutingRule -Name "portalrule" `
  -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimPortalBackendPool `
  -BackendHttpSettings $apimPoolPortalSetting
$managementRule = New-AzApplicationGatewayRequestRoutingRule -Name "managementrule" `
  -RuleType Basic -HttpListener $managementListener -BackendAddressPool $apimManagementBackendPool `
  -BackendHttpSettings $apimPoolManagementSetting
```

> [!TIP]
> Cambie `-RuleType` y el enrutamiento para restringir el acceso a determinadas páginas del portal para desarrolladores.

### <a name="step-11"></a>Paso 11

Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones En este ejemplo, usamos la [SKU WAF_v2](../web-application-firewall/ag/ag-overview.md) para aumentar la seguridad del recurso de API Management.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_v2" -Tier "WAF_v2" -Capacity 2
```

### <a name="step-12"></a>Paso 12

Configuración de WAFS para que esté en modo "Prevención".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Cree una instancia de Application Gateway con todos los objetos de configuración de los pasos anteriores.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location `
  -BackendAddressPools $apimGatewayBackendPool,$apimPortalBackendPool,$apimManagementBackendPool `
  -BackendHttpSettingsCollection $apimPoolGatewaySetting, $apimPoolPortalSetting, $apimPoolManagementSetting `
  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 `
  -HttpListeners $gatewayListener,$portalListener,$managementListener `
  -RequestRoutingRules $gatewayRule,$portalRule,$managementRule `
  -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $certGateway,$certPortal,$certManagement `
  -TrustedRootCertificate $trustedRootCert -Probes $apimGatewayProbe,$apimPortalProbe,$apimManagementProbe
```

Una vez completada la implementación de Application Gateway, confirme el estado de mantenimiento de los back-end de API Management en el portal o ejecute el siguiente comando:

```powershell
Get-AzApplicationGatewayBackendHealth -Name $appgwName -ResourceGroupName $resGroupName
```

Asegúrese de que el estado de mantenimiento de cada grupo de back-end es Correcto. Si necesita solucionar problemas de un back-end incorrecto o un back-end con un estado de mantenimiento desconocido, consulte [Solución de problemas de mantenimiento de back-end en Application Gateway](../application-gateway/application-gateway-backend-health-troubleshooting.md).

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Aplicación de un CNAME al nombre de host del proxy de API Management para el nombre DNS público del recurso de Application Gateway

Una vez creada la puerta de enlace, configure el front-end para la comunicación. Cuando se utiliza una dirección IP pública, Application Gateway requiere un nombre DNS asignado dinámicamente, que puede no ser fácil de usar.

Use el nombre DNS de Application Gateway para crear un registro CNAME y haga que el nombre de host de puerta de enlace de API Management (`api.contoso.net` en los ejemplos anteriores) apunte a este nombre DNS. Para configurar el registro IP CNAME de front-end, recupere los detalles de Application Gateway y su nombre DNS o IP asociados mediante el elemento `PublicIPAddress`. No se recomienda usar registros A, ya que la dirección VIP puede cambiar cuando se reinicia la puerta de enlace.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

Con fines de prueba, puede actualizar el archivo de hosts en la máquina local con entradas que asignen la dirección IP pública de Application Gateway a cada uno de los nombres de host del punto de conexión de API Management que configuró (por ejemplo, `api.contoso.net`, `portal.contoso.net`, `management.contoso.net`).

## <a name="summary"></a>Resumen

El servicio Azure API Management configurado en una red virtual proporciona una interfaz de puerta de enlace única para todas las API configuradas, independientemente de si están hospedadas de forma local o en la nube. La integración de Application Gateway con API Management proporciona la flexibilidad de habilitar de manera selectiva API determinadas para que estén accesibles en Internet, así de proporcionar un firewall de aplicaciones web como front-end para la instancia de API Management.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre Azure Application Gateway.
  * [Introducción a Puerta de enlace de aplicaciones](../application-gateway/overview.md)
  * [Firewall de aplicaciones web de Application Gateway](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway mediante enrutamiento basado en rutas de acceso](../application-gateway/tutorial-url-route-powershell.md)
* Más información acerca de API Management y Virtual Network
  * [Uso de API Management con la red virtual interna](api-management-using-with-internal-vnet.md)
  * [Cómo usar API Management con redes virtuales](api-management-using-with-vnet.md)
