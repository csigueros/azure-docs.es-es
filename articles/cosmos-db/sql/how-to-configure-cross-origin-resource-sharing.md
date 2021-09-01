---
title: Uso compartido de recursos entre orígenes (CORS) en Azure Cosmos DB
description: En este artículo se describe cómo configurar el uso compartido de recursos entre orígenes (CORS) en Azure Cosmos DB mediante Azure Portal y las plantillas de Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 912cc07cc1091675af49c70f1e1402749a4571aa
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114324"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configuración del uso compartido de recursos entre orígenes (CORS)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El uso compartido de recursos entre orígenes (CORS) es una característica de HTTP que permite que una aplicación web que se ejecuta en un dominio tenga acceso a recursos de otro dominio. Los exploradores web implementan una restricción de seguridad que se conoce como directiva de mismo origen que impide que una página web realice llamadas API en un dominio diferente. Sin embargo, CORS aporta un modo seguro de permitir que el dominio de origen llame a las API de otro dominio. Ahora Core (SQL) API, en Azure Cosmos DB, admite el encabezado "allowedOrigins" de uso compartido de recursos entre orígenes (CORS). Después de habilitar la compatibilidad con CORS para la cuenta de Azure Cosmos, solo se evalúan las solicitudes autenticadas para determinar si se admiten según las reglas especificadas.

Puede configurar el uso compartido de recursos entre orígenes (CORS) en Azure Portal o con una plantilla de Azure Resource Manager. En cuanto a las cuentas de Cosmos que usan Core (SQL) API, Azure Cosmos DB admite una biblioteca de JavaScript que funciona tanto en Node.js como en entornos basados en explorador. Ahora esta biblioteca puede beneficiarse de la compatibilidad con CORS al usar el modo de puerta de enlace. El uso de esta característica no requiere configuración en el lado cliente. Con compatibilidad con CORS, los recursos de un explorador pueden acceder directamente a Azure Cosmos DB desde la [biblioteca de JavaScript](https://www.npmjs.com/package/@azure/cosmos) o directamente desde la [API de REST](/rest/api/cosmos-db/) para operaciones sencillas.

> [!NOTE]
> La compatibilidad con CORS solo es aplicable y es compatible con Core (SQL) API de Azure Cosmos DB. No es aplicable a las API de Azure Cosmos DB para Cassandra, Gremlin o MongoDB, ya que estos protocolos no usan HTTP para la comunicación entre cliente y servidor.

## <a name="enable-cors-support-from-azure-portal"></a>Habilitar la compatibilidad con CORS en Azure Portal

Use los pasos siguientes para habilitar el uso compartido de recursos entre orígenes en Azure Portal:

1. Vaya a la cuenta de Azure Cosmos DB. Abra la hoja **CORS**.

2. Especifique una lista de orígenes separados por comas que podrán realizar llamadas entre orígenes a su cuenta de Azure Cosmos DB. Por ejemplo: `https://www.mydomain.com`, `https://mydomain.com` y `https://api.mydomain.com`. También puede usar un carácter comodín "\*" para permitir todos los orígenes y, a continuación, seleccione **Enviar**. 

   > [!NOTE]
   > Actualmente, no puede usar comodines como parte del nombre de dominio. Por ejemplo, aún no se admite el formato `https://*.mydomain.net`. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Habilitar el uso compartido de recursos entre orígenes en Azure Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Habilitar la compatibilidad con CORS mediante la plantilla de Resource Manager

Para habilitar CORS con una plantilla de Resource Manager, agregue la sección “cors” con la propiedad “allowedOrigins” a cualquier plantilla existente. El siguiente esquema JSON es un ejemplo de una plantilla que crea una cuenta de Azure Cosmos con CORS habilitado.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Uso de la biblioteca de JavaScript para Azure Cosmos DB desde un explorador

Actualmente, la biblioteca de JavaScript para Azure Cosmos DB incluye solo la versión para CommonJS de la biblioteca con su paquete. Para usar esta biblioteca desde el explorador, debe usar una herramienta como Rollup o Webpack para crear una biblioteca compatible con el explorador. Algunas bibliotecas de Node.js deben tener objetos ficticios del explorador para ellas. A continuación se incluye un ejemplo de un archivo de configuración de webpack que tiene la configuración de objetos ficticios necesaria.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Este es un [código de ejemplo](https://github.com/christopheranderson/cosmos-browser-sample) que usa TypeScript y Webpack con la biblioteca del SDK de JavaScript de Azure Cosmos DB para crear una aplicación Todo que envía actualizaciones en tiempo real cuando se crean nuevos elementos.
Como procedimiento recomendado, no use la clave principal para comunicarse con Azure Cosmos DB desde el explorador. En su lugar, use los tokens de recursos para comunicarse. Para más información sobre los tokens de recursos, consulte el artículo [Protección del acceso a los datos de Azure Cosmos DB](../secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre otras formas de proteger la cuenta de Azure Cosmos, consulte los artículos siguientes:

* [Configuración de un firewall para Azure Cosmos DB](../how-to-configure-firewall.md)

* [Configuración del acceso basado en red virtual y subred para la cuenta de Azure Cosmos DB](../how-to-configure-vnet-service-endpoint.md)
