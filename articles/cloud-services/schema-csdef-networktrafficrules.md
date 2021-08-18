---
title: Azure Cloud Services (clásico) -Esquema NetworkTrafficRules de definición de Azure Cloud Services | Microsoft Docs
description: Obtenga información sobre NetworkTrafficRules, que limita los roles que pueden acceder a los puntos de conexión internos de un rol. Se combina con los roles de un archivo de definición de servicio.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6a73a867d0a204ae4afa0f99c42adae7215b242c
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113087565"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Esquema NetworkTrafficRules de definición de Azure Cloud Services (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

El nodo `NetworkTrafficRules` es un elemento opcional del archivo de definición de servicio que especifica cómo se comunican los roles entre sí. Limita qué roles pueden acceder a los puntos de conexión internos del rol en cuestión. `NetworkTrafficRules` no es un elemento independiente; se combina con dos o más roles de un archivo de definición de servicio.

La extensión predeterminada del archivo de definición de servicio es. csdef.

> [!NOTE]
>  El nodo `NetworkTrafficRules` solo está disponible mediante la versión 1.3 o superior de Azure SDK.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema básico de definición de servicio para las reglas de tráfico de red
El formato básico de un archivo de definición de servicio que contiene definiciones de tráfico de red es el siguiente.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
El nodo `NetworkTrafficRules` del archivo de definición de servicio incluye estos elementos, que se describen más detalladamente en las sucesivas secciones de este tema:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento Destinations](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

Elemento AllowAllTraffic

[Elemento WhenSource](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> Elemento NetworkTrafficRules
El elemento `NetworkTrafficRules` especifica qué roles se pueden comunicar con qué punto de conexión de otro rol. Un servicio puede contener una definición de `NetworkTrafficRules`.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> Elemento OnlyAllowTrafficTo
El elemento `OnlyAllowTrafficTo` describe una colección de puntos de conexión de destino y los roles que se pueden comunicar con ellos. Puede especificar varios nodos `OnlyAllowTrafficTo`.

##  <a name="destinations-element"></a><a name="Destinations"></a> Elemento Destinations
El elemento `Destinations` describe una colección de elementos RoleEndpoint con los que se puede comunicar.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> Elemento RoleEndpoint
El elemento `RoleEndpoint` describe un punto de conexión en un rol con el que permitir la comunicación. Puede especificar varios elementos `RoleEndpoint` si hay más de un punto de conexión en el rol.

| Atributo      | Tipo     | Descripción |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Necesario. El nombre del punto de conexión al que permitir el tráfico.|
| `roleName`     | `string` | Necesario. El nombre del rol web al que permitir la comunicación.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
El elemento `AllowAllTraffic` es una regla que permite que todos los roles se comuniquen con los puntos de conexión definidos en el nodo `Destinations`.

##  <a name="whensource-element"></a><a name="WhenSource"></a> Elemento WhenSource
El elemento `WhenSource` describe una colección de roles que se pueden comunicar con los puntos de conexión definidos en el nodo `Destinations`.

| Atributo | Tipo     | Descripción |
| --------- | -------- | ----------- |
| `matches` | `string` | Necesario. Especifica la regla que se aplicará al permitir las comunicaciones. Actualmente, el único valor válido es `AnyRule`.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> Elemento FromRole
El elemento `FromRole` especifica los roles que se pueden comunicar con los puntos de conexión definidos en el nodo `Destinations`. Puede especificar varios elementos `FromRole` si hay más de un rol que pueda comunicarse con los puntos de conexión.

| Atributo  | Tipo     | Descripción |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Necesario. El nombre del rol desde el que se va a permitir la comunicación.|

## <a name="see-also"></a>Consulte también
[Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Esquema de definición de servicio en la nube [clásico])




