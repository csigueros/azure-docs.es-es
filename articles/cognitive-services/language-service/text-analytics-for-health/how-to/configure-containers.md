---
title: Configuración de contenedores de Text Analytics for Health
titleSuffix: Azure Cognitive Services
description: Los contenedores de Text Analytics for Health usan un marco de configuración común, por lo que puede configurar y administrar fácilmente los valores de almacenamiento, registro, telemetría y seguridad de los contenedores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 801deee67503a7f7010e3c986b2ab1cf818c9f51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093172"
---
# <a name="configure-text-analytics-for-health-docker-containers"></a>Configuración de contenedores de Docker de Text Analytics for Health

Text Analytics for Health proporciona a cada contenedor un marco de configuración común, por lo que puede configurar y administrar fácilmente los valores de almacenamiento, registro, telemetría y seguridad de los contenedores. También están disponibles varios [ejemplos de comandos de docker run](use-containers.md#run-the-container-with-docker-run).

## <a name="configuration-settings"></a>Parámetros de configuración

[!INCLUDE [Container shared configuration settings table](../../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Las opciones [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) y [`Eula`](#eula-setting) se usan en conjunto y debe proporcionar valores válidos para las tres; en caso contrario, no se inicia el contenedor. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](use-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Opción de configuración ApiKey

La opción de configuración `ApiKey` especifica la clave de recurso de Azure usada para realizar un seguimiento de la información de facturación del contenedor. Debe especificar un valor para ApiKey que debe ser una clave válida para el recurso de _Language_ especificado para la opción de configuración [`Billing`](#billing-configuration-setting).

Este valor se puede encontrar en el siguiente lugar:

* Azure Portal: Administración de recursos de **Language**, en **Claves y punto de conexión**

## <a name="applicationinsights-setting"></a>Opción de configuración ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Opción de configuración Billing

El valor `Billing` especifica el URI de punto de conexión del recurso de _Language_ en Azure que se usa para medir la información de facturación del contenedor. Debe especificar un valor para esta opción de configuración, y el valor debe ser un URI de punto de conexión válido para un recurso de _Language_ en Azure. El contenedor informa sobre el uso cada 10 a 15 minutos.

Este valor se puede encontrar en el siguiente lugar:

* Azure Portal: Información general de **Language**, con la etiqueta `Endpoint`

|Obligatorio| Nombre | Tipo de datos | Descripción |
|--|------|-----------|-------------|
|Sí| `Billing` | String | Identificador URI del punto de conexión de facturación. Para más información sobre cómo obtener el URI de facturación, consulte la [recopilación de los parámetros necesarios](use-containers.md#gathering-required-parameters). Para más información y para obtener una lista completa de los puntos de conexión regionales, consulte [Nombres de subdominios personalizados para Cognitive Services.](../../../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Opción de configuración Eula

[!INCLUDE [Container shared configuration eula settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Opción de configuración Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configuración de las credenciales del proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Opción de configuración Logging
 
[!INCLUDE [Container shared configuration logging settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configuración de montaje

Utilice montajes de enlace para leer y escribir datos hacia y desde el contenedor. Puede especificar un montaje de entrada o un montaje de salida mediante la opción `--mount` del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Los contenedores de Text Analytics for Health no usan montajes de entrada ni salida para almacenar datos de entrenamiento o del servicio. 

La sintaxis exacta de la ubicación de montaje del host varía según el sistema operativo del host. Además, la ubicación de montaje del [equipo host](use-containers.md#host-computer-requirements-and-recommendations) puede no estar accesible debido a un conflicto entre los permisos que utiliza la cuenta de servicio de Docker y los permisos de la ubicación de montaje del host. 

|Opcional| Nombre | Tipo de datos | Descripción |
|-------|------|-----------|-------------|
|No permitida| `Input` | String | Los contenedores de Text Analytics for Health no usan esto.|
|Opcional| `Output` | String | Destino del montaje de salida. El valor predeterminado es `/output`. Esta es la ubicación de los registros. Esto incluye los registros de contenedor. <br><br>Ejemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Instalación y ejecución de contenedores](use-containers.md)
* Uso de [Contenedores de Cognitive Services](../../../cognitive-services-container-support.md)
