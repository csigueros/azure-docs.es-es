---
title: Conexión y consulta del Servidor único de MySQL
description: 'Vínculos a los inicios rápidos que muestran cómo conectarse a Azure SQL Database: Servidor único y ejecutar consultas.'
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: def4794d091155cbf10a854d304d4390659626bf
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652856"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Información general de conexión y consulta de Azure Database for MySQL: Servidor único

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

En el documento siguiente se incluyen vínculos a ejemplos que muestran cómo conectarse a Azure Database for MySQL: Servidor único y realizar consultas en él. En esta guía también se incluyen las recomendaciones y bibliotecas de TLS que puede usar para conectarse al servidor en los idiomas admitidos a continuación.

## <a name="quickstarts"></a>Guías de inicio rápido

| Guía de inicio rápido | Descripción |
|---|---|
|[MySQL Workbench](connect-workbench.md)|En este inicio rápido se muestra cómo usar el cliente de MySQL Workbench para conectarse a una base de datos. Después puede usar instrucciones MySQL para consultar, insertar, actualizar y eliminar datos en la base de datos.|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|En este artículo se muestra cómo ejecutar **mysql.exe** en [Azure Cloud Shell](../cloud-shell/overview.md) para conectarse al servidor y, después, ejecutar instrucciones para consultar, insertar, actualizar y eliminar datos en la base de datos.|
|[MySQL con Visual Studio](https://www.mysql.com/why-mysql/windows/visualstudio)|Puede usar MySQL para Visual Studio a fin de conectarse al servidor MySQL. MySQL para Visual Studio se integra directamente en el Explorador de servidores, lo que facilita la configuración de conexiones nuevas y el trabajo con objetos de base de datos.|
|[PHP](connect-php.md)|En este inicio rápido se muestra cómo usar PHP para crear un programa que se conecte a una base de datos y que use instrucciones MySQL a fin de consultar los datos.|
|[Java](connect-java.md)|Este inicio rápido muestra cómo utilizar Java para conectarse a una base de datos y luego utilizar instrucciones MySQL a fin de consultar los datos.|
|[Node.js](connect-nodejs.md)|En este inicio rápido se muestra cómo se usa Node.js para crear un programa que se conecte a una base de datos y que use instrucciones MySQL a fin de consultar los datos.|
|[.NET(C#)](connect-csharp.md)|En este inicio rápido se muestra cómo se usa .NET (C#) para crear un programa de C# que se conecte a una base de datos y que use instrucciones MySQL a fin de consultar los datos.|
|[Go](connect-go.md)|En este inicio rápido se muestra cómo usar Go para conectarse a una base de datos. También se muestran las instrucciones Transact-SQL para consultar y modificar los datos.|
|[Python](connect-python.md)|En este inicio rápido se muestra cómo utilizar Python para conectarse a una base de datos y luego utilizar instrucciones MySQL a fin de consultar los datos. |
|[Ruby](connect-ruby.md)|En este inicio rápido se muestra cómo usar Ruby para crear un programa que se conecte a una base de datos y que use instrucciones MySQL a fin de consultar los datos.|
|[C++](connect-cpp.md)|En este inicio rápido se muestra cómo usar C++ para crear un programa que se conecte a una base de datos y que use instrucciones MySQL a fin de consultar los datos.|

## <a name="tls-considerations-for-database-connectivity"></a>Consideraciones de TLS para la conectividad de bases de datos

El protocolo Seguridad de capa de transporte (TLS) lo usan todos los controladores que Microsoft proporciona o admite para conectarse a bases de datos de Azure Database for MySQL. No es necesaria ninguna configuración especial, pero se aplica TLS 1.2 para los servidores recientemente creados. Se recomienda que, si usa TLS 1.0 y 1.1, después actualice la versión de TLS para los servidores. Consulte [Procedimientos para configurar TLS](howto-tls-configurations.md).

## <a name="libraries"></a>Bibliotecas

Azure Database for MySQL usa la edición comunitaria más popular del mundo de la base de datos MySQL. Por lo tanto, es compatible con una amplia variedad de controladores y lenguajes de programación. El objetivo es la compatibilidad con las tres versiones más recientes de los controladores MySQL, y continúan los esfuerzos con los autores de la comunidad de código abierto para mejorar constantemente la funcionalidad y la facilidad de uso de los controladores de MySQL.

Vea qué [controladores](concepts-compatibility.md) son compatibles con Azure Database for MySQL: Servidor único.

## <a name="next-steps"></a>Pasos siguientes

- [Migración de datos mediante volcado y restauración](concepts-migrate-dump-restore.md)
- [Migración de datos mediante importación y exportación](concepts-migrate-import-export.md)