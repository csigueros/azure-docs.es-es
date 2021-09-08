---
title: Aplicaciones de ejemplo de entorno local de MySQL a Azure Database for MySQL
description: Descargue la documentación adicional que hemos creado para esta Guía de migración y conozca los pasos de configuración.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 27f4ded8e1378ccceeb11409e9c393fbc46c92aa
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084559"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-sample-applications"></a>Migración de aplicaciones de ejemplo de un entorno local de MySQL a Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>Información general

En este artículo se indica cómo implementar una aplicación de ejemplo con una guía de migración completa de MySQL y cómo revisar los parámetros de servidor disponibles.

## <a name="environment-setup"></a>Configuración del entorno

[Descargue la documentación adicional](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf) que hemos creado para esta guía de migración y aprenda a configurar un entorno para realizar los pasos de migración de la guía para la [aplicación de demostración de conferencias](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app) de ejemplo.

## <a name="azure-resource-manager-arm-templates"></a>Plantillas de Azure Resource Manager (ARM)

### <a name="secure"></a>Seguridad

La plantilla de ARM implementa todos los recursos con puntos de conexión privados. La plantilla de ARM elimina de forma eficaz cualquier acceso a los servicios PaaS desde Internet.

[Plantilla de ARM segura](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>No segura

Esta plantilla de ARM implementará recursos mediante la implementación estándar, en la que todos los recursos están disponibles desde Internet.

[Plantilla de ARM no segura](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>Parámetros de servidor predeterminados de MySQL 5.5 y Azure Database for MySQL

Puede encontrar la [lista completa de parámetros de servidor predeterminados de MySQL 5.5 y Azure Database for MySQL](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf) en nuestro repositorio de GitHub.