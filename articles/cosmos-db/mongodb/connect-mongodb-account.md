---
title: Conectar una aplicación de MongoDB a Azure Cosmos DB
description: Aprenda a conectar una aplicación de MongoDB a Azure Cosmos DB mediante una cadena de conexión de Azure Portal
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: ./connect-mongodb-account-experimental
ms.openlocfilehash: f3afc6d31cd312e16cd901ffe0c31bc3eddf13db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779951"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar una aplicación de MongoDB a Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Aprenda a conectar su aplicación de MongoDB a una instancia de Azure Cosmos DB mediante una cadena de conexión de MongoDB. Después, puede usar una base de datos de Azure Cosmos como almacén de datos de la aplicación MongoDB.

En este tutorial se proporcionan dos maneras de recuperar información de la cadena de conexión:

- [El método de inicio rápido](#get-the-mongodb-connection-string-by-using-the-quick-start), para su uso con controladores de .NET, Node.js, MongoDB Shell, Java y Python.
- [El método de la cadena de conexión personalizada](#get-the-mongodb-connection-string-to-customize), para su uso con otros controladores.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).
- Una cuenta de Cosmos. Para obtener instrucciones, consulte [Compilación de una aplicación web con la API de Azure Cosmos DB para MongoDB y .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Obtención de la cadena de conexión de MongoDB mediante el menú de inicio rápido

1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API.
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Inicio rápido**.
4. Elija la plataforma ( **.NET**, **Node.js**, **Shell de MongoDB**, **Java**, **Python**). Si no ve el controlador o la herramienta en la lista, no se preocupe, documentamos constantemente más fragmentos de código de conexión. Comente a continuación lo que le gustaría ver. Para aprender a crear su propia conexión, lea la sección sobre cómo [obtener información de la cadena de conexión de la cuenta](#get-the-mongodb-connection-string-to-customize).
5. Copie y pegue el fragmento de código en la aplicación MongoDB.

    :::image type="content" source="./media/connect-mongodb-account/quickstart-blade.png" alt-text="Hoja Inicio rápido":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Obtención de la cadena de conexión de MongoDB para personalizar

1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API.
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Cadena de conexión**.
4. Se abre la hoja **Cadena de conexión**, que contiene toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

   :::image type="content" source="./media/connect-mongodb-account/connection-string-blade.png" alt-text="Hoja Cadena de conexión" lightbox= "./media/connect-mongodb-account/connection-string-blade.png" :::

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión

> [!Important]
> Azure Cosmos DB tiene estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura mediante *TLS*.

Azure Cosmos DB es compatible con el formato URI de la cadena de conexión de MongoDB estándar con un par de requisitos específicos: Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura mediante TLS. Por tanto, el formato de la cadena de conexión es:

`mongodb://username:password@host:port/[database]?ssl=true`

Los valores de esta cadena están disponibles en la hoja **Cadena de conexión** mostrada antes:

* Username (obligatorio): nombre de la cuenta de Cosmos.
* Password (obligatorio): contraseña de la cuenta de Cosmos.
* Host (obligatorio): FQDN de la cuenta de Cosmos.
* Port (obligatorio): 10255.
* Database (opcional): base de datos que usa la conexión. Si no se proporciona ninguna base de datos, la base de datos predeterminada es "test".
* ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja **Cadena de conexión**. Una cadena de conexión válida es:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="driver-requirements"></a>Requisitos para controladores

Todos los controladores que admiten la versión 3.4 o posteriores del protocolo de conexión admitirán de API de Azure Cosmos DB para MongoDB.

En concreto, los controladores de cliente deben admitir la extensión TLS de la identificación de nombre de servicio (SNI) o la opción de cadena de conexión appName. Si se proporciona el parámetro `appName`, debe incluirse como se encuentra en el valor de la cadena de conexión en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Studio 3T](connect-using-mongochef.md) con la API de Azure Cosmos DB para MongoDB.
- Aprenda a [usar Robo 3T](connect-using-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](nodejs-console-app.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
