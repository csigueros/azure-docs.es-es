---
title: Uso de Robo 3T para conectarse a Azure Cosmos DB
description: Obtenga información sobre cómo conectarse a Azure Cosmos DB mediante Robo 3T y la API de Azure Cosmos DB para MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: bb7de61fc81a39fc6000f4adfac71a62c93aec41
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039639"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Uso de Robo 3T con la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Para conectar a una cuenta de Cosmos con Robo 3T, debe:

* Descargar e instalar [Robo 3T](https://robomongo.org/)
* Tener información de la [cadena de conexión](connect-mongodb-account.md) de Cosmos DB

> [!NOTE]
> Actualmente, Robo 3T v1.2 y las versiones anteriores se admiten con la API de Cosmos DB para MongoDB.

## <a name="connect-using-robo-3t"></a>Conectar con Robo 3T

Para agregar la cuenta de Cosmos DB al administrador de conexiones de Robo 3T, siga estos pasos:

1. Recupere la información de conexión de su cuenta de Cosmos configurada con la API MongoDB de Azure Cosmos DB siguiendo las instrucciones indicadas [aquí](connect-mongodb-account.md).

    :::image type="content" source="./media/connect-using-robomongo/connectionstringblade.png" alt-text="Captura de pantalla de la hoja de cadena de conexión":::
2. Ejecute la aplicación *Robomongo*.

3. Haga clic en el botón de conexión, que está bajo **File** (Archivo), para administrar las conexiones. Después, haga clic en la opción **Create** (Create) de la ventana **MongoDB Connections**; se abrirá **Connection Settings** (Configuración de conexiones).

4. En la ventana **Connection Settings** (Configuración de conexiones), elija un nombre. Después, busque el **host** y **puerto** en la información de conexión del paso 1 y escríbalos en **Address** (Dirección) y **Port** (Puerto) respectivamente.

    :::image type="content" source="./media/connect-using-robomongo/manageconnections.png" alt-text="Captura de pantalla de la funcionalidad de administración de conexiones de Robomongo":::
5. En la pestaña **Authentication** (Autenticación), haga clic en **Perform authentication** (Realizar autenticación). Después, escriba la base de datos (el valor predeterminado es *Admin*), el **nombre de usuario** y la **contraseña**.
Tanto el **nombre de usuario** como la **contraseña** están en la información de conexión del paso 1.

    :::image type="content" source="./media/connect-using-robomongo/authentication.png" alt-text="Captura de pantalla de la pestaña de autenticación de Robomongo":::
6. En la pestaña **SSL**, marque **Use SSL protocol** (Usar protocolo SSL) y, luego, cambie el **método de autenticación** a **Self-signed Certificate** (Certificado autofirmado).

    :::image type="content" source="./media/connect-using-robomongo/ssl.png" alt-text="Captura de pantalla de la pestaña SSL de Robomongo":::
7. Por último, haga clic en **Test** (Probar) para comprobar que puede conectarse. Después, seleccione **Save** (Guardar).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Studio 3T](connect-using-mongochef.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](nodejs-console-app.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
- ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    - Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    - Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).