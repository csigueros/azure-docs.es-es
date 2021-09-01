---
title: Azure VPN Gateway en Azure Australia
description: Implementación de VPN Gateway en Azure Australia para cumplir lo dispuesto en el ISM y proteger eficazmente las agencias gubernamentales australianas
author: emilyre
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0221f28df5ddf53df0aad298cd6692c279b5fb75
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2021
ms.locfileid: "117029041"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure VPN Gateway en Azure Australia

Un servicio crítico con cualquier nube pública es la conexión segura de los recursos y servicios en la nube a los sistemas locales existentes. El servicio que proporciona esta funcionalidad en Azure es Azure VPN Gateway. En este artículo, se describen los principales puntos que deben tenerse en cuenta al configurar una puerta de enlace de VPN para cumplir los [controles del manual de seguridad de la información (ISM)](https://acsc.gov.au/infosec/ism/) de Australian Signals Directorate (ASD).

Las puertas de enlace de VPN se usan para enviar tráfico cifrado entre una red virtual de Azure y otra red. Las redes virtuales de VPN se pueden enfrentar a tres posibles escenarios:

- Sitio a sitio (S2S)
- Punto a sitio (P2S)
- De red a red

Este artículo se centra en las puertas de enlace de VPN S2S. En el diagrama 1 se muestra un ejemplo de configuración de una puerta de enlace de VPN S2S.

![Puerta de enlace de VPN con conexiones a varios sitios](media/vpngateway-multisite-connection-diagram.png)

*Diagrama 1: puerta de enlace de VPN S2S de Azure*

## <a name="key-design-considerations"></a>Consideraciones clave sobre el diseño

Hay tres opciones de red para conectar Azure a los clientes gubernamentales australianos:

- ICON
- Azure ExpressRoute
- Internet público

La [guía del consumidor para Azure](https://servicetrust.microsoft.com/viewpage/Australia) del Centro Australiano de Seguridad Cibernética recomienda que VPN Gateway (o un servicio de terceros certificado PROTEGIDO equivalente) se use junto con las tres opciones de redes. Esta recomendación es asegurarse de que las conexiones cumplen los controles de ISM en cuanto a cifrado e integridad.

### <a name="encryption-and-integrity"></a>Cifrado e integridad

De forma predeterminada, la VPN negocia los parámetros y algoritmos de cifrado e integridad durante el establecimiento de la conexión como parte de los protocolos de enlace de IKE. Durante el protocolo de enlace de IKE, la configuración y el orden de preferencia dependen de si la puerta de enlace de VPN es el iniciador o el respondedor. Esta designación se controla mediante el dispositivo VPN. La configuración final de la conexión se controla mediante la configuración del dispositivo VPN. Para más información acerca de los dispositivos VPN validados y su configuración, consulte [Acerca de los servicios VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Las puertas de enlace de VPN pueden controlar el cifrado y la integridad mediante la configuración de una directiva de IPsec o IKE personalizada en la conexión.

### <a name="resource-operations"></a>Operaciones de recursos

Las puertas de enlace de VPN crean una conexión entre los entornos de Azure y los que no son de Azure a través de la red pública de Internet. El ISM tiene controles relacionados con la autorización explícita de las conexiones. De forma predeterminada, es posible usar las puertas de enlace de VPN para crear túneles no autorizados en entornos seguros. Es fundamental que las organizaciones usen el control de acceso basado en rol de Azure (RBAC de Azure) para controlar quién puede crear y modificar las puertas de enlace de VPN y sus conexiones. Azure no tiene ningún rol integrado para administrar las puertas integrales de VPN, por lo que se requiere un rol personalizado.

El acceso a los roles Propietario, Colaborador y Colaborador de la red está muy controlado. También se recomienda usar Azure AD Privileged Identity Management para tener un control más pormenorizado del acceso.

### <a name="high-availability"></a>Alta disponibilidad

Las puertas de enlace de VPN de Azure pueden tener varias conexiones y admitir varios dispositivos VPN locales en el mismo entorno local. Vea el diagrama 1.

Las redes virtuales de Azure pueden tener varias puertas de enlace de VPN que se pueden implementar en configuraciones independientes, activas-pasivas o activas-activas.

Se recomienda implementar todas las puertas de enlace de VPN en una [configuración de alta disponibilidad](../vpn-gateway/vpn-gateway-highlyavailable.md). Un ejemplo son dos dispositivos VPN locales conectados a dos puertas de enlace de VPN en los modos activo-pasivo o activo-activo. Vea el diagrama 2.

![Conexiones redundantes de puerta de enlace de VPN](media/dual-redundancy.png)

*Diagrama 2: puertas de enlace de VPN activas-activas y dos dispositivos VPN*

### <a name="forced-tunneling"></a>Tunelización forzada

La tunelización forzada redirige u obliga a todo el tráfico vinculado a Internet a volver al entorno local a través de la puerta de enlace de VPN para su inspección y auditoría. Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure atraviesa la infraestructura de red de Azure hasta la red pública de Internet, sin la opción para inspeccionar o auditar el tráfico. La tunelización forzada es fundamental cuando una organización está obligada a usar una puerta de enlace de Internet segura (SIG) para un entorno.

## <a name="detailed-configuration"></a>Configuración detallada

### <a name="service-attributes"></a>Atributos del servicio

Las puertas de enlace de VPN para las conexiones S2S configuradas para el gobierno de Australia deben tener los siguientes atributos:

|Atributo | Obligatorio|
|--- | --- |
|gatewayType | "VPN"|
|

La configuración de los atributos necesaria para cumplir los controles del ISM para la PROTECCIÓN es:

|Atributo | Obligatorio|
|--- |---|
|vpnType |"RouteBased"|
|vpnClientConfiguration/vpnClientProtocols | "IkeV2"|
|

Las puertas de enlace de VPN de Azure admiten un rango de algoritmos criptográficos desde los estándares de los protocolos IPsec e IKE. La directiva personalizada establece una interoperabilidad máxima con un amplio rango de dispositivos VPN de terceros. Como resultado, es posible que durante el protocolo de enlace de IKE se negocie una configuración no compatible. Se recomienda encarecidamente aplicar los parámetros de las [directivas de IPsec/IKE personalizadas](../vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md) a vpnClientConfiguration en las puertas de enlace de VPN para asegurarse de que las conexiones cumplen los controles de ISM para las conexiones del entorno local a Azure. En la tabla siguiente se muestran los principales atributos.

|Atributo|Posible|Obligatorio|
|---|---|---|
|saLifeTimeSeconds|>14 400 s|>300 s|
|saDataSizeKilobytes| |>1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

En la tabla anterior, en el caso de dhGroup y pfsGroup, se prefieren ECP256 y ECP384, aunque se pueden usar otros valores.

### <a name="related-services"></a>Servicios relacionados

Al diseñar y configurar una puerta de enlace de VPN de Azure, también deben estar disponibles y deben configurarse varios servicios relacionados.

|Servicio | Acción requerida|
|--- | ---|
|Red virtual | Las puertas de enlace de VPN están conectadas a una red virtual. Cree una red virtual antes de crear una puerta de enlace de VPN.|
|Dirección IP pública | Las puertas de enlace de VPN S2S necesitan una dirección IP pública para establecer la conexión entre el dispositivo VPN local y la puerta de enlace de VPN. Cree una dirección IP pública antes de crear una puerta de enlace de VPN S2S.|
|Subred | Cree una subred de la red virtual para la puerta de enlace de VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Pasos de implementación con PowerShell

### <a name="azure-role-based-access-control"></a>Control de acceso basado en roles de Azure

1. Cree un rol personalizado. Un ejemplo es Colaborador de virtualNetworkGateway. Cree un rol que se asignará a los usuarios a los que se les vaya a permitir crear y modificar puertas de enlace de VPN. El rol personalizado debe permitir las siguientes operaciones:

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Agregue el rol personalizado a los usuarios que tengan permiso para crear y administrar puertas de enlace de VPN y las conexiones a los entornos locales.

### <a name="create-a-vpn-gateway"></a>Creación de una puerta de enlace de VPN

En estos pasos se supone que ya se ha creado una red virtual.

1. Cree una dirección IP pública.
2. Cree una subred de la puerta de enlace de VPN.
3. Cree el archivo de configuración de la IP de una puerta de enlace de VPN.
4. Cree una puerta de enlace de VPN.
5. Cree una puerta de enlace de red local para el dispositivo VPN local.
6. Cree una directiva IPsec. En este paso se supone que usa directivas IPsec o IKE personalizadas.
7. Cree una conexión entre la puerta de enlace VPN y una puerta de enlace de red local mediante la directiva IPsec.

### <a name="enforce-tunneling"></a>Aplicación de la tunelización

Si se requiere la tunelización forzada para crear la puerta de enlace de VPN:

1. Cree una tabla de rutas y reglas de enrutamiento.
2. Asocie una tabla de enrutamiento a las subredes adecuadas.

Después de crear la puerta de enlace de VPN:

- Establezca GatewayDefaultSite en el entorno local de la puerta de enlace de VPN.

### <a name="example-powershell-script"></a>Script de PowerShell de ejemplo

Un ejemplo de script de PowerShell que se usa para crear una directiva IPsec o IKE personalizada que cumpla los controles del ISM de la clasificación de seguridad PROTEGIDA de Australia.

Se supone que ya existen la red virtual, la puerta de enlace de VPN y las puertas de enlace locales.

#### <a name="create-an-ipsecike-policy"></a>Cree una directiva IPsec/IKE.

El script de ejemplo siguiente crea una directiva de IPsec o IKE con los algoritmos y parámetros siguientes:

- IKEv2: AES256, SHA256 y DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, vigencia de SA de 14 400 segundos y 102 400 000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Creación de una conexión VPN de sitio a sitio con una directiva IPsec/IKE personalizada

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo se ha tratado la configuración específica de VPN Gateway para cumplir los requisitos especificados en el manual de seguridad de la información para proteger los datos PROTEGIDOS del gobierno australiano mientras están en tránsito. Para conocer los pasos necesarios para configurar una puerta de enlace de VPN:

- [Introducción a la puerta de enlace de red de Azure](../vpn-gateway/index.yml)  
- [¿Qué es VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md)  
- [Creación de una red virtual con conexión VPN de sitio a sitio mediante PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)  
- [Creación y administración de una instancia de VPN Gateway](../vpn-gateway/tutorial-create-gateway-portal.md)
