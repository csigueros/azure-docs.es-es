---
title: Esquema de NetworkConfiguration de Azure Cloud Services (clásico) | Microsoft Docs
description: Conozca los elementos secundarios del elemento NetworkConfiguration del archivo de configuración del servicio, que especifica valores de Virtual Network y DNS.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
'---thor': tagore
ms.openlocfilehash: 009e1c19cc3f4349651c5b17d99c8b94e7dfbecf
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822666"
---
# <a name="azure-cloud-services-classic-config-networkconfiguration-schema"></a>Esquema de NetworkConfiguration de configuración de Azure Cloud Services (clásico)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

El elemento `NetworkConfiguration` del archivo de configuración de servicio especifica valores de DNS y red virtual. Estas configuraciones son opcionales para los servicios en la nube.

Puede usar los siguientes recursos para obtener más información acerca de las redes virtuales y los esquemas asociados:

- [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Esquema de configuración de Cloud Service [clásico])
- [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Esquema de definición de servicio en la nube [clásico])
- [Creación de una red virtual (clásica)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
En el ejemplo siguiente se muestra el elemento `NetworkConfiguration` y sus elementos secundarios.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

En la siguiente tabla se describen los elementos secundarios del elemento `NetworkConfiguration`.

| Elemento       | Descripción |
| ------------- | ----------- |
| AccessControl | Opcional. Especifica las reglas de acceso a los puntos de conexión de un servicio en la nube. El nombre de control de acceso se define mediante una cadena en el atributo `name`. El elemento `AccessControl` contiene uno o varios elementos `Rule`. Se puede definir más de un elemento `AccessControl`.|
| Regla | Opcional. Indica la acción que debe realizarse para un intervalo de direcciones IP de subred especificado. El orden de la regla se define mediante un valor de cadena en el atributo `order`. Cuanto menor sea el número de regla, mayor será la prioridad. Por ejemplo, se pueden especificar reglas con números de orden de 100, 200 y 300. La regla con el número de orden de 100 tiene prioridad sobre la regla que tiene un orden de 200.<br /><br /> La acción de la regla se define mediante una cadena en el atributo `action`. Los valores posibles son:<br /><br /> -   `permit`: especifica que solo los paquetes del intervalo de subred especificado pueden comunicarse con el punto de conexión.<br />-   `deny`: especifica que se deniega el acceso a los puntos de conexión del intervalo de subred especificado.<br /><br /> El intervalo de subred de direcciones IP que resultan afectados por la regla se definen mediante una cadena en el atributo `remoteSubnet`. La descripción de la regla se define mediante una cadena en el atributo `description`.|
| EndpointAcl | Opcional. Especifica la asignación de reglas de control de acceso a un punto de conexión. El nombre del rol que contiene el punto de conexión se define mediante una cadena en el atributo `role`. El nombre del punto de conexión se define mediante una cadena en el atributo `endpoint`. El nombre del conjunto de reglas de `AccessControl` que se deben aplicar al punto de conexión se definen en una cadena en el atributo `accessControl`. Se puede definir más de un elemento `EndpointAcl`.|
| DnsServer | Opcional. Especifica la configuración de un servidor DNS. Puede especificar la configuración de servidores DNS sin una red virtual. El nombre del servidor DNS se define mediante una cadena en el atributo `name`. La dirección IP del servidor DNS se define mediante una cadena en el atributo `IPAddress`. La dirección IP debe ser una dirección IPv4 válida.|
| VirtualNetworkSite | Opcional. Especifica el nombre del sitio de red Virtual en el que quiere implementar el servicio en la nube. Este valor no crea un sitio de red virtual. Hace referencia a un sitio que se ha definido anteriormente en el archivo de red para la red virtual. Un servicio en la nube solo puede ser miembro de una red virtual. Si no especifica este valor, el servicio en la nube no se implementará en una red virtual. El nombre del sitio de red virtual se define mediante una cadena en el atributo `name`.|
| InstanceAddress | Opcional. Especifica la asociación de un rol a una subred o un conjunto de subredes en la red virtual. Al asociar un nombre de rol a una dirección de instancia, puede especificar las subredes a las que quiere asociar este rol. El elemento `InstanceAddress` contiene un elemento Subnets. El nombre del rol que está asociado con la subred o las subredes se define mediante una cadena en el atributo `roleName`.|
| Subnet | Opcional. Especifica la subred que corresponde al nombre de subred en el archivo de configuración de red. El nombre de la subred se define mediante una cadena en el atributo `name`.|
| ReservedIP | Opcional. Especifica la dirección IP reservada que se debe asociar con la implementación. Debe usar Crear dirección IP reservada para crear la dirección IP reservada. Cada implementación de un servicio en la nube se puede asociar a una dirección IP reservada. El nombre de la dirección IP reservada se define mediante una cadena en el atributo `name`.|

## <a name="see-also"></a>Consulte también
[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Esquema de configuración de Cloud Service [clásico])