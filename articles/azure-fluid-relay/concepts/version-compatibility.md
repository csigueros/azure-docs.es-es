---
title: Compatibilidad con las versiones de Fluid Framework
description: >
  Cómo determinar qué versiones de Fluid Framework son compatibles con el servicio Azure Fluid Relay.
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661693"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Compatibilidad con las versiones de Fluid Framework

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

Para conectar la aplicación al servicio Azure Fluid Relay, debe usar la biblioteca **@fluidframework/azure-client** . También debe usar la biblioteca **fluid-framework** para usar las estructuras de datos principales que proporcione Fluid Framework.

Puesto que usa Azure Fluid Relay, primero debe instalar la versión más reciente disponible de @fluidframework/azure-client y usar esa versión para determinar qué versión de la biblioteca fluid-framework debe usar. La biblioteca expresa una *dependencia del mismo nivel* en la versión del paquete fluid-framework del que depende.

Asimismo, puede usar la herramienta [install-peerdeps](https://www.npmjs.com/package/install-peerdeps) para instalar @fluidframework/azure-client y la versión compatible de fluid-framework mediante el siguiente comando:

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> Durante la versión preliminar pública, las versiones de **@fluidframework/azure-client** y **fluid-framework** coincidirán. Es decir, si la versión actual de **@fluidframework/azure-client** es 0.48, será compatible con **fluid-framework** 0.48. Lo contrario también es válido.

## <a name="compatibility-table"></a>Tabla de compatibilidad

| Paquete npm                         | Versión mínima | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| fluid-framework                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de un servicio Azure Fluid Relay](../how-tos/connect-fluid-azure-service.md)
- [Conexión a un servicio Azure Fluid Relay](../how-tos/connect-fluid-azure-service.md)
- [Uso de AzureClient para pruebas locales](../how-tos/local-mode-with-azure-client.md)
