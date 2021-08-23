---
title: 'Inicio rápido: Su primera consulta de JavaScript'
description: En este inicio rápido, dará los pasos necesarios para habilitar la biblioteca de Resource Graph para JavaScript y ejecutará su primera consulta.
ms.date: 07/09/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 9dc5068232e6ad19715535ba8e41cff213e23311
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459970"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Inicio rápido: Ejecución de la primera consulta de Resource Graph con JavaScript

Este inicio rápido le guiará a través del proceso de incorporación de las bibliotecas a la instalación de JavaScript. El primer paso para usar Azure Resource Graph es inicializar una aplicación de JavaScript con las bibliotecas necesarias.

Cuando finalice el proceso, habrá agregado las bibliotecas a la instalación de JavaScript y ejecutado su primera consulta de Resource Graph.

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de Azure**: si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

- **Node.js**: se requiere [Node.js](https://nodejs.org/) versión 12 o posterior.

## <a name="application-initialization"></a>Inicialización de aplicaciones

Para permitir que JavaScript consulte Azure Resource Graph, debe configurarse el entorno. Este programa de instalación funciona siempre que se pueda usar JavaScript, incluido [bash en Windows 10](/windows/wsl/install-win10).

1. Inicialice un nuevo proyecto de Node.js mediante la ejecución del siguiente comando.

   ```bash
   npm init -y
   ```

1. Agregue una referencia al módulo yargs.

   ```bash
   npm install yargs
   ```

1. Agregue una referencia al módulo de Azure Resource Graph.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Agregue una referencia a la biblioteca de autenticación de Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Compruebe en _package.json_ que `@azure/arm-resourcegraph` utiliza la versión **2.0.0** o posterior y `@azure/ms-rest-nodeauth` la versión **3.0.3** o posterior.

## <a name="query-the-resource-graph"></a>Consulta de Resource Graph

1. Cree un nuevo archivo denominado _index.js_ y escriba el código siguiente.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query) {
       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

   > [!NOTE]
   > Este código crea una consulta basada en inquilino. Para limitar la consulta a un [grupo de administración](../management-groups/overview.md) o una suscripción, defina y agregue una [solicitud de consulta](/javascript/api/@azure/arm-resourcegraph/queryrequest) a la llamada `client.resources` y especifique `managementGroups` o `subscriptions`.

1. En el terminal, escriba el siguiente comando:

   ```bash
   node index.js --query "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Como esta consulta de ejemplo no proporciona un modificador de ordenación como `order by`, es probable que al ejecutar esta consulta varias veces se produzca un conjunto diferente de recursos por solicitud.

1. Cambie el primer parámetro a `index.js` y cambie la consulta para realizar la operación `order by` en la propiedad **Nombre**.

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc"
   ```

   A medida que el script intenta autenticarse, se muestra un mensaje similar al siguiente en el terminal:

   > Para iniciar sesión, use un explorador web para abrir la página https://microsoft.com/devicelogin y escriba el código FGB56WJUGK para autenticarse.

   Una vez que se autentica en el explorador, el script continúa ejecutándose.

   > [!NOTE]
   > Al igual que con la primera consulta, es probable que al ejecutar esta consulta varias veces se produzca un conjunto diferente de recursos por solicitud. El orden de los comandos de consulta es importante. En este ejemplo, el `order by` viene después del `limit`. Este orden de comandos limita primero los resultados de la consulta y, luego, los ordena.

1. Cambie el primer parámetro a `index.js` y cambie la consulta para realizar la operación `order by` primero en la propiedad **Nombre** y, luego, para realizar la operación `limit` a los cinco primeros resultados.

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5"
   ```

Cuando la consulta final se ejecuta varias veces, suponiendo que nada cambie en su entorno, los resultados devueltos serán coherentes y estarán ordenados por la propiedad **Nombre**, pero todavía limitados a los cinco primeros resultados.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si desea quitar las bibliotecas instaladas de la aplicación, ejecute el siguiente comando.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha agregado las bibliotecas de Resource Graph al entorno de JavaScript y ha ejecutado su primera consulta. Para más información sobre el lenguaje de Resource Graph, vaya a la página de detalles del lenguaje de consulta.

> [!div class="nextstepaction"]
> [Obtener más información sobre el lenguaje de consulta](./concepts/query-language.md)
