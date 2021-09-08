---
title: Conversión a la biblioteca de Spring Boot
titleSuffix: Azure App Configuration
description: Obtenga información sobre cómo pasar a la nueva biblioteca Spring Boot de App Configuration desde la versión anterior.
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553111"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>Conversión a una nueva biblioteca Spring Boot de App Configuration

Ahora hay una nueva versión disponible de la biblioteca de App Configuration para Spring Boot. La versión presenta nuevas características, como la actualización de inserción, pero también una serie de cambios importantes. Estos cambios no son compatibles con versiones anteriores de las configuraciones que usaban la versión anterior de la biblioteca. Para los siguientes temas.

* Identificadores de grupo y artefacto
* Perfiles de Spring
* Carga y recarga de configuración
* Carga de marcas de características

En este artículo se proporciona una referencia sobre el cambio y las acciones necesarias para migrar a la nueva versión de la biblioteca.

## <a name="group-and-artifact-id-changed"></a>Id. de grupo y artefacto cambiado

Todas las bibliotecas de Spring Boot de Azure han actualizado sus id. de grupo y artefacto para que coincidan con un nuevo formato. Los nuevos nombres de paquete son:

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Uso de perfiles de Spring

En la versión anterior, los perfiles de Spring se usaban como parte de la configuración para que pudieran coincidir con el formato de los archivos de configuración. Por ejemplo,

```properties
/<application name>_dev/config.message
```

Esto se ha cambiado para que las etiquetas predeterminadas de una consulta sean los perfiles de Spring con el formato siguiente, con una etiqueta que coincida con el perfil de Spring:

```properties
/application/config.message
```

 Para convertir al nuevo formato, puede ejecutar los siguientes comandos con el nombre del almacén:

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

o usar la característica Import/Export del portal.

Cuando haya implementado completamente la nueva versión, puede quitar las claves antiguas mediante la ejecución de:

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

Este comando enumerará todas las claves que está a punto de eliminar para que pueda comprobar que no se quitarán claves inesperadas. Las claves también se pueden eliminar en el portal.

## <a name="which-configurations-are-loaded"></a>Qué configuraciones se cargan

El caso predeterminado de carga de la configuración que coincide con `/applicaiton/*` no ha cambiado. El cambio es que ya no se usará `/${spring.application.name}/*` de forma adicional automáticamente, a menos que se establezca. Para utilizar `/${spring.application.name}/*`, puede usar la nueva configuración Selects.

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>Recarga de configuración

La supervisión de todos los almacenes de configuración ahora está deshabilitada de forma predeterminada. Se ha agregado una nueva configuración a la biblioteca para permitir que los almacenes de configuración tengan habilitada la supervisión. Además, se ha cambiado el nombre de expiración de caché a intervalo de actualización y también se ha cambiado para que sea por almacén de configuración. Además, si la supervisión de un almacén de configuración está habilitada, es necesario configurar al menos una clave inspeccionada, con una etiqueta opcional.

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

No ha habido ningún cambio en el funcionamiento del intervalo de actualización, el cambio modifica el nombre de la configuración para aclarar la funcionalidad. El requisito de una clave inspeccionada asegura que cuando se cambien las configuraciones, la biblioteca no intentará cargar las configuraciones hasta que se realicen todos los cambios.

## <a name="feature-flag-loading"></a>Carga de marcas de características

Ahora, la carga de marcas de características está deshabilitada de forma predeterminada. Además, las marcas de características ahora tienen un filtro de etiqueta, así como un intervalo de actualización.

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```
