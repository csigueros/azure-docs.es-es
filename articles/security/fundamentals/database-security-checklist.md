---
title: Lista de comprobación de la seguridad de Azure Database | Microsoft Docs
description: Use la lista de comprobación de seguridad de base de datos de Azure para asegurarse de solucionar problemas importantes de seguridad de informática en la nube.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: c462e4084cc1fd2e46d7eec268501402cee7c887
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342972"
---
# <a name="azure-database-security-checklist"></a>Lista de comprobación de la seguridad de Azure Database

Para ayudar a mejorar la seguridad, Azure Database incluye una serie de controles de seguridad integrados que puede usar para limitar y controlar el acceso.

Entre ellas se incluyen las siguientes:

-    Un firewall que permite crear [reglas de firewall](../../azure-sql/database/firewall-configure.md) que limitan la conectividad en función de su dirección IP
-    Firewall de nivel de servidor accesible desde Azure Portal
-    Reglas de firewall de nivel de base de datos accesibles desde SSMS
-    Conectividad segura a una base de datos mediante cadenas de conexión seguras
-    Uso de la administración del acceso
-    Cifrado de datos
-    Auditoría de SQL Database
-    Detección de amenazas de SQL Database

## <a name="introduction"></a>Introducción
La informática en la nube requiere nuevos paradigmas de seguridad que son poco conocidos para muchos usuarios de aplicaciones, administradores de bases de datos y programadores. Como resultado, algunas organizaciones dudan de si implementar una infraestructura en la nube para la administración de datos debido a los riesgos de seguridad percibidos. Sin embargo, se puede mitigar gran parte de este problema mediante una mejor comprensión de las características de seguridad integradas en Microsoft Azure y Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista de comprobación
Le recomendamos que lea el artículo [Prácticas recomendadas de seguridad de Azure Database](../../azure-sql/database/security-best-practice.md) antes de revisar esta lista. Una vez que conozca las prácticas recomendadas, podrá obtener el máximo partido de esta lista de comprobación. A continuación, puede usarla para asegurarse de que ha abordado las cuestiones importantes de la seguridad para las bases de datos de Azure.


|Categoría de la lista de comprobación| Descripción|
| ------------ | -------- |
|**Protección de datos**||
| <br> Cifrado en movimiento o tránsito| <ul><li>[Seguridad de la capa de transporte](/windows-server/security/tls/transport-layer-security-protocol), para el cifrado de datos cuando los datos se pasan a las redes.</li><li>Las bases de datos requieren una comunicación segura desde los clientes basados en el protocolo [TDS (Tabular Data Stream)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) a través de TLS (Seguridad de la capa de transporte).</li></ul> |
|<br>Cifrado en reposo| <ul><li>[Cifrado de datos transparente](../../azure-sql/database/transparent-data-encryption-tde-overview.md), cuando los datos inactivos se almacenan físicamente en cualquier formato digital.</li></ul>|
|**Control de acceso**||  
|<br> Acceso a la base de datos | <ul><li>[Autenticación](../../azure-sql/database/logins-create-manage.md) (Autenticación de Azure Active Directory). La autenticación de AD usa las identidades administradas por Azure Active Directory.</li><li>[Autorización](../../azure-sql/database/logins-create-manage.md). Conceda a los usuarios los privilegios mínimos necesarios.</li></ul> |
|<br>Acceso a las aplicaciones| <ul><li>[Seguridad de nivel de fila](/sql/relational-databases/security/row-level-security). Se usa la directiva de seguridad, al mismo tiempo que se restringe el acceso de nivel de fila según el contexto de ejecución, rol o identidad de un usuario).</li><li>[Enmascaramiento dinámico de datos](../../azure-sql/database/dynamic-data-masking-overview.md). El uso de directivas y permisos limita la exposición de información confidencial al enmascararla para los usuarios sin privilegios</li></ul>|
|**Supervisión proactiva**||  
| <br>Seguimiento y detección| <ul><li>[La auditoría](../../azure-sql/database/auditing-overview.md) realiza un seguimiento de los eventos de bases de datos y los escribe en un registro de auditoría y de actividad en su [cuenta de Azure Storage](../../storage/common/storage-account-create.md).</li><li>Seguimiento del estado de Azure Database mediante [registros de actividad de Azure Monitor](../../azure-monitor/essentials/platform-logs-overview.md).</li><li>[Detección de amenazas](../../azure-sql/database/threat-detection-configure.md) detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad. </li></ul> |
|<br>Microsoft Defender for Cloud| <ul><li>[Supervisión de datos](../../security-center/security-center-remediate-recommendations.md). Use Microsoft Defender for Cloud como solución de supervisión de seguridad centralizada para SQL y otros servicios de Azure.</li></ul>|        

## <a name="conclusion"></a>Conclusión
Azure Database es una sólida plataforma de base de datos, con una amplia gama de características de seguridad que satisfacen muchos de los requisitos de cumplimiento tanto normativos como organizativos. Puede proteger fácilmente los datos controlando el acceso físico a los mismos y con diversas opciones de seguridad en el archivo (nivel de fila, de columna o de archivo) con el cifrado de datos transparente, el cifrado de nivel de celda o la seguridad de nivel de fila. Always Encrypted también permite operaciones en los datos cifrados, simplificando el proceso de actualizaciones de las aplicaciones. A su vez, el acceso a los registros auditoría de la actividad de SQL Database le proporciona la información que necesita, lo que le permite saber cómo y cuándo se tiene acceso a datos.

## <a name="next-steps"></a>Pasos siguientes
Con unos pocos pasos sencillos puede mejorar la protección de su base de datos contra usuarios malintencionados o acceso no autorizado. En este tutorial, aprenderá a:

- Configurar [reglas de firewall](../../azure-sql/database/firewall-configure.md) para un servidor o una base de datos.
- Proteger los datos con [cifrado](/sql/relational-databases/security/encryption/sql-server-encryption).
- Habilitar la [auditoría de SQL Database](../../azure-sql/database/auditing-overview.md).
