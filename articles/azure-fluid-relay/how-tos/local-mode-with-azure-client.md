---
title: 'Procedimiento: Uso de AzureClient para pruebas locales'
description: Procedimiento para usar AzureClient para probar una aplicación sin un servicio
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661703"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>Procedimiento: Uso de AzureClient para pruebas locales

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Este artículo le guía por los pasos necesarios para configurar **AzureClient** en modo local y usarlo para probar cualquier aplicación de Fluid localmente.

## <a name="configure-and-create-an-azureclient"></a>Configuración y creación de AzureClient

**AzureClient** se puede configurar para ejecutarse en una instancia local de Azure Fluid Relay, para lo que se usa una configuración como la siguiente.

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

En este ejemplo se usa **InsecureTokenProvider** para generar y firmar tokens de autenticación que el servicio Azure Fluid Relay aceptará. Sin embargo, como su nombre indica, esto no es seguro y no se debe utilizar en entornos de producción. Para más información sobre InsecureTokenProvider, consulte [Autenticación y autorización en la aplicación](https://fluidframework.com/docs/build/auth/#the-token-provider).

Para realizar la ejecución a nivel local, primero es preciso configurar las direcciones URL del solicitante y del almacenamiento para que apunten al dominio y puerto en el que se ejecuta la instancia local del servicio Azure Fluid Relay (http://localhost:7070 de forma predeterminada). El último paso es establecer `tenantId` en `LOCAL_MODE_TENANT_ID`. Todos estos valores juntos configuran AzureClient para que funcione con un servicio Azure Fluid Relay local.  

## <a name="enabling-debug-logging"></a>Habilitación del registro de depuración

Puede habilitar el registro de depuración integrado desde Fluid Framework con la siguiente configuración en una consola del explorador.

`localStorage.debug = 'fluid:*'`

Para escenarios más avanzados, puede usar `logger` en AzureClient. Esto permite personalizar el comportamiento del registro. Para más información sobre el registrador o la telemetría, consulte el artículo sobre [registro y telemetría](https://fluidframework.com/docs/testing/telemetry/) en fluidframework.com. 

## <a name="running-azure-fluid-relay-service-locally"></a>Ejecución local del servicio Azure Fluid Relay

Para usar el modo local de AzureClient, primero debe iniciar un servidor local. La ejecución de `npx @fluidframework/azure-local-service@latest` desde la ventana del terminal iniciará el servidor local de Azure Fluid Relay. Una vez iniciado el servidor, puede ejecutar la aplicación en el servicio local.
